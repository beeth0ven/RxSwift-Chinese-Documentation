## distinctUntilChanged

**阻止 `Observable` 发出相同的元素**

![](/assets/WhichOperator/Operators/distinctUntilChanged.png)

**distinctUntilChanged** 操作符将阻止 `Observable` 发出相同的元素。如果后一个元素和前一个元素是相同的，那么这个元素将不会被发出来。如果后一个元素和前一个元素不相同，那么这个元素才会被发出来。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐱", "🐷", "🐱", "🐱", "🐱", "🐵", "🐱")
    .distinctUntilChanged()
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
🐱
🐷
🐱
🐵
🐱
```
