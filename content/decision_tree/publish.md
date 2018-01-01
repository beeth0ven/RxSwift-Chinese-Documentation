## publish

**将 `Observable` 转换为可被连接的 `Observable`**

![](/assets/WhichOperator/Operators/publish.png)

**publish** 会将 `Observable` 转换为**可被连接的 `Observable`**。**可被连接的 `Observable`** 和普通的 `Observable` 十分相似，不过在被订阅后不会发出元素，直到 [connect] 操作符被应用为止。这样一来你可以控制 `Observable` 在什么时候**开始**发出元素。

---

### 演示

```swift
let intSequence = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
    .publish()

_ = intSequence
    .subscribe(onNext: { print("Subscription 1:, Event: \($0)") })

DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
    _ = intSequence.connect()
}

DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
  _ = intSequence
      .subscribe(onNext: { print("Subscription 2:, Event: \($0)") })
}

DispatchQueue.main.asyncAfter(deadline: .now() + 6) {
  _ = intSequence
      .subscribe(onNext: { print("Subscription 3:, Event: \($0)") })
}
```

**输出结果：**

```swift
Subscription 1:, Event: 0
Subscription 1:, Event: 1
Subscription 2:, Event: 1
Subscription 1:, Event: 2
Subscription 2:, Event: 2
Subscription 1:, Event: 3
Subscription 2:, Event: 3
Subscription 3:, Event: 3
Subscription 1:, Event: 4
Subscription 2:, Event: 4
Subscription 3:, Event: 4
Subscription 1:, Event: 5
Subscription 2:, Event: 5
Subscription 3:, Event: 5
Subscription 1:, Event: 6
Subscription 2:, Event: 6
Subscription 3:, Event: 6
...
```

[connect]:connect.md
