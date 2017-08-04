# Summary

* [RxSwift 中文文档](introduction.md)
* [1. 为什么要使用 RxSwift?](content/why_rxswift.md)
* [2. 你好 RxSwift！](content/first_app.md)
* [3. 函数响应式编程](content/think_reactive.md)
  * [3.1 函数式编程](content/think_reactive/funtional_programming.md)
  * [3.2 函数式编程 -> 函数响应式编程](content/think_reactive/functional_reactive_progaramming.md)
  * [3.3 数据绑定](content/think_reactive/data_binding.md)
* [4. RxSwift 的核心组件](content/rxswift_core.md)
  * [4.1 Observable - 可被监听的序列](content/rxswift_core/observable.md)
    * [Observable](content/rxswift_core/observable/observable.md)
    * [Single](content/rxswift_core/observable/single.md)
    * [Completeable](content/rxswift_core/observable/completeable.md)
    * [Maybe](content/rxswift_core/observable/maybe.md)
    * [Driver](content/rxswift_core/observable/driver.md)
    * [ControlEvent](content/rxswift_core/observable/control_event.md)
  * [4.2 Observer - 观察者](content/rxswift_core/observer.md)  
    * [AnyObserver](content/rxswift_core/observer/any_observer.md)
    * [UIBindingObserver](content/rxswift_core/observer/uibinding_observer.md)
  * [4.3 Observable & Observer 既是可被监听的序列也是观察者](content/rxswift_core/observable_and_observer.md)
    * [AsyncSubject](content/rxswift_core/observable_and_observer/async_subject.md)
    * [PublishSubject](content/rxswift_core/observable_and_observer/publish_subject.md)
    * [ReplaySubject](content/rxswift_core/observable_and_observer/replay_subject.md)
    * [BehaviorSubject](content/rxswift_core/observable_and_observer/behavior_subject.md)
    * [Variable](content/rxswift_core/observable_and_observer/variable.md)
    * [ControlProperty](content/rxswift_core/observable_and_observer/control_property.md)
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
  * [4.4 Disposable - 可被清除的](content/rxswift_core/disposable.md)
  * [4.4 Schedulers - 调度器](content/rxswift_core/schedulers.md)
  * [4.5 Error Handling - 错误处理](content/rxswift_core/error_handling.md)
* [5. 如何选择操作符？](content/decision_tree.md)
* [6. 更多例子](content/more_demo.md)
* [7. RxSwift 常用架构](content/architecture.md)
  * 6.1 MVVM
  * 6.2 RxFeedback - 反馈循环
  * [6.3 ReactorKit - 反应器](content/architecture/reactorkit.md)
* [8. 为什么要继续使用 RxSwift?](content/why_rxswift_again.md)
* [9. 学习资源](content/resource.md)
* [10. 关于本文档](content/about.md)
