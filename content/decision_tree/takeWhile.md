## takeWhile

**镜像一个 `Observable` 直到某个元素的判定为 false**

![](/assets/WhichOperator/Operators/takeWhile.png)

**takeWhile** 操作符将镜像源 `Observable` 直到某个元素的判定为 **false**。此时，这个镜像的 `Observable` 将立即终止。

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 3, 2, 1)
    .takeWhile { $0 < 4 }
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
1
2
3
```
