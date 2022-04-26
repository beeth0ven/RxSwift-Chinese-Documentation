# RxSwift 核心

这一章主要介绍 **RxSwift** 的核心内容：

![](/assets/RxSwiftCore.png)

* [Observable](rxswift_core/observable.md) - 产生事件
* [Observer](rxswift_core/observer.md) - 响应事件
* [Operator](rxswift_core/operator.md) - 创建变化组合事件
* [Disposable](rxswift_core/disposable.md) - 管理绑定（订阅）的生命周期
* [Schedulers](rxswift_core/schedulers.md) - 线程队列调配

```swift
// Observable<String>
let text = usernameOutlet.rx.text.orEmpty.asObservable()

// Observable<Bool>
let passwordValid = text
    // Operator
    .map { $0.count >= minimalUsernameLength }

// Observer<Bool>
let observer = passwordValidOutlet.rx.isHidden

// Disposable
let disposable = passwordValid
    // Scheduler 用于控制任务在那个线程队列运行
    .subscribeOn(MainScheduler.instance)
    .observeOn(MainScheduler.instance)
    .bind(to: observer)


...

// 取消绑定，你可以在退出页面时取消绑定
disposable.dispose()
```


下面几节会详细介绍这几个组件的功能和用法。

_ℹ️ 提示：这一章主要介绍一些偏理论方面的知识。你如果觉得阅读起来比较乏味的话，可以先**快速地浏览一遍**，了解 **RxSwift** 的核心组件大概有哪些内容。待以后遇到实际问题时，在回来查询。你可以直接跳到 [更多例子](/content/more_demo.md) 章节，去了解如何应用 **RxSwift**。_
