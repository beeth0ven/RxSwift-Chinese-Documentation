
# 纯函数

### 什么是纯函数？

在函数式编程里我们会经常谈到这两个概念。一个是 [纯函数]。另一个是 [附加作用]（副作用）。这里我们就结合实际来介绍一下 [纯函数] 和 [附加作用]。

下面我们给出两个函数 `increaseA` 和 `increaseB`，他们其中一个是 [纯函数]，另一个不是 [纯函数]：


```swift
var state = 0

func increaseA() {
    state += 1
}

increaseA()

print(state) // 结果： 1
```

```swift
func increaseB(state: Int) -> Int {
    return state + 1
}

let state = increaseB(state: 0)

print(state) // 结果： 1
```

他们的作用差不多，使 `state + 1`， 我们可以猜测一下 `increaseA` 和 `increaseB` 哪一个是 [纯函数]？

...


...


...

... 经过 10 秒后


**现在公布答案：**`increaseB` 是 [纯函数]，`increaseA` 不是 [纯函数]

为什么 `increaseB` 是 [纯函数]？

因为他特别 **纯洁**：除了用入参 `state` 计算返回值以外没做任何其他的事情。

那为什么 `increaseA` 不是 [纯函数]？

因为他修改了函数本体以外的值 `state`, 他拥有这个 [附加作用]，因此他 **并不纯洁** 就不是 [纯函数]。

我们再来做以下两个测试，然后猜测他们能不能测试成功：

```swift
func testIncreaseA() {
    increaseA()
    state == 1 // 结果：?? 🤔
}
```

```swift
func testIncreaseB() {
    let state = increaseB(state: 0)
    state == 1 // 结果：true 😎
}
```

...


...


...

... 经过 20 秒后

嗯... 这里我们可以肯定第二个测试 `testIncreaseB` 会成功。`0 + 1` 肯定等于 `1`。那第一个测试呢？这可不好说了，我们并不知道  `increaseA` 是在什么环境下被调用的，不知道在这个环境下初始 `state` 是多少。如果他是 `0` 那测试就会成功的，如果他不是 `0` 那测试就会失败的。因此在不知道所处环境时，我们无法判断测试是否会成功。

由于 `increaseA` 存在修改外部 `state` 的 [附加作用] 所以他不是 [纯函数]。事实上如果函数有以下任意一种作用，他也不是纯函数：

* 发起网络请求
* 刷新 UI
* 读写数据库
* 获取位置信息
* 使用蓝牙模块
* 打印输出
* ...

我们将这些作用称为函数的 [附加作用]（副作用）。

而 [纯函数] 的定义就是： **没有 [附加作用] 的函数，并且在参数相同时，返回值也一定相同。**

因此在已知执行逻辑时，[纯函数] 所产生的结果是**可以被预测的**。一些现代化的库都利用了这个特性来做状态管理，如：[RxFeedback]， [Redux]，[ReactorKit] 等等。

### 纯函数用于状态管理

我们用一个足够简单的例子来演示，如何用 [纯函数] 做状态管理：

```swift
typealias State = Int

enum Event {
    case increase
    case decrease
}

func reduce(_ state: State, event: Event) -> State {
    switch event {
    case .increase:
        return state + 1
    case .decrease:
        return state - 1
    }
}
```

这个例子似乎过于简单，以至于我们看不出他有什么特别的。好吧，我承认他的主要目的是向大家演示，用 [纯函数] 做状态管理的**基本单元是什么**。

首先，我们得有个状态：

```swift
typealias State = Int
```

然后，我们要有各种事件：

```swift
enum Event {
    case increase
    case decrease
}
```

最后，我们要有一个 [纯函数] 来管理我们的状态：

```swift
func reduce(_ state: State, event: Event) -> State {
    switch event {
    case .increase:
        return state + 1
    case .decrease:
        return state - 1
    }
}
```

这样，我们就可以做测试了，当 `App` 处于某个状态时，发生了某个事件，会产生一个结果，这个结果是否符合我们的预期：

```swift
func testReduce() {
    
    let state1 = reduce(0, event: .increase)
    state1 == 1 // 结果：true 😎

    let state2 = reduce(10, event: .decrease)
    state2 == 9 // 结果：true 😎
}
```

以上两个测试都是成功的。当然这里的状态管理过于简单。而真实应用程序的状态都是非常复杂的。并且程序的行为都是很难预测的。要解决这个问题，我们要感谢 [纯函数]，还记得他的特征吗？

**[纯函数] 在参数相同时，返回值也一定相同。**

我们再来看下 `reduce` 方法：
```swift
func reduce(_ state: State, event: Event) -> State { ... }
```

我们有没有获得一点点灵感...

...


...


...


...


...

... 经过 60 秒后

希望你已经获得答案了。

当程序处于某个特定状态时，发生了某个特定事件，会产生某个**唯一的结果**。这个结果与**所处的环境无关**，不论是处于应用程序运行环境，还是在测试环境。这个结果只和**初始状态**以及**发生的事件**有关。因此，程序的行为是**可以被预测的**，而且程序运行时的状态更新，可以在测试环境中被模拟出来。

...


...


...


...


...

... 经过 60 秒后

现在，我们来看一个相对复杂的例子：

### 登录状态管理

```swift
typealias UserID = String

enum LoginError: Error, Equatable {
    case usernamePasswordMismatch
    case offline
}

struct State: Equatable {

    var username: String
    var password: String

    var loading: Bool
    var data: UserID?
    var error: LoginError?

    enum Event {
        case onUpateUsername(String)
        case onUpatePassword(String)
        case onTriggerLogin
        case onLoginSuccess(UserID)
        case onLoginError(LoginError)
    }

    static func reduce(_ state: State, event: Event) -> State {
        var newState = state
        switch event {
        case .onUpateUsername(let username):
            newState.username = username
        case .onUpatePassword(let password):
            newState.password = password
        case .onTriggerLogin:
            newState.loading = true
            newState.data = nil
            newState.error = nil
        case .onLoginSuccess(let userId):
            newState.loading = false
            newState.data = userId
        case .onLoginError(let error):
            newState.loading = false
            newState.error = error
        }
        return newState
    }
}
```

我们重新走下流程 😄，用 [纯函数] 做状态管理：

首先，我们得有个状态：

```swift
struct State: Equatable {

    var username: String    // 输入的用户名
    var password: String    // 输入的密码

    var loading: Bool       // 登录中
    var data: UserID?       // 登录成功
    var error: LoginError?  // 登录失败

    ...
}
```

然后，我们要有各种事件：

```swift
    enum Event {
        case onUpateUsername(String)    //  更新用户名
        case onUpatePassword(String)    //  更新密码
        case onTriggerLogin             //  触发登录
        case onLoginSuccess(UserID)     //  登录成功
        case onLoginError(LoginError)   //  登录失败
    }
```

最后，我们要有一个 [纯函数] 来管理我们的状态：

```swift
    static func reduce(_ state: State, event: Event) -> State {
        var newState = state
        switch event {
        case .onUpateUsername(let username):
            newState.username = username
        case .onUpatePassword(let password):
            newState.password = password
        case .onTriggerLogin:
            newState.loading = true
            newState.data = nil
            newState.error = nil
        case .onLoginSuccess(let userId):
            newState.loading = false
            newState.data = userId
        case .onLoginError(let error):
            newState.loading = false
            newState.error = error
        }
        return newState
    }
```

现在我们可以在测试环境模拟各种事件，并且判断结果是否符合预期：

* 更新用户名事件

```swift
func testOnUpateUsername() {

    let state = State(
        username: "",
        password: "",
        loading: false,
        data: nil,
        error: nil
    )

    let newState = State.reduce(state, event: .onUpateUsername("beeth0ven"))

    let expect = State(
        username: "beeth0ven",
        password: "",
        loading: false,
        data: nil,
        error: nil
    )

    newState == expect // 结果：true 😎
}
```

* 更新密码事件

```swift
func testOnUpatePassword() {
    
    let state = State(
        username: "beeth0ven",
        password: "",
        loading: false,
        data: nil,
        error: nil
    )
    
    let newState = State.reduce(state, event: .onUpatePassword("123456"))
    
    let expect = State(
        username: "beeth0ven",
        password: "123456",
        loading: false,
        data: nil,
        error: nil
    )
    
    newState == expect // 结果：true 😎
}
```

* 触发登录事件

```swift
func testOnTriggerLogin() {

    let state = State(
        username: "beeth0ven",
        password: "123456",
        loading: false,
        data: nil,
        error: nil
    )

    let newState = State.reduce(state, event: .onTriggerLogin)

    let expect = State(
        username: "beeth0ven",
        password: "123456",
        loading: true,
        data: nil,
        error: nil
    )

    newState == expect // 结果：true 😎
}
```

* 登录成功事件

```swift
func testOnLoginSuccess() {

    let state = State(
        username: "beeth0ven",
        password: "123456",
        loading: true,
        data: nil,
        error: nil
    )
    
    let newState = State.reduce(state, event: .onLoginSuccess("userID007"))
    
    let expect = State(
        username: "beeth0ven",
        password: "123456",
        loading: false,
        data: "userID007",
        error: nil
    )
    
    newState == expect // 结果：true 😎
}
```

* 登录失败事件

```swift
func testOnLoginError() {
    
    let state = State(
        username: "beeth0ven",
        password: "123456",
        loading: true,
        data: nil,
        error: nil
    )
    
    let newState = State.reduce(state, event: .onLoginError(.usernamePasswordMismatch))
    
    let expect = State(
        username: "beeth0ven",
        password: "123456",
        loading: false,
        data: nil,
        error: .usernamePasswordMismatch
    )
    
    newState == expect // 结果：true 😎
}
```

这样我们可以轻易掌控程序的运行状态，以及各种状态更新。

现在，我们知道如何用 [纯函数] 做状态管理了。不过当前的代码形态，离投入生产环境，还存在好几个过度形态。这些过度形态有的是围绕如何引入 [附加作用]，而做了一些应用架构。在这个问题上，不同地架构也提出了不同的解决方案，如：[RxFeedback] 用 `feedbackLoop` 引入 [附加作用]，[Redux] 用 `middleware` 引入 [附加作用] 等等。这里就不一一介绍了，这些库的官方网站都会有相关说明。

最后，我们还是将代码演化到下一个形态，这里我选择使用 [Redux] 流派。因为个人的觉得他的知识依赖要少一些，可以让更多读者从中获益。

### 下一步 -- 引入 Store

```swift
class Store {

    // 观察者，用于响应状态更新，第一个 State? 为旧状态，第二个 State 为当前状态
    typealias Observer = (State?, State) -> Void
    
    private(set) var state: State  // 当前状态
    private var _observers: [UUID: Observer]  // 所有的观察者
    
    // 初始化
    init(initailState: State) {
        self.state = initailState
        self._observers = [:]
    }
    
    // 发出事件
    func dispatch(event: State.Event) {
        let oldState = self.state
        self.state = State.reduce(self.state, event: event)
        _publish(oldState: oldState, newState: self.state)
    }
    
    // 订阅状态更新
    func subscribe(observer: @escaping Observer) -> UUID {
        let subscriptionID = UUID() //  UUID 是唯一标识符，该 id 可用于取消订阅
        _observers[subscriptionID] = observer
        observer(nil, self.state) // 订阅时，将当前状态回放给该观察者
        return subscriptionID
    }
    
    // 取消订阅
    func unsubscribe(_ subscriptionID: UUID) {
        _observers.removeValue(forKey: subscriptionID)
    }
    
    // 私有方法，通知所有的观察者，状态已经更新了
    private func _publish(oldState: State?, newState: State) {
        _observers.values.forEach { observer in
            observer(oldState, newState)
        }
    }
}

```

如何使用 `Store`:

```swift
func useStore() {
    
    let initailState = State(
        username: "",
        password: "",
        loading: false,
        data: nil,
        error: nil
    )
    
    let store = Store(initailState: initailState)
    
    // 以下变量 newStates 和 oldStates 用于录制状态历史
    var newStates: [State] = []
    var oldStates: [State?] = []
    
    let subscriptionID = store.subscribe { (oldState, newState) in
        newStates.append(newState)
        oldStates.append(oldState)
    }
    
    // 模拟真实事件
    store.dispatch(event: .onUpateUsername("beeth0ven"))
    store.dispatch(event: .onUpatePassword("123456"))

    // 取消订阅
    store.unsubscribe(subscriptionID)
    
    // 描叙预期 
    let expectNewStates = [
        State(
            username: "",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "123456",
            loading: false,
            data: nil,
            error: nil
        )
    ]
    
    let expectOldStates = [
        nil,
        State(
            username: "",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "",
            loading: false,
            data: nil,
            error: nil
        )
    ]
    
    // 比对结果
    newStates == expectNewStates // 结果：true 😎
    oldStates == expectOldStates // 结果：true 😎
}
```

以上是在单元测试环境下,

首先下创建 `Store`:

```swift    
    let initailState = State(
        username: "",
        password: "",
        loading: false,
        data: nil,
        error: nil
    )
    
    let store = Store(initailState: initailState)
```

然后，订阅程序状态，并且将这些状态录制下来:

```swift    
    var newStates: [State] = []
    var oldStates: [State?] = []
    
    let subscriptionID = store.subscribe { (oldState, newState) in
        newStates.append(newState)
        oldStates.append(oldState)
    }
```

然后，模拟输入用户名事件和输入密码事件:

```swift    
    store.dispatch(event: .onUpateUsername("beeth0ven"))
    store.dispatch(event: .onUpatePassword("123456"))
```

然后，取消订阅:

```swift    
    store.unsubscribe(subscriptionID)
```

最后，比对录制的状态是否符合预期：

```swift    
    let expectNewStates = [
        State(
            username: "",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "123456",
            loading: false,
            data: nil,
            error: nil
        )
    ]
    
    let expectOldStates = [
        nil,
        State(
            username: "",
            password: "",
            loading: false,
            data: nil,
            error: nil
        ),
        State(
            username: "beeth0ven",
            password: "",
            loading: false,
            data: nil,
            error: nil
        )
    ]
    
    newStates == expectNewStates // 结果：true 😎
    oldStates == expectOldStates // 结果：true 😎
```

这就是如何在测试环境里面使用 `Store`，那么在 `App` 里面如何使用 `Store` 呢。 一个 **相对简单（并未优化）** 的方法，就是将 `Store` 注入到对应的组件里面，这里以 `ViewController` 为例：
* `ViewController` 可以使用 `store.subscribe` 方法订阅程序的状态。当状态更新时，比对新旧状态，然后刷新**过时了**的 UI。
* 当用户触发某个事件时，调用 `store.dispatch` 方法将事件发出去，如：当用户点击登录按钮时，就调用 `store.dispatch(event: .onTriggerLogin)`。
* 在 `ViewController` 的 `deinit` 方法里面注销订阅 `store.unsubscribe(subsriptionID)`。

## 总结

本节主要介绍了 [纯函数] 和 [附加作用]，期间还演示如何用 [纯函数] 做状态管理的。最后还演化出了一个极简版的 [Redux]。希望大家可以从中获益！

### 参考

* [纯函数]
* [附加作用]
* [RxFeedback]
* [Redux]
* [ReactorKit]

[纯函数]:https://zh.wikipedia.org/wiki/%E7%BA%AF%E5%87%BD%E6%95%B0
[附加作用]:/content/recipes/side_effects.md
[RxFeedback]:https://github.com/NoTests/RxFeedback.swift
[Redux]:https://redux.js.org/
[ReactorKit]:https://github.com/ReactorKit/ReactorKit

