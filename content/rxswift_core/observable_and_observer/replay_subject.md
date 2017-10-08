## ReplaySubject

![](/assets/ObservableAndObserver/ReplaySubject.png)

**ReplaySubject** 将对观察者发送全部的元素，无论观察者是何时进行订阅的。

这里存在多个版本的 **ReplaySubject**，有的只会将最新的 n 个元素发送给观察者，有的只会将限制时间段内最新的元素发送给观察者。

如果把 **ReplaySubject** 当作观察者来使用，注意不要在多个线程调用 `onNext`, `onError` 或 `onCompleted`。这样会导致无序调用，将造成意想不到的结果。

---

### 演示

```swift
let disposeBag = DisposeBag()
let subject = ReplaySubject<String>.create(bufferSize: 1)

subject
  .subscribe { print("Subscription: 1 Event:", $0) }
  .disposed(by: disposeBag)

subject.onNext("🐶")
subject.onNext("🐱")

subject
  .subscribe { print("Subscription: 2 Event:", $0) }
  .disposed(by: disposeBag)

subject.onNext("🅰️")
subject.onNext("🅱️")
```

**输出结果：**

```swift
Subscription: 1 Event: next(🐶)
Subscription: 1 Event: next(🐱)
Subscription: 2 Event: next(🐱)
Subscription: 1 Event: next(🅰️)
Subscription: 2 Event: next(🅰️)
Subscription: 1 Event: next(🅱️)
Subscription: 2 Event: next(🅱️)
```
