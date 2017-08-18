## never

**创建一个永远不会发出元素的 `Observable`**

![](/assets/Operator/Operators/never.png)

**never** 操作符将创建一个 `Observable`，这个 `Observable` 不会产生任何事件。

### 演示

创建一个不会产生任何事件的 `Observable`：

```swift
let id = Observable<Int>.never()
```

它相当于：

```swift
let id = Observable<Int>.create { observer in
    return Disposables.create()
}
```
