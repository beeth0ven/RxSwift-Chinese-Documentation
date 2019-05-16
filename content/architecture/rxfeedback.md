## RxFeedback

[![Travis CI](https://travis-ci.org/kzaher/RxFeedback.svg?branch=master)](https://travis-ci.org/kzaher/RxFeedback) ![platforms](https://img.shields.io/badge/platforms-iOS%20%7C%20macOS%20%7C%20tvOS%20%7C%20watchOS%20-333333.svg) ![pod](https://img.shields.io/cocoapods/v/RxFeedback.svg) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) [![Swift Package Manager compatible](https://img.shields.io/badge/Swift%20Package%20Manager-compatible-brightgreen.svg)](https://github.com/apple/swift-package-manager)

### 作者

[Krunoslav Zaher](https://github.com/kzaher) 是 [RxFeedback] 的作者。他也是 [RxSwift](https://github.com/ReactiveX/RxSwift) 的**创始人**以及 [ReactiveX 组织](https://github.com/ReactiveX) 的核心成员。他有 16 年以上的编程经验（ VR 引擎，BPM 系统，移动端应用程序，机器人等），最近在研究响应式编程。

### 介绍

[RxSwift](https://github.com/ReactiveX/RxSwift) 最简单的架构

![](/assets/Architecture/RxFeedback/RxFeedback.png)

```swift
typealias Feedback<State, Event> = (Observable<State>) -> Observable<Event>

public static func system<State, Event>(
    initialState: State,
    reduce: @escaping (State, Event) -> State,
    feedback: Feedback<State, Event>...
) -> Observable<State>
```

## 为什么？

* 直接
  * 已经发生 -> Event
  * 即将发生 -> Request
  * 执行 Request -> Feedback loop
* [声明式]
  * 首先系统行为被明确声明出来，然后在调用 subscribe 后开始运作 => 编译时就保证了不会有“未处理状态”
* 容易调试
  * 大多数逻辑是 [纯函数]，可以通过 xCode 调试器调试，或者将命令打印出来
* 适用于任何级别
  * [整个系统](https://kafka.apache.org/documentation/)
  * 应用程序（state 被储存在数据库中，CoreData, Firebase, Realm）
  * view controller (state 被储存在 system 操作符)
  * 在 feedback loop 中（feedback loop 中 调用另一个 system 操作符）
* 容易做依赖注入
* 易测试
  * Reducer 是 [纯函数]，只需调用他并断言结果即可
  * 伴随 [附加作用] 的测试 -> TestScheduler
* 可以处理循环依赖
* 完全从[附加作用]中分离业务逻辑
  * 业务逻辑可以在不同平台之间转换


### 示例

<img src="/assets/Architecture/RxFeedback/Counter.gif" width="320px" />

```swift
Observable.system(
    initialState: 0,
    reduce: { (state, event) -> State in
        switch event {
        case .increment:
            return state + 1
        case .decrement:
            return state - 1
        }
    },
    scheduler: MainScheduler.instance,
    feedback:
        // UI is user feedback
        bind(self) { me, state -> Bindings<Event> in
            let subscriptions = [
                state.map(String.init).bind(to: me.label.rx.text)
            ]

            let events = [
                me.plus.rx.tap.map { Event.increment },
                me.minus.rx.tap.map { Event.decrement }
            ]

            return Bindings(
                subscriptions: subscriptions,
                events: events
            )
        }
)
```

这是一个简单计数的例子，只是用于演示 [RxFeedback] 架构。

### State

系统状态用 **State** 表示：

```swift
typealias State = Int
```

* 这里的状态就是计数的数值

### Event

事件用 **Event** 表示：

```swift
enum Event {
    case increment
    case decrement
}
```

* increment 增加数值事件
* decrement 减少数值事件

当产生 **Event** 时更新状态：

```swift
Observable.system(
    initialState: 0,
    reduce: { (state, event) -> State in
            switch event {
            case .increment:
                return state + 1
            case .decrement:
                return state - 1
            }
        },
    scheduler: MainScheduler.instance,
    feedback: ...
    )
```

* increment 状态数值加一
* decrement 状态数值减一

### Feedback Loop

将**状态**输出到 UI 页面上，或者将 UI **事件**输入到反馈循环里面去:

```swift
Observable.system(
    initialState: 0,
    reduce: { ... },
    scheduler: MainScheduler.instance,
    feedback:
        // UI is user feedback
        bind(self) { me, state -> Bindings<Event> in
            let subscriptions = [
                state.map(String.init).bind(to: me.label.rx.text)
            ]

            let events = [
                me.plus.rx.tap.map { Event.increment },
                me.minus.rx.tap.map { Event.decrement }
            ]

            return Bindings(
                subscriptions: subscriptions,
                events: events
            )
        }
    )
```

* 将状态数值用 `label` 显示出来
* 将增加按钮的点击，作为增加数值事件传入
* 将减少按钮的点击，作为减少数值事件传入

## 安装

### [CocoaPods](http://cocoapods.org/)

[CocoaPods](http://cocoapods.org/) 是一个 Cocoa 项目的依赖管理工具。你可以通过以下命令安装他：

```bash
$ gem install cocoapods
```

将 RxFeedback 整合到项目中来，你需要在 `Podfile` 中指定他：

```bash
pod 'RxFeedback', '~> 3.0'
```

然后运行以下命令：

```bash
$ pod install
```


### [Carthage](https://github.com/Carthage/Carthage)

[Carthage](https://github.com/Carthage/Carthage) 是一个分散式依赖管理工具，他将构建你的依赖并提供二进制框架。

你可以通过以下 [Homebrew](http://brew.sh/) 命令安装 Carthage：

```bash
$ brew update
$ brew install carthage
```

将 RxFeedback 整合到项目中来，你需要在 `Cartfile` 中指定他：

```bash
github "NoTests/RxFeedback" ~> 3.0
```

运行 `carthage update` 去构建框架，然后将 `RxFeedback.framework` 拖入到 Xcode 项目中来。由于 `RxFeedback` 对 `RxSwift` 和 `RxCocoa` 有依赖，所以你也需要将 `RxSwift.framework` 和 `RxCocoa.framework` 拖入到 Xcode 项目中来。

### [Swift Package Manager](https://swift.org/package-manager/)

[Swift Package Manager](https://swift.org/package-manager/) 是一个自动分发 Swift 代码的工具，他已经被集成到 Swift 编译器中。

一旦你配置好了 Swift 包，添加 RxFeedback 就非常简单了，你只需要将他添加到文件 `Package.swift` 的 `dependencies` 的值中。

```bash
dependencies: [
    .package(url: "https://github.com/NoTests/RxFeedback.swift.git", majorVersion: 1)
]
```
## 与其他架构的区别
* [Elm] - 非常相似，feedback loop 用作 [附加作用]， 而不是 `Cmd`, 要执行的 [附加作用] 被编码到 state 中，并且通过 feedback loop 完成请求
* [Redux] - 也很像，不过采用 feedback loops 而不是 middleware
* [Redux-Observable] - observables 观察状态，与视图和状态之间的 middleware
* [Cycle.js] - 一言难尽 :)，请咨询 [@andrestaltz](https://twitter.com/andrestaltz)
* [MVVM] - 将状态和 [附加作用] 分离，而且不需要 View

### 示例

下一节将用 [Github Search] 来演示如何使用 [RxFeedback]。

[RxFeedback]:https://github.com/kzaher/RxFeedback
[Github Search]:rxfeedback/github_search.md

[附加作用]:/content/recipes/pure_function.md
[纯函数]:/content/recipes/pure_function.md
[声明式]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B

[Elm]:https://guide.elm-lang.org/architecture/
[Redux]:https://redux.js.org/
[Redux-Observable]:https://redux-observable.js.org/
[Cycle.js]:https://cycle.js.org/
[MVVM]:/content/architecture/mvvm.md
