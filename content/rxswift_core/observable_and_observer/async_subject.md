## AsyncSubject

![](/assets/ObservableAndObserver/AsyncSubject.png)

**AsyncSubject** 将在源 `Observable` 产生完成事件后，发出最后一个元素（仅仅只有最后一个元素），如果源 `Observable` 没有发出任何元素，只有一个完成事件。那 **AsyncSubject** 也只有一个完成事件。

![](/assets/ObservableAndObserver/AsyncSubject1.png)

它会对随后的观察者发出最终元素。如果源 `Observable` 因为产生了一个 `error` 事件而中止， **AsyncSubject** 就不会发出任何元素，而是将这个 `error` 事件发送出来。

---

### 演示

```swift
let disposeBag = DisposeBag()
let subject = AsyncSubject<String>()

subject
  .subscribe { print("Subscription: 1 Event:", $0) }
  .disposed(by: disposeBag)

subject.onNext("🐶")
subject.onNext("🐱")
subject.onNext("🐹")
subject.onCompleted()
```

**输出结果：**

```swift
Subscription: 1 Event: next(🐹)
Subscription: 1 Event: completed
```
