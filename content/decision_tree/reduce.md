## reduce

**持续的将 `Observable` 的每一个元素应用一个函数，然后发出最终结果**

![](/assets/WhichOperator/Operators/reduce.png)

**reduce** 操作符将对第一个元素应用一个函数。然后，将结果作为参数填入到第二个元素的应用函数中。以此类推，直到遍历完全部的元素后发出最终结果。

这种操作符在其他地方有时候被称作是 **accumulator**，**aggregate**，**compress**，**fold** 或者 **inject**。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of(10, 100, 1000)
    .reduce(1, accumulator: +)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
1111
```
