## Single

**Single** 是 `Observable` 的另外一个版本。不像 `Observable` 可以发出多个元素，它要么只能发出一个元素，要么产生一个 `error` 事件。

* 发出一个元素，或一个 `error` 事件
* 不会共享[附加作用]

一个比较常见的例子就是执行 HTTP 请求，然后返回一个**应答**或**错误**。不过你也可以用 **Single** 来描述任何只有一个元素的序列。

### 如何创建 Single
创建 **Single** 和创建 **Observable** 非常相似：

```swift
func getRepo(_ repo: String) -> Single<[String: Any]> {

    return Single<[String: Any]>.create { single in
        let url = URL(string: "https://api.github.com/repos/\(repo)")!
        let task = URLSession.shared.dataTask(with: url) {
            data, _, error in

            if let error = error {
                single(.error(error))
                return
            }

            guard let data = data,
                  let json = try? JSONSerialization.jsonObject(with: data, options: .mutableLeaves),
                  let result = json as? [String: Any] else {
                single(.error(DataError.cantParseJSON))
                return
            }

            single(.success(result))
        }

        task.resume()

        return Disposables.create { task.cancel() }
    }
}
```

之后，你可以这样使用 **Single**：

```swift
getRepo("ReactiveX/RxSwift")
    .subscribe(onSuccess: { json in
        print("JSON: ", json)
    }, onError: { error in
        print("Error: ", error)
    })
    .disposed(by: disposeBag)
```

订阅提供一个 `SingleEvent` 的枚举：

```swift
public enum SingleEvent<Element> {
    case success(Element)
    case error(Swift.Error)
}
```

* success - 产生一个单独的元素
* error - 产生一个错误

你同样可以对 `Observable` 调用 `.asSingle()` 方法，将它转换为 **Single**。


[附加作用]:/content/recipes/side_effects.md
