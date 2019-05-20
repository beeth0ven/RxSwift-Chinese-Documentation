# 共享 [附加作用]


文档中一些特征序列，会有如下特性：

共享 [附加作用]：
 * [Driver](/content/rxswift_core/observable/driver.md)
 * [Signal](/content/rxswift_core/observable/signal.md)
 * [ControlEvent](/content/rxswift_core/observable/control_event.md)
 * ...

不共享 [附加作用]：
 * [Single](/content/rxswift_core/observable/single.md)
 * [Completable](/content/rxswift_core/observable/completable.md)
 * [Maybe](/content/rxswift_core/observable/maybe.md)
 * ...

那什么是**共享 [附加作用]**，什么是**不共享 [附加作用]**？

### 共享 [附加作用]：

```swift
...
let observable: Observable<Teacher> = API.teacher(teacherId: 1)
let shareSideEffects: Driver<Teacher> = observable.asDriver(onErrorDriveWith: .empty())

let observer0: (Teacher) -> () = ...
let observer1: (Teacher) -> () = ...

shareSideEffects.drive(onNext: observer0)
shareSideEffects.drive(onNext: observer1) // 第二次订阅
```

如果一个序列**共享 [附加作用]**，那在第二次订阅时，不会重新发起网络请求，而是共享第一次网络请求（[附加作用]）。

### 不共享 [附加作用]：

```swift
...
let observable: Observable<Teacher> = API.teacher(teacherId: 1)
let notShareSideEffects: Single<Teacher> = observable.asSingle()

let observer0: (Teacher) -> () = ...
let observer1: (Teacher) -> () = ...

notShareSideEffects.subscribe(onSuccess: observer0)
notShareSideEffects.subscribe(onSuccess: observer1) // 第二次订阅
```

如果一个序列**不共享 [附加作用]**，那在第二次订阅时，会重新发起网络请求，而不是共享第一次网络请求（[附加作用]）。

因此我们需要注意，如果一个网络请求序列，他**不共享 [附加作用]**，那每一次订阅时就会单独发起网络请求。这时最好改用 **共享 [附加作用]** 的序列，或者使用 [share] 操作符。



[附加作用]:/content/recipes/side_effects.md
[share]:/content/decision_tree/shareReplay.md
