### elementAt

**只发出 `Observable` 中的第 n 个元素**

![](/assets/WhichOperator/Operators/elementAt.png)

**elementAt** 操作符将拉取 `Observable` 序列中指定索引数的元素，然后将它作为唯一的元素发出。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐱", "🐰", "🐶", "🐸", "🐷", "🐵")
    .elementAt(3)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
🐸
```
