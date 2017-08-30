## filter

**仅仅发出 `Observable` 中通过判定的元素**

![](/assets/Operator/Operators/filter.png)

**filter** 操作符将通过你提供的判定方法过滤一个 `Observable`。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of(2, 30, 22, 5, 60, 1)
          .filter { $0 > 10 }
          .subscribe(onNext: { print($0) })
          .disposed(by: disposeBag)
```

**输出结果：**

```swift
30
22
60
```
