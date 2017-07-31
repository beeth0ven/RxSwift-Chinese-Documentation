# RxSwift 的核心组件

这一章主要介绍 **RxSwift** 的核心组件：

![](/assets/RxSwiftCore.png)

* [Observable](rxswift_core/observable.md) - 产生事件
* [Observer](rxswift_core/observer.md) - 响应事件
* [Operator](rxswift_core/operator.md) - 变化组合事件
* [Disposable](rxswift_core/disposable.md) - 管理绑定（订阅）的生命周期
* [Schedulers](rxswift_core/schedulers.md) - 线程调配

```swift
// Observable<String>
let text = usernameOutlet.rx.text.orEmpty.asObservable()

// Observable<Bool>
let passwordValid = text
    // Operator
    .map { $0.characters.count >= minimalUsernameLength }

// Observer<Bool>
let observer = passwordValidOutlet.rx.isHidden

// Disposable
let disposable = passwordValid
    // Scheduler 用于控制任务在那个线程运行
    .subscribeOn(MainScheduler.instance)
    .observeOn(MainScheduler.instance)
    .bind(to: observer)
    

...

// 取消绑定，你可以在退出页面时取消绑定
disposable.dispose()
```


下面几节会详细介绍这几个组件的功能和用法。
