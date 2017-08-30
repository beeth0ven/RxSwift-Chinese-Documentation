## error

**创建一个只有 `error` 事件的 `Observable`**

![](/assets/Operator/Operators/error.png)

**error** 操作符将创建一个 `Observable`，这个 `Observable` 只会产生一个 `error` 事件。

---

### 演示

创建一个只有 `error` 事件的 `Observable`：

```swift
let error: Error = ...
let id = Observable<Int>.error(error)
```

它相当于：

```swift
let error: Error = ...
let id = Observable<Int>.create { observer in
    observer.onError(error)
    return Disposables.create()
}
```
