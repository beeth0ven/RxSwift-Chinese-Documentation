## connect

**通知 `ConnectableObservable` 可以开始发出元素了**

![](/assets/WhichOperator/Operators/publish.png)

** `ConnectableObservable`** 和普通的 `Observable` 十分相似，不过在被订阅后不会发出元素，直到 **connect** 操作符被应用为止。这样一来你可以等所有观察者全部订阅完成后，才发出元素。

---

### 演示

```swift
let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
    .replay(5)

_ = intSequence
    .subscribe(onNext: { print("Subscription 1:, Event: \($0)") })

DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
    _ = intSequence.connect()
}

DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
  _ = intSequence
      .subscribe(onNext: { print("Subscription 2:, Event: \($0)") })
}

DispatchQueue.main.asyncAfter(deadline: .now() + 8) {
  _ = intSequence
      .subscribe(onNext: { print("Subscription 3:, Event: \($0)") })
}

```

**输出结果：**

```swift
Subscription 1:, Event: 0
Subscription 2:, Event: 0
Subscription 1:, Event: 1
Subscription 2:, Event: 1
Subscription 1:, Event: 2
Subscription 2:, Event: 2
Subscription 1:, Event: 3
Subscription 2:, Event: 3
Subscription 1:, Event: 4
Subscription 2:, Event: 4
Subscription 3:, Event: 0
Subscription 3:, Event: 1
Subscription 3:, Event: 2
Subscription 3:, Event: 3
Subscription 3:, Event: 4
Subscription 1:, Event: 5
Subscription 2:, Event: 5
Subscription 3:, Event: 5
Subscription 1:, Event: 6
Subscription 2:, Event: 6
Subscription 3:, Event: 6
...
```
