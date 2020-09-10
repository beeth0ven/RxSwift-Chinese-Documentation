# 为什么要使用 RxSwift ？

我们先看一下 RxSwift 能够帮助我们做些什么：

## Target Action

传统实现方法：

```swift
button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
```

```swift
func buttonTapped() {
    print("button Tapped")
}
```

通过 Rx 来实现：

```swift
button.rx.tap
    .subscribe(onNext: {
        print("button Tapped")
    })
    .disposed(by: disposeBag)
```

你不需要使用 Target Action，这样使得代码逻辑清晰可见。


## 代理

传统实现方法：

```swift
class ViewController: UIViewController {
    ...
    override func viewDidLoad() {
        super.viewDidLoad()
        scrollView.delegate = self
    }
}

extension ViewController: UIScrollViewDelegate {
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        print("contentOffset: \(scrollView.contentOffset)")
    }
}
```

通过 Rx 来实现：

```swift
class ViewController: UIViewController {
    ...
    override func viewDidLoad() {
        super.viewDidLoad()

        scrollView.rx.contentOffset
            .subscribe(onNext: { contentOffset in
                print("contentOffset: \(contentOffset)")
            })
            .disposed(by: disposeBag)
    }
}
```

你不需要书写代理的配置代码，就能获得想要的结果。

## 闭包回调

传统实现方法：

```swift
URLSession.shared.dataTask(with: URLRequest(url: url)) {
    (data, response, error) in
    guard error == nil else {
        print("Data Task Error: \(error!)")
        return
    }

    guard let data = data else {
        print("Data Task Error: unknown")
        return
    }

    print("Data Task Success with count: \(data.count)")
}.resume()
```

通过 Rx 来实现：

```swift
URLSession.shared.rx.data(request: URLRequest(url: url))
    .subscribe(onNext: { data in
        print("Data Task Success with count: \(data.count)")
    }, onError: { error in
        print("Data Task Error: \(error)")
    })
    .disposed(by: disposeBag)
```

回调也变得十分简单

## 通知

传统实现方法：

```swift
var ntfObserver: NSObjectProtocol!

override func viewDidLoad() {
    super.viewDidLoad()

    ntfObserver = NotificationCenter.default.addObserver(
          forName: .UIApplicationWillEnterForeground,
          object: nil, queue: nil) { (notification) in
        print("Application Will Enter Foreground")
    }
}

deinit {
    NotificationCenter.default.removeObserver(ntfObserver)
}
```

通过 Rx 来实现：

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    NotificationCenter.default.rx
        .notification(.UIApplicationWillEnterForeground)
        .subscribe(onNext: { (notification) in
            print("Application Will Enter Foreground")
        })
        .disposed(by: disposeBag)
}
```

你不需要去管理观察者的生命周期，这样你就有更多精力去关注业务逻辑。

## 多个任务之间有依赖关系

例如，先通过用户名密码取得 Token 然后通过 Token 取得用户信息，

传统实现方法：

```swift
/// 用回调的方式封装接口
enum API {

    /// 通过用户名密码取得一个 token
    static func token(username: String, password: String,
        success: (String) -> Void,
        failure: (Error) -> Void) { ... }

    /// 通过 token 取得用户信息
    static func userinfo(token: String,
        success: (UserInfo) -> Void,
        failure: (Error) -> Void) { ... }
}
```

```swift
/// 通过用户名和密码获取用户信息
API.token(username: "beeth0ven", password: "987654321",
    success: { token in
        API.userInfo(token: token,
            success: { userInfo in
                print("获取用户信息成功: \(userInfo)")
            },
            failure: { error in
                print("获取用户信息失败: \(error)")
        })
    },
    failure: { error in
        print("获取用户信息失败: \(error)")
})
```

通过 Rx 来实现：

```swift
/// 用 Rx 封装接口
enum API {

    /// 通过用户名密码取得一个 token
    static func token(username: String, password: String) -> Observable<String> { ... }

    /// 通过 token 取得用户信息
    static func userInfo(token: String) -> Observable<UserInfo> { ... }
}
```

```swift
/// 通过用户名和密码获取用户信息
API.token(username: "beeth0ven", password: "987654321")
    .flatMapLatest(API.userInfo)
    .subscribe(onNext: { userInfo in
        print("获取用户信息成功: \(userInfo)")
    }, onError: { error in
        print("获取用户信息失败: \(error)")
    })
    .disposed(by: disposeBag)
```

这样你可以[避免回调地狱](https://en.wiktionary.org/wiki/callback_hell)，从而使得代码易读，易维护。

## 等待多个并发任务完成后处理结果

例如，需要将两个网络请求合并成一个，

通过 Rx 来实现：

```swift
/// 用 Rx 封装接口
enum API {

    /// 取得老师的详细信息
    static func teacher(teacherId: Int) -> Observable<Teacher> { ... }

    /// 取得老师的评论
    static func teacherComments(teacherId: Int) -> Observable<[Comment]> { ... }
}
```

```swift
/// 同时取得老师信息和老师评论
Observable.zip(
      API.teacher(teacherId: teacherId),
      API.teacherComments(teacherId: teacherId)
    ).subscribe(onNext: { (teacher, comments) in
        print("获取老师信息成功: \(teacher)")
        print("获取老师评论成功: \(comments.count) 条")
    }, onError: { error in
        print("获取老师信息或评论失败: \(error)")
    })
    .disposed(by: disposeBag)
```

这样你可用寥寥几行代码来完成相当复杂的异步操作。

---

### 那么为什么要使用 RxSwift ？
* 复合 - Rx 就是复合的代名词
* 复用 - 因为它易复合
* 清晰 - 因为声明都是不可变更的
* 易用 - 因为它抽象了异步编程，使我们统一了代码风格
* 稳定 - 因为 Rx 是完全通过单元测试的
