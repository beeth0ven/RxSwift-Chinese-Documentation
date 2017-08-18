## create

**通过一个构建函数完整的创建一个 `Observable`**

![](/assets/Operator/Operators/create.png)

**create** 操作符将创建一个 `Observable`，你需要提供一个构建函数，在构建函数里面描述事件（`next`，`error`，`completed`）的产生过程。

通常情况下一个有限的序列，只会调用一次**观察者**的 `onCompleted` 或者 `onError` 方法。并且在调用它们后，不会再去调用**观察者**的其他方法。

### 演示

创建一个 `[0, 1, ... 8, 9]` 的序列：

```swift
let id = Observable<Int>.create { observer in
    observer.onNext(0)
    observer.onNext(1)
    observer.onNext(2)
    observer.onNext(3)
    observer.onNext(4)
    observer.onNext(5)
    observer.onNext(6)
    observer.onNext(7)
    observer.onNext(8)
    observer.onNext(9)
    observer.onCompleted()
    return Disposables.create()
}
```
