## map

**通过一个转换函数，将 `Observable` 的每个元素转换一遍**

![](/assets/WhichOperator/Operators/map.png)

**map** 操作符将源 `Observable` 的每个元素应用你提供的转换方法，然后返回含有转换结果的  `Observable`。

---

### 演示

```swift
let disposeBag = DisposeBag()
Observable.of(1, 2, 3)
          .map { $0 * 10 }
          .subscribe(onNext: { print($0) })
          .disposed(by: disposeBag)
```

**输出结果：**

```swift
10
20
30
```

---

### 参考

* [flatMap](flatMap.md)
