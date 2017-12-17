### skip

**跳过 `Observable` 中头 n 个元素**

![](/assets/WhichOperator/Operators/skip.png)

**skip** 操作符可以让你跳过 `Observable` 中头 n 个元素，只关注后面的元素。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
    .skip(2)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
🐶
🐸
🐷
🐵
```
