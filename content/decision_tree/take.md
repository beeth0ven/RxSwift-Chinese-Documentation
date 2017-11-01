## take

**仅仅从 `Observable` 中发出头 n 个元素**

![](/assets/WhichOperator/Operators/take.png)

通过 **take** 操作符你可以只发出头 **n** 个元素。并且忽略掉后面的元素，直接结束序列。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
    .take(3)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
🐱
🐰
🐶
```
