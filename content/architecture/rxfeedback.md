## RxFeedback

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

系统状态用 `State` 表示，事件用 `Event` 表示。

---

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
