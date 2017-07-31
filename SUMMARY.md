# Summary

* [RxSwift 中文文档](README.md)
* [1. 为什么要使用 RxSwift?](content/why_rxswift.md)
* [2. 你好 RxSwift！](content/first_app.md)
* [3. 函数响应式编程](content/think_reactive.md)
  * [3.1 函数式编程](content/think_reactive/funtional_programming.md)
  * [3.2 函数式编程 -> 函数响应式编程](content/think_reactive/functional_reactive_progaramming.md)
  * [3.3 数据绑定](content/think_reactive/data_binding.md)
* [4. RxSwift 的核心组件](content/rxswift_core.md)
  * [4.1 Observable - 可被监听的序列](content/rxswift_core/observable.md)
    * Observable
    * Single
    * Completeable
    * Maybe
    * Driver
    * ControlEvent
  * [4.2 Observer - 观察者](content/rxswift_core/observer.md)  
    * AnyObserver
    * UIBindingObserver
  * [4.3 Observable & Observer 同时是可被监听的序列和观察者](content/rxswift_core/observable_and_observer.md)
    * AsyncSubject
    * PublishSubject
    * ReplaySubject
    * BehaviorSubject
    * Variable
    * ControlProperty
  * [4.3 Operator - 操作符](content/rxswift_core/operator.md)
    * combineLatest
    * delay
    * debounce
    * distinctUntilChanged
    * do
    * filter
    * flatMap
    * map
    * merge
    * observeOn
    * retry
    * scan
    * shareReplay
    * skip
    * skipUntil
    * startWith
    * subscribeOn
    * takeUntil
    * timeout
    * withLatestFrom
    * zip
  * [4.4 Disposable - 可被清除的绑定](content/rxswift_core/disposable.md)
  * [4.4 Schedulers - 计划](content/rxswift_core/schedulers.md)
  * 4.5 Error Handling - 错误处理
* [5. 更多例子](content/more_demo.md)
* [6. RxSwift 常用架构](content/architecture.md)
  * 6.1 MVVM - 数据绑定 UI
  * 6.2 RxFeedback - 反馈循环
  * [6.3 ReactorKit - 结合Flux和响应式编程](content/architecture/reactorkit.md)
* [7. 为什么要继续使用 RxSwift?](content/why_rxswift_again.md)
* [8. 学习资源](content/resource.md)
* [9. 关于本文档](content/about.md)
