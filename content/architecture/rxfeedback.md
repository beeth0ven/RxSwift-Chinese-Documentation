## RxFeedback

[![Travis CI](https://travis-ci.org/kzaher/RxFeedback.svg?branch=master)](https://travis-ci.org/kzaher/RxFeedback) ![platforms](https://img.shields.io/badge/platforms-iOS%20%7C%20macOS%20%7C%20tvOS%20%7C%20watchOS%20-333333.svg) ![pod](https://img.shields.io/cocoapods/v/RxFeedback.svg) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) [![Swift Package Manager compatible](https://img.shields.io/badge/Swift%20Package%20Manager-compatible-brightgreen.svg)](https://github.com/apple/swift-package-manager)

### 作者

[Krunoslav Zaher](https://github.com/kzaher) 是 [RxFeedback] 的作者。他也是 [RxSwift](https://github.com/ReactiveX/RxSwift) 的**创始人**以及 [ReactiveX 组织](https://github.com/ReactiveX) 的核心成员。他有 16 年以上的编程经验（ VR 引擎，BPM 系统，移动端应用程序，机器人等），最近在研究响应式编程。

### 介绍

[RxSwift](https://github.com/ReactiveX/RxSwift) 最简单的架构

![](/assets/Architecture/RxFeedback/RxFeedback.png)

```swift
public static func system<State, Event>(
        initialState: State,
        reduce: @escaping (State, Event) -> State,
        feedback: (Observable<State>) -> Observable<Event>...
    ) -> Observable<State>
```

模拟一个[反馈循环系统](https://zh.wikipedia.org/wiki/控制理论)。

模拟系统将在被订阅后启动，并且在订阅被释放后停止。

系统状态用 **State** 表示，事件用 **Event** 表示。

---

### 示例

![](/assets/Architecture/RxFeedback/Counter.gif)

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
        UI.bind(self) { me, state -> UI.Bindings<Event> in
            let subscriptions = [
                state.map(String.init).bind(to: me.label!.rx.text)
            ]
            let events = [
                me.plus!.rx.tap.map { Event.increment },
                me.minus!.rx.tap.map { Event.decrement }
            ]
            return UI.Bindings(subscriptions: subscriptions, events: events)
        }
    )
```

这是一个简单计数的例子，只是用于演示 [RxFeedback] 架构。

---

### State

系统状态用 **State** 表示：

```swift
typealias State = Int
```

* 这里的状态就是计数的数值

---

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

---

### Feedback Loop

将**状态**输出到 UI 页面上，或者将 UI **事件**输入到反馈循环里面去:

```swift
Observable.system(
    initialState: 0,
    reduce: { ... },
    scheduler: MainScheduler.instance,
    feedback:
        // UI is user feedback
        UI.bind(self) { me, state -> UI.Bindings<Event> in
            let subscriptions = [
                state.map(String.init).bind(to: me.label!.rx.text)
            ]
            let events = [
                me.plus!.rx.tap.map { Event.increment },
                me.minus!.rx.tap.map { Event.decrement }
            ]
            return UI.Bindings(subscriptions: subscriptions, events: events)
        }
    )
```

* 将状态数值用 `label` 显示出来
* 将增加按钮的点击，作为增加数值事件传入
* 将减少按钮的点击，作为减少数值事件传入

---

### 优势

这就是 [RxFeedback](https://github.com/kzaher/RxFeedback) 架构，它的优势是：

* 简单
* 直接
* 容易调试
* 能被应用到任何级别
* 完美支持依赖注入
* 支持循环依赖
* 完全将业务逻辑分离（跨平台）

### 示例

下一节将用 [Github Search] 来演示如何使用 [RxFeedback]。

[RxFeedback]:https://github.com/kzaher/RxFeedback
[Github Search]:rxfeedback/github_search.md
