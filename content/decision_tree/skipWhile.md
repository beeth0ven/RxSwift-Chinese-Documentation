### skipWhile

**跳过 `Observable` 中头几个元素，直到元素的判定为否**

![](/assets/WhichOperator/Operators/skipWhile.png)

**skipWhile** 操作符可以让你忽略源 `Observable` 中头几个元素，直到元素的判定为否后，它才镜像源 `Observable`。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 3, 2, 1)
    .skipWhile { $0 < 4 }
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
4
3
2
1
```
