## Maybe

**Maybe** 是 `Observable` 的另外一个版本。它介于 [Single](single.md) 和 [Completable](completable.md) 之间，它要么只能发出一个元素，要么产生一个 `completed` 事件，要么产生一个 `error` 事件。

* 发出一个元素或者一个 `completed` 事件或者一个 `error` 事件
* 不会共享附加作用

如果你遇到那种可能需要发出一个元素，又可能不需要发出时，就可以使用 **Maybe**。

### 如何创建 Maybe
创建 **Maybe** 和创建 **Observable** 非常相似：

```swift
func generateString() -> Maybe<String> {
    return Maybe<String>.create { maybe in
        maybe(.success("RxSwift"))

        // OR

        maybe(.completed)

        // OR

        maybe(.error(error))

        return Disposables.create {}
    }
}
```

之后，你可以这样使用 **Maybe**：

```swift
generateString()
    .subscribe(onSuccess: { element in
        print("Completed with element \(element)")
    }, onError: { error in
        print("Completed with an error \(error.localizedDescription)")
    }, onCompleted: {
        print("Completed with no element")
    })
    .disposed(by: disposeBag)
```

你同样可以对 `Observable` 调用 `.asMaybe()` 方法，将它转换为 **Maybe**。
