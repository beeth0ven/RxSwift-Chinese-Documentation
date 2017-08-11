## Variable

在 **Swift** 中我们经常会用到 **var** 关键字来声明变量。**RxSwift** 提供的 **Variable** 实际上是 **var** 的 **Rx** 版本，你可以将它看作是 **RxVar。**

我们来对比一下 **var** 以及 **Variable** 的用法：

**使用 var：**

```swift
// 在 ViewController 中
var model: Model? = nil {
    didSet { updateUI(with: model) }
}

override func viewDidLoad() {
    super.viewDidLoad()

    model = getModel()
}

func updateUI(with model: Model?) { ... }
func getModel() -> Model { ... }
```

**使用 Variable：**

```swift
// 在 ViewController 中
let model: Variable<Model?> = Variable(nil)

override func viewDidLoad() {
    super.viewDidLoad()

    model.asObservable()
        .subscribe(onNext: { [weak self] model in
            self?.updateUI(with: model)
        })
        .disposed(by: disposeBag)

    model.value = getModel()
}

func updateUI(with model: Model?) { ... }
func getModel() -> Model { ... }
```

第一种使用 **var** 的方式十分常见，在 `ViewController` 中监听 `Model` 的变化，然后刷新页面。

第二种使用 **Variable** 则是 **RxSwift** 独有的。**Variable** 几乎提供了 **var** 的所有功能。另外，加上一条非常重要的特性，就是可以通过调用 `asObservable()` 方法转换成 `Observable`。所以，如果你声明的变量需要提供 **Rx** 支持，那就选用 **Variable** 这个类型。

### 说明
**Variable** 封装了一个 [BehaviorSubject](behavior_subject.md)，所以它会持有当前值，并且 **Variable** 会对新的观察者发送当前值。它不会产生 `error` 事件。**Variable** 在 `deinit` 时，会发出一个 `completed` 事件。
