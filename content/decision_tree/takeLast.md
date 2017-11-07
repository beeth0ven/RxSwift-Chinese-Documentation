## takeLast

**仅仅从 `Observable` 中发出尾部 n 个元素**

![](/assets/WhichOperator/Operators/takeLast.png)

通过 **takeLast** 操作符你可以只发出尾部 **n** 个元素。并且忽略掉前面的元素。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
    .takeLast(3)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
🐸
🐷
🐵
```
