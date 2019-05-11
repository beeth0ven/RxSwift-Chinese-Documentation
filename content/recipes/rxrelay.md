# RxRelay

**RxRelay** 既是 **可监听序列** 也是 **观察者**。

他和 **Subjects** 相似，唯一的区别是不会接受 `onError` 或 `onCompleted` 这样的终止事件。

在将非 Rx 样式的 API 转化为 Rx 样式时，**Subjects** 是非常好用的。不过一旦 **Subjects** 接收到了终止事件 `onError` 或 `onCompleted`。他就无法继续工作了，也不会转发后续任何事件。有些时候这是合理的，但在多数场景中这并不符合我们的预期。

在这些场景中一个更严谨的做法就是，创造一种**特殊的 Subjects**，这种 **Subjects** 不会接受终止事件。有了他，我们将 API 转化为 Rx 样式时，就不必担心一个意外的终止事件，导致后续事件转发失效。

我们将这种**特殊的 Subjects** 称作 **RxRelay**：


## PublishRelay
**PublishRelay** 就是 [PublishSubject] 去掉终止事件 `onError` 或 `onCompleted`。


### 演示

```swift
let disposeBag = DisposeBag()
let relay = PublishRelay<String>()

relay
    .subscribe { print("Event:", $0) }
    .disposed(by: disposeBag)

relay.accept("🐶")
relay.accept("🐱")
```

**输出结果：**

```swift
Event: next(🐶)
Event: next(🐱)
```

## BehaviorRelay
**BehaviorRelay** 就是 [BehaviorSubject] 去掉终止事件 `onError` 或 `onCompleted`。

### 演示

```swift
let disposeBag = DisposeBag()
let relay = BehaviorRelay(value: "🔴")

relay
    .subscribe { print("Event:", $0) }
    .disposed(by: disposeBag)

relay.accept("🐶")
relay.accept("🐱")
```

**输出结果：**

```swift
Event: next(🔴)
Event: next(🐶)
Event: next(🐱)
```

**BehaviorRelay** 将取代 **Variable**，因为 **Variable** 很容易会引导我们使用[命令式编程]，而不是[声明式编程]。


[PublishSubject]:/content/rxswift_core/observable_and_observer/publish_subject.md
[BehaviorSubject]:/content/rxswift_core/observable_and_observer/behavior_subject.md
[命令式编程]:https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B
[声明式编程]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B