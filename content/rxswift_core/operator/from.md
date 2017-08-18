## from

**将其他类型或者数据结构转换为 `Observable`**

![](/assets/Operator/Operators/from.png)


当你在使用 `Observable` 时，如果能够直接将其他类型转换为 `Observable`，这将是非常省事的。**from** 操作符就提供了这种功能。

### 演示

将一个**数组**转换为 `Observable`：

```swift
let numbers = Observable.from([0, 1, 2])
```

它相当于：

```swift
let numbers = Observable<Int>.create { observer in
    observer.onNext(0)
    observer.onNext(1)
    observer.onNext(2)
    observer.onCompleted()
    return Disposables.create()
}
```
---

将一个**可选值**转换为 `Observable`：

```swift
let optional: Int? = 1
let value = Observable.from(optional: optional)
```

它相当于：

```swift
let optional: Int? = 1
let value = Observable<Int>.create { observer in
    if let element = optional {
        observer.onNext(element)
    }
    observer.onCompleted()
    return Disposables.create()
}
```
