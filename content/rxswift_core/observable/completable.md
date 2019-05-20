## Completable

**Completable** 是 `Observable` 的另外一个版本。不像 `Observable` 可以发出多个元素，它要么只能产生一个 `completed` 事件，要么产生一个 `error` 事件。

* 发出零个元素
* 发出一个 `completed` 事件或者一个 `error` 事件
* 不会[共享附加作用]

**Completable** 适用于那种你只关心任务是否完成，而不需要在意任务返回值的情况。它和 `Observable<Void>` 有点相似。

### 如何创建 Completable
创建 **Completable** 和创建 **Observable** 非常相似：

```swift
func cacheLocally() -> Completable {
    return Completable.create { completable in
       // Store some data locally
       ...
       ...

       guard success else {
           completable(.error(CacheError.failedCaching))
           return Disposables.create {}
       }

       completable(.completed)
       return Disposables.create {}
    }
}
```

之后，你可以这样使用 **Completable**：

```swift
cacheLocally()
    .subscribe(onCompleted: {
        print("Completed with no error")
    }, onError: { error in
        print("Completed with an error: \(error.localizedDescription)")
     })
    .disposed(by: disposeBag)
```

订阅提供一个 `CompletableEvent` 的枚举：

```swift
public enum CompletableEvent {
    case error(Swift.Error)
    case completed
}
```

* completed - 产生完成事件
* error - 产生一个错误

[共享附加作用]:/content/recipes/share_side_effects.md
