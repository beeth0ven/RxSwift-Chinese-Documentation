## just

**创建 `Observable` 发出唯一的一个元素**

![](/assets/Operator/Operators/just.png)

**just** 操作符将某一个元素转换为 `Observable`。

---

### 演示

一个序列只有唯一的元素 **0**：

```swift
let id = Observable.just(0)
```

它相当于：

```swift
let id = Observable<Int>.create { observer in
    observer.onNext(0)
    observer.onCompleted()
    return Disposables.create()
}
```
