## Driver

**Driver（司机？）** 是一个精心准备的特征序列。它主要是为了简化 UI 层的代码。不过如果你遇到的序列具有以下特征，你也可以使用它：

* 不会产生 `error` 事件
* 一定在 `MainScheduler` 监听（主线程监听）
* 共享附加作用

这些都是驱动 UI 的序列所具有的特征。

### 为什么要使用 Driver ？

我们举个例子来说明一下，为什么要使用 **Driver**。

这是文档简介页的例子：

```swift
let results = query.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
    }

results
    .map { "\($0.count)" }
    .bind(to: resultCount.rx.text)
    .disposed(by: disposeBag)

results
    .bind(to: resultsTableView.rx.items(cellIdentifier: "Cell")) {
      (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .disposed(by: disposeBag)
```

这段代码的主要目的是：

* 取出用户输入稳定后的内容
* 向服务器请求一组结果
* 将返回的结果绑定到两个 UI 元素上：`tableView` 和 显示结果数量的`label`

那么这里存在什么问题？

* 如果 `fetchAutoCompleteItems` 的序列产生了一个错误（网络请求失败），这个错误将取消所有绑定，当用户输入一个新的关键字时，是无法发起新的网络请求。
* 如果 `fetchAutoCompleteItems` 在后台返回序列，那么刷新页面也会在后台进行，这样就会出现异常崩溃。
* 返回的结果被绑定到两个 UI 元素上。那就意味着，每次用户输入一个新的关键字时，就会分别为两个 UI 元素发起 HTTP 请求，这并不是我们想要的结果。

一个更好的方案是这样的：

```swift
let results = query.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .observeOn(MainScheduler.instance)  // 结果在主线程返回
            .catchErrorJustReturn([])           // 错误被处理了，这样至少不会终止整个序列
    }
    .share(replay: 1)                             // HTTP 请求是被共享的

results
    .map { "\($0.count)" }
    .bind(to: resultCount.rx.text)
    .disposed(by: disposeBag)

results
    .bind(to: resultsTableView.rx.items(cellIdentifier: "Cell")) {
      (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .disposed(by: disposeBag)
```

在一个大型系统内，要确保每一步不被遗漏是一件不太容易的事情。所以更好的选择是合理运用编译器和特征序列来确保这些必备条件都已经满足。

以下是使用 **Driver** 优化后的代码：

```swift
let results = query.rx.text.asDriver()        // 将普通序列转换为 Driver
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .asDriver(onErrorJustReturn: [])  // 仅仅提供发生错误时的备选返回值
    }

results
    .map { "\($0.count)" }
    .drive(resultCount.rx.text)               // 这里改用 `drive` 而不是 `bindTo`
    .disposed(by: disposeBag)                 // 这样可以确保必备条件都已经满足了

results
    .drive(resultsTableView.rx.items(cellIdentifier: "Cell")) {
      (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .disposed(by: disposeBag)
```

首先第一个 `asDriver` 方法将 [ControlProperty](/content/rxswift_core/observable_and_observer/control_property.md) 转换为 **Driver**

然后第二个变化是：

```swift
.asDriver(onErrorJustReturn: [])
```

任何可监听序列都可以被转换为 `Driver`，只要他满足 3 个条件：

* 不会产生 `error` 事件
* 一定在 `MainScheduler` 监听（主线程监听）
* 共享附加作用

那么要如何确定条件都被满足？通过 Rx 操作符来进行转换。`asDriver(onErrorJustReturn: [])` 相当于以下代码：

```swift
let safeSequence = xs
  .observeOn(MainScheduler.instance)       // 主线程监听
  .catchErrorJustReturn(onErrorJustReturn) // 无法产生错误
  .share(replay: 1, scope: .whileConnected)// 共享附加作用
return Driver(raw: safeSequence)           // 封装
```

最后使用 `drive` 而不是 `bindTo`

`drive` 方法只能被 `Driver` 调用。这意味着，如果你发现代码所存在 `drive`，那么这个序列不会产生错误事件并且一定在主线程监听。这样你可以安全的绑定 UI 元素。
