## Github Search（示例）

<img src="/assets/Architecture/ReactorKit/GithubPaginatedSearchFull.gif" width="320px" />

我们还是使用**Github 搜索**来演示如何使用 [ReactorKit]。这个例子是使用 [ReactorKit] 重构以后的版本，你可以在这里下载[这个例子](https://github.com/ReactorKit/ReactorKit/tree/master/Examples/GitHubSearch)。

### 简介

这个 App 主要有这样几个交互：

* 输入搜索关键字，显示搜索结果
* 当用户滑动列表到底部时，加载下一页
* 当用户点击某一条搜索结果是，用 Safari 打开链接

---

### Action

**Action** 用于描叙用户行为：

```swift
enum Action {
  case updateQuery(String?)
  case loadNextPage
}
```

* updateQuery 搜索关键字变更
* loadNextPage 触发加载下页

---

### Mutation

**Mutation** 用于描状态变更：

```swift
enum Mutation {
  case setQuery(String?)
  case setRepos([String], nextPage: Int?)
  case appendRepos([String], nextPage: Int?)
  case setLoadingNextPage(Bool)
}
```

* setQuery 更新搜索关键字
* setRepos 更新搜索结果
* appendRepos 添加搜索结果
* setLoadingNextPage 设置是否正在加载下一页

---

### State

这个是用于描述当前状态：

```swift
struct State {
  var query: String?
  var repos: [String] = []
  var nextPage: Int?
  var isLoadingNextPage: Bool = false
}
```

* query 搜索关键字
* repos 搜索结果
* nextPage 下一页页数
* isLoadingNextPage 是否正在加载下一页

我们通常会使用这些状态来控制页面布局。

---

### mutate()

将 **Action** 转换为 **Mutation**：

```swift
func mutate(action: Action) -> Observable<Mutation> {
    switch action {
    case let .updateQuery(query):
      return Observable.concat([
        // 1) set current state's query (.setQuery)
        Observable.just(Mutation.setQuery(query)),

        // 2) call API and set repos (.setRepos)
        self.search(query: query, page: 1)
          // cancel previous request when the new `.updateQuery` action is fired
          .takeUntil(self.action.filter(isUpdateQueryAction))
          .map { Mutation.setRepos($0, nextPage: $1) },
      ])

    case .loadNextPage:
      guard !self.currentState.isLoadingNextPage else { return Observable.empty() } // prevent from multiple requests
      guard let page = self.currentState.nextPage else { return Observable.empty() }
      return Observable.concat([
        // 1) set loading status to true
        Observable.just(Mutation.setLoadingNextPage(true)),

        // 2) call API and append repos
        self.search(query: self.currentState.query, page: page)
          .takeUntil(self.action.filter(isUpdateQueryAction))
          .map { Mutation.appendRepos($0, nextPage: $1) },

        // 3) set loading status to false
        Observable.just(Mutation.setLoadingNextPage(false)),
      ])
    }
  }
```

* 当用户输入一个新的搜索关键字时，就从服务器请求 `repos`，然后转换成**更新** `repos` 事件（Mutation）。
* 当用户触发加载下页时，就从服务器请求 `repos`，然后转换成**添加** `repos` 事件。

---

### reduce()

`reduce()` 通过旧的 **State** 以及 **Mutation** 创建一个新的 **State**：

```swift
func reduce(state: State, mutation: Mutation) -> State {
  switch mutation {
  case let .setQuery(query):
    var newState = state
    newState.query = query
    return newState

  case let .setRepos(repos, nextPage):
    var newState = state
    newState.repos = repos
    newState.nextPage = nextPage
    return newState

  case let .appendRepos(repos, nextPage):
    var newState = state
    newState.repos.append(contentsOf: repos)
    newState.nextPage = nextPage
    return newState

  case let .setLoadingNextPage(isLoadingNextPage):
    var newState = state
    newState.isLoadingNextPage = isLoadingNextPage
    return newState
  }
}
```

* setQuery 更新搜索关键字
* setRepos 更新搜索结果，以及下一页页数
* appendRepos 添加搜索结果，以及下一页页数
* setLoadingNextPage 设置是否正在加载下一页

---

### bind(reactor:)

在 **View** 层进行用户输入绑定和状态输出绑定：

```swift
func bind(reactor: GitHubSearchViewReactor) {
  // Action
  searchBar.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .map { Reactor.Action.updateQuery($0) }
    .bind(to: reactor.action)
    .disposed(by: disposeBag)

  tableView.rx.contentOffset
    .filter { [weak self] offset in
      guard let `self` = self else { return false }
      guard self.tableView.frame.height > 0 else { return false }
      return offset.y + self.tableView.frame.height >= self.tableView.contentSize.height - 100
    }
    .map { _ in Reactor.Action.loadNextPage }
    .bind(to: reactor.action)
    .disposed(by: disposeBag)

  // State
  reactor.state.map { $0.repos }
    .bind(to: tableView.rx.items(cellIdentifier: "cell")) { indexPath, repo, cell in
      cell.textLabel?.text = repo
    }
    .disposed(by: disposeBag)

  // View
  tableView.rx.itemSelected
    .subscribe(onNext: { [weak self, weak reactor] indexPath in
      guard let `self` = self else { return }
      self.tableView.deselectRow(at: indexPath, animated: false)
      guard let repo = reactor?.currentState.repos[indexPath.row] else { return }
      guard let url = URL(string: "https://github.com/\(repo)") else { return }
      let viewController = SFSafariViewController(url: url)
      self.present(viewController, animated: true, completion: nil)
    })
    .disposed(by: disposeBag)
}
```

* 将用户更改输入关键字行为绑定到用户行为上
* 将用户要求加载下一页行为绑定到用户行为上
* 将搜索结果输出到列表页上
* 当用户点击某一条搜索结果是，用 Safari 打开链接

---

### 整体结构

我们已经了解 [ReactorKit] 每一个组件的功能了，现在我们看一下完整的核心代码：

**GitHubSearchViewReactor.swift**

```swift
final class GitHubSearchViewReactor: Reactor {
  enum Action {
    case updateQuery(String?)
    case loadNextPage
  }

  enum Mutation {
    case setQuery(String?)
    case setRepos([String], nextPage: Int?)
    case appendRepos([String], nextPage: Int?)
    case setLoadingNextPage(Bool)
  }

  struct State {
    var query: String?
    var repos: [String] = []
    var nextPage: Int?
    var isLoadingNextPage: Bool = false
  }

  let initialState = State()

  func mutate(action: Action) -> Observable<Mutation> {
    switch action {
    case let .updateQuery(query):
      return Observable.concat([
        // 1) set current state's query (.setQuery)
        Observable.just(Mutation.setQuery(query)),

        // 2) call API and set repos (.setRepos)
        self.search(query: query, page: 1)
          // cancel previous request when the new `.updateQuery` action is fired
          .takeUntil(self.action.filter(isUpdateQueryAction))
          .map { Mutation.setRepos($0, nextPage: $1) },
      ])

    case .loadNextPage:
      guard !self.currentState.isLoadingNextPage else { return Observable.empty() } // prevent from multiple requests
      guard let page = self.currentState.nextPage else { return Observable.empty() }
      return Observable.concat([
        // 1) set loading status to true
        Observable.just(Mutation.setLoadingNextPage(true)),

        // 2) call API and append repos
        self.search(query: self.currentState.query, page: page)
          .takeUntil(self.action.filter(isUpdateQueryAction))
          .map { Mutation.appendRepos($0, nextPage: $1) },

        // 3) set loading status to false
        Observable.just(Mutation.setLoadingNextPage(false)),
      ])
    }
  }

  func reduce(state: State, mutation: Mutation) -> State {
    switch mutation {
    case let .setQuery(query):
      var newState = state
      newState.query = query
      return newState

    case let .setRepos(repos, nextPage):
      var newState = state
      newState.repos = repos
      newState.nextPage = nextPage
      return newState

    case let .appendRepos(repos, nextPage):
      var newState = state
      newState.repos.append(contentsOf: repos)
      newState.nextPage = nextPage
      return newState

    case let .setLoadingNextPage(isLoadingNextPage):
      var newState = state
      newState.isLoadingNextPage = isLoadingNextPage
      return newState
    }
  }

  ...
}
```

**GitHubSearchViewController.swift**

```swift
class GitHubSearchViewController: UIViewController, View {
  @IBOutlet var searchBar: UISearchBar!
  @IBOutlet var tableView: UITableView!

  var disposeBag = DisposeBag()

  override func viewDidLoad() {
    super.viewDidLoad()
    tableView.contentInset.top = 44 // search bar height
    tableView.scrollIndicatorInsets.top = tableView.contentInset.top
  }

  func bind(reactor: GitHubSearchViewReactor) {
    // Action
    searchBar.rx.text
      .throttle(0.3, scheduler: MainScheduler.instance)
      .map { Reactor.Action.updateQuery($0) }
      .bind(to: reactor.action)
      .disposed(by: disposeBag)

    tableView.rx.contentOffset
      .filter { [weak self] offset in
        guard let `self` = self else { return false }
        guard self.tableView.frame.height > 0 else { return false }
        return offset.y + self.tableView.frame.height >= self.tableView.contentSize.height - 100
      }
      .map { _ in Reactor.Action.loadNextPage }
      .bind(to: reactor.action)
      .disposed(by: disposeBag)

    // State
    reactor.state.map { $0.repos }
      .bind(to: tableView.rx.items(cellIdentifier: "cell")) { indexPath, repo, cell in
        cell.textLabel?.text = repo
      }
      .disposed(by: disposeBag)

    // View
    tableView.rx.itemSelected
      .subscribe(onNext: { [weak self, weak reactor] indexPath in
        guard let `self` = self else { return }
        self.tableView.deselectRow(at: indexPath, animated: false)
        guard let repo = reactor?.currentState.repos[indexPath.row] else { return }
        guard let url = URL(string: "https://github.com/\(repo)") else { return }
        let viewController = SFSafariViewController(url: url)
        self.present(viewController, animated: true, completion: nil)
      })
      .disposed(by: disposeBag)
  }
}
```

这是使用 [ReactorKit] 重构以后的 Github Search。[ReactorKit] 分层非常详细，分工也是非常明确的。当你在处理大型应用程序时，这可以帮助你更好的管理代码。

[ReactorKit]:https://github.com/ReactorKit/ReactorKit
