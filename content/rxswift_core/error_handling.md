## Error Handling - 错误处理

一旦序列里面产出了一个 `error` 事件，整个序列将被终止。[RxSwift](https://github.com/ReactiveX/RxSwift) 主要有两种错误处理机制：

* retry - 重试
* catch - 恢复

---

### retry - 重试

[retry](/content/decision_tree/retry.md) 可以让序列在发生错误后重试：

```swift
// 请求 JSON 失败时，立即重试，
// 重试 3 次后仍然失败，就将错误抛出

let rxJson: Observable<JSON> = ...

rxJson
    .retry(3)
    .subscribe(onNext: { json in
        print("取得 JSON 成功: \(json)")
    }, onError: { error in
        print("取得 JSON 失败: \(error)")
    })
    .disposed(by: disposeBag)
```

以上的代码非常直接 `retry(3)` 就是当发生错误时，就进行重试操作，并且最多重试 3 次。

### retryWhen

如果我们需要在发生错误时，经过一段延时后重试，那可以这样实现：

```swift
// 请求 JSON 失败时，等待 5 秒后重试，

let retryDelay: Double = 5  // 重试延时 5 秒

rxJson
    .retryWhen { (rxError: Observable<Error>) -> Observable<Int> in
        return Observable.timer(retryDelay, scheduler: MainScheduler.instance)
    }
    .subscribe(...)
    .disposed(by: disposeBag)
```

这里我们需要用到 **retryWhen** 操作符，这个操作符主要描述应该在何时重试，并且通过闭包里面返回的 `Observable` 来控制重试的时机：

```swift
.retryWhen { (rxError: Observable<Error>) -> Observable<Int> in
    ...
}
```

闭包里面的参数是 `Observable<Error>` 也就是所产生错误的序列，然后返回值是一个 `Observable`。当这个返回的 `Observable` 发出一个元素时，就进行重试操作。当它发出一个 `error` 或者 `completed` 事件时，就不会重试，并且将这个事件传递给到后面的观察者。

如果需要加上一个最大重试次数的限制：

```swift
// 请求 JSON 失败时，等待 5 秒后重试，
// 重试 4 次后仍然失败，就将错误抛出

let maxRetryCount = 4       // 最多重试 4 次
let retryDelay: Double = 5  // 重试延时 5 秒

rxJson
    .retryWhen { (rxError: Observable<Error>) -> Observable<Int> in
        return rxError.flatMapWithIndex { (error, index) -> Observable<Int> in
            guard index < maxRetryCount else {
                return Observable.error(error)
            }
            return Observable<Int>.timer(retryDelay, scheduler: MainScheduler.instance)
        }
    }
    .subscribe(...)
    .disposed(by: disposeBag)
```

我们这里要实现的是，如果重试超过 4 次，就将错误抛出。如果错误在 4 次以内时，就等待 5 秒后重试：

```swift
...
rxError.flatMapWithIndex { (error, index) -> Observable<Int> in
    guard index < maxRetryCount else {
        return Observable.error(error)
    }
    return Observable<Int>.timer(retryDelay, scheduler: MainScheduler.instance)
}
...
```

我们用 **flatMapWithIndex** 这个操作符，因为它可以给我们提供错误的索引数 `index`。然后用这个索引数判断是否超过最大重试数，如果超过了，就将错误抛出。如果没有超过，就等待 5 秒后重试。

---

### catchError - 恢复

[catchError](/content/decision_tree/catchError.md) 可以在错误产生时，用一个备用元素或者一组备用元素将错误替换掉：

```swift
searchBar.rx.text.orEmpty
    ...
    .flatMapLatest { query -> Observable<[Repository]> in
        ...
        return searchGitHub(query)
            .catchErrorJustReturn([])
    }
    ...
    .bind(to: ...)
    .disposed(by: disposeBag)
```

我们开头的 [Github 搜索](/introduction.md)就用到了[catchErrorJustReturn](/content/decision_tree/catchError.md)。当错误产生时，就返回一个空数组，于是就会显示一个空列表页。

你也可以使用 [catchError](/content/decision_tree/catchError.md)，当错误产生时，将错误事件替换成一个备选序列：

```swift
// 先从网络获取数据，如果获取失败了，就从本地缓存获取数据

let rxData: Observable<Data> = ...      // 网络请求的数据
let cahcedData: Observable<Data> = ...  // 之前本地缓存的数据

rxData
    .catchError { _ in cahcedData }
    .subscribe(onNext: { date in
        print("获取数据成功: \(date.count)")
    })
    .disposed(by: disposeBag)
```

---

### Result

如果我们只是想给用户错误提示，那要如何操作呢？

以下提供一个最为直接的方案，不过这个方案存在一些问题：

```swift
// 当用户点击更新按钮时，
// 就立即取出修改后的用户信息。
// 然后发起网络请求，进行更新操作，
// 一旦操作失败就提示用户失败原因

updateUserInfoButton.rx.tap
    .withLatestFrom(rxUserInfo)
    .flatMapLatest { userInfo -> Observable<Void> in
        return update(userInfo)
    }
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: {
        print("用户信息更新成功")
    }, onError: { error in
        print("用户信息更新失败： \(error.localizedDescription)")
    })
    .disposed(by: disposeBag)
```

这样实现是非常直接的。但是一旦网络请求操作失败了，序列就会终止。整个订阅将被取消。如果用户再次点击更新按钮，就无法再次发起网络请求进行更新操作了。

为了解决这个问题，我们需要选择合适的方案来进行错误处理。例如，使用系统自带的枚举 **Result**：

```swift
public enum Result<Success, Failure> where Failure : Error {
    case success(Success)
    case failure(Failure)
}
```

然后之前的代码需要修改成：

```swift
updateUserInfoButton.rx.tap
    .withLatestFrom(rxUserInfo)
    .flatMapLatest { userInfo -> Observable<Result<Void, Error>> in
        return update(userInfo)
            .map(Result.success)  // 转换成 Result
            .catchError { error in Observable.just(Result.failure(error)) }
    }
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: { result in
        switch result {           // 处理 Result
        case .success:
            print("用户信息更新成功")
        case .failure(let error):
            print("用户信息更新失败： \(error.localizedDescription)")
        }
    })
    .disposed(by: disposeBag)
```

这样我们的错误事件被包装成了 `Result.failure(Error)` 元素，就不会终止整个序列。即便网络请求失败了，整个订阅依然存在。如果用户再次点击更新按钮，也是能够发起网络请求进行更新操作的。

另外你也可以使用 [materialize](/content/decision_tree/materialize.md) 操作符来进行错误处理。这里就不详细介绍了，如你想了解如何使用 [materialize](/content/decision_tree/materialize.md) 可以参考这篇文章 [How to handle errors in RxSwift](http://adamborek.com/how-to-handle-errors-in-rxswift/)!
