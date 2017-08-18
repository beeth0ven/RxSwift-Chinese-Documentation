## repeatElement

**创建 `Observable` 重复的发出某个元素**

![](/assets/Operator/Operators/repeatElement.png)

**repeatElement** 操作符将创建一个 `Observable`，这个 `Observable` 将无止尽的发出同一个元素。

### 演示

创建 `Observable` 重复的发出某个元素：

```swift
let id = Observable.repeatElement(0)
```

它相当于：

```swift
let id = Observable<Int>.create { observer in
    observer.onNext(0)
    observer.onNext(0)
    observer.onNext(0)
    observer.onNext(0)
    ... // 无数次
    return Disposables.create()
}
```
