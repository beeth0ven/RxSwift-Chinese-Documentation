## Github Search（示例）

<img src="/assets/Architecture/RxFeedback/GithubPaginatedSearchFull.gif" width="320px" />


这个例子是我们经常会遇见的**Github 搜索**。它是使用 [RxFeedback](https://github.com/kzaher/RxFeedback) 重构以后的版本，你可以在这里下载[这个例子](https://github.com/kzaher/RxFeedback/blob/master/Examples/GithubPaginatedSearch.swift)。

### 简介

这个 App 主要有这样几个交互：

* 输入搜索关键字，显示搜索结果
* 当请求时产生错误，就给出错误提示
* 当用户滑动列表到底部时，加载下一页

### State

![](/assets/Architecture/RxFeedback/State.png)

这个是用于描述当前状态：

```swift
fileprivate struct State {
    var search: String {
        didSet { ... }
    }
    var nextPageURL: URL?
    var shouldLoadNextPage: Bool
    var results: [Repository]
    var lastError: GitHubServiceError?
}

...

extension State {
    var loadNextPage: URL? { return ... }
}
```

我们这个例子（Github 搜索） 就有这样几个状态：

* search 搜索关键字
* nextPageURL 下一页的 URL
* shouldLoadNextPage 是否可以加载下一页
* results 搜索结果
* lastError 搜索时产生的错误
* loadNextPage 加载下一页的触发

我们通常会使用这些状态来控制页面布局。

或者，用被请求的状态，触发另外一个事件。

---

### Event

![](/assets/Architecture/RxFeedback/Event.png)

这个是用于描述所产生的事件：

```swift
fileprivate enum Event {
    case searchChanged(String)
    case response(SearchRepositoriesResponse)
    case startLoadingNextPage
}
```

**事件**通常会使**状态**发生变化，然后产生一个新的**状态**：

```swift
extension State {
    ...
    static func reduce(state: State, event: Event) -> State {
        switch event {
        case .searchChanged(let search):
            var result = state
            result.search = search
            result.results = []
            return result
        case .startLoadingNextPage:
            var result = state
            result.shouldLoadNextPage = true
            return result
        case .response(.success(let response)):
            var result = state
            result.results += response.repositories
            result.shouldLoadNextPage = false
            result.nextPageURL = response.nextURL
            result.lastError = nil
            return result
        case .response(.failure(let error)):
            var result = state
            result.shouldLoadNextPage = false
            result.lastError = error
            return result
        }
    }
}
```

当发生某个**事件**时，更新当前**状态**：

* searchChanged 搜索关键字变更

  将搜索关键字更新成当前值，并且清空搜索结果。

* startLoadingNextPage 触发加载下页

  允许加载下一页，如果下一页的 URL 存在，就加载下一页。

* response(.success(...)) 搜索结果返回成功

  将搜索结果加入到对应的数组里面去，然后将相关状态更新。

* response(.failure(...)) 搜索结果返回失败

  保存错误状态。

---

### Feedback Loop

![](/assets/Architecture/RxFeedback/FeedbackLoop.png)

**Feedback Loop** 是用来引入[附加作用]的。

例如，你可以将**状态**输出到 UI 页面上，或者将 UI **事件**输入到反馈循环里面去:

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    ...

    Driver.system(
        initialState: State.empty,
        reduce: State.reduce,
        feedback:
        // UI, user feedback
        UI.bind(self) { me, state in
            let subscriptions = [
                state.map { $0.search }.drive(me.searchText!.rx.text),
                state.map { $0.lastError?.displayMessage }.drive(me.status!.rx.textOrHide),
                state.map { $0.results }.drive(searchResults.rx.items(cellIdentifier: "repo"))(configureRepository),
                state.map { $0.loadNextPage?.description }.drive(me.loadNextPage!.rx.textOrHide),
                ]
            let events = [
                me.searchText!.rx.text.orEmpty.changed.asDriver().map(Event.searchChanged),
                triggerLoadNextPage(state)
            ]
            return UI.Bindings(subscriptions: subscriptions, events: events)
        },
        // NoUI, automatic feedback
        ...
        )
        .drive()
        .disposed(by: disposeBag)
}
```

这里定义的 `subscriptions` 就是如何将**状态**输出到 UI 页面上，而 `events` 则是如何将 UI **事件**输入到反馈循环里面去。

---

### 被请求的状态

![](/assets/Architecture/RxFeedback/QueriedState.png)

**被请求的状态**是，用于发出异步请求，以**事件**的形式返回结果。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    ...

    Driver.system(
        initialState: State.empty,
        reduce: State.reduce,
        feedback:
        // UI, user feedback
        ... ,
        // NoUI, automatic feedback
        react(query: { $0.loadNextPage }, effects: { resource in
            return URLSession.shared.loadRepositories(resource: resource)
                .asDriver(onErrorJustReturn: .failure(.offline))
                .map(Event.response)
        })
        )
        .drive()
        .disposed(by: disposeBag)
}
```

这里 `loadNextPage` 就是**被请求的状态**，当状态 `loadNextPage` 不为 nil 时，就请求加载下一页。

---

### 整体结构

![](/assets/Architecture/RxFeedback/All.png)

现在我们看一下这个例子整体结构，这样可以帮助你理解这种架构。然后，以下是核心代码：

```swift
...
fileprivate struct State {
    var search: String {
        didSet {
            if search.isEmpty {
                self.nextPageURL = nil
                self.shouldLoadNextPage = false
                self.results = []
                self.lastError = nil
                return
            }
            self.nextPageURL = URL(string: "https://api.github.com/search/repositories?q=\(search.URLEscaped)")
            self.shouldLoadNextPage = true
            self.lastError = nil
        }
    }

    var nextPageURL: URL?
    var shouldLoadNextPage: Bool
    var results: [Repository]
    var lastError: GitHubServiceError?
}

fileprivate enum Event {
    case searchChanged(String)
    case response(SearchRepositoriesResponse)
    case startLoadingNextPage
}

// transitions
extension State {
    static var empty: State {
        return State(search: "", nextPageURL: nil, shouldLoadNextPage: true, results: [], lastError: nil)
    }
    static func reduce(state: State, event: Event) -> State {
        switch event {
        case .searchChanged(let search):
            var result = state
            result.search = search
            result.results = []
            return result
        case .startLoadingNextPage:
            var result = state
            result.shouldLoadNextPage = true
            return result
        case .response(.success(let response)):
            var result = state
            result.results += response.repositories
            result.shouldLoadNextPage = false
            result.nextPageURL = response.nextURL
            result.lastError = nil
            return result
        case .response(.failure(let error)):
            var result = state
            result.shouldLoadNextPage = false
            result.lastError = error
            return result
        }
    }
}

// queries
extension State {
    var loadNextPage: URL? {
        return self.shouldLoadNextPage ? self.nextPageURL : nil
    }
}

class GithubPaginatedSearchViewController: UIViewController {
    @IBOutlet weak var searchText: UISearchBar?
    @IBOutlet weak var searchResults: UITableView?
    @IBOutlet weak var status: UILabel?
    @IBOutlet weak var loadNextPage: UILabel?

    private let disposeBag = DisposeBag()

    override func viewDidLoad() {
        super.viewDidLoad()

        let searchResults = self.searchResults!

        searchResults.register(UITableViewCell.self, forCellReuseIdentifier: "repo")

        let triggerLoadNextPage: (Driver<State>) -> Driver<Event> = { state in
            return state.flatMapLatest { state -> Driver<Event> in
                if state.shouldLoadNextPage {
                    return Driver.empty()
                }

                return searchResults.rx.nearBottom.map { _ in Event.startLoadingNextPage }
            }
        }

        func configureRepository(_: Int, repo: Repository, cell: UITableViewCell) {
            cell.textLabel?.text = repo.name
            cell.detailTextLabel?.text = repo.url.description
        }

        let bindUI: (Driver<State>) -> Driver<Event> = UI.bind(self) { me, state in
            let subscriptions = [
                state.map { $0.search }.drive(me.searchText!.rx.text),
                state.map { $0.lastError?.displayMessage }.drive(me.status!.rx.textOrHide),
                state.map { $0.results }.drive(searchResults.rx.items(cellIdentifier: "repo"))(configureRepository),
                state.map { $0.loadNextPage?.description }.drive(me.loadNextPage!.rx.textOrHide),
                ]
            let events = [
                me.searchText!.rx.text.orEmpty.changed.asDriver().map(Event.searchChanged),
                triggerLoadNextPage(state)
            ]
            return UI.Bindings(subscriptions: subscriptions, events: events)
        }

        Driver.system(
            initialState: State.empty,
            reduce: State.reduce,
            feedback:
            // UI, user feedback
            bindUI,
            // NoUI, automatic feedback
            react(query: { $0.loadNextPage }, effects: { resource in
                return URLSession.shared.loadRepositories(resource: resource)
                    .asDriver(onErrorJustReturn: .failure(.offline))
                    .map(Event.response)
            })
            )
            .drive()
            .disposed(by: disposeBag)
    }
}
...
```

这是使用 [RxFeedback](https://github.com/kzaher/RxFeedback) 重构以后的 **Github Search**。你可以对比一下使用 [ReactorKit](/content/architecture/reactorkit.md) 重构以后的 [Github Search](/content/architecture/reactorkit/github_search.md) 两者有许多相似之处。


[附加作用]:/content/recipes/side_effects.md
