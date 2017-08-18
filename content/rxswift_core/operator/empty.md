## empty

**创建一个空 `Observable`**

![](/assets/Operator/Operators/empty.png)

**empty** 操作符将创建一个 `Observable`，这个 `Observable` 只有一个完成事件。

### 演示

创建一个空 `Observable`：

```swift
let id = Observable<Int>.empty()
```

它相当于：

```swift
let id = Observable<Int>.create { observer in
    observer.onCompleted()
    return Disposables.create()
}
```
