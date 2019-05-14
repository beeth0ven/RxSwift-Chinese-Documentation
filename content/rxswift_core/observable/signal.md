# Signal

**Signal** 和 [Driver] 相似，唯一的区别是，[Driver] **会**对新观察者回放（重新发送）上一个元素，而 **Signal** **不会**对新观察者回放上一个元素。

他有如下特性:

* 不会产生 `error` 事件
* 一定在 `MainScheduler` 监听（主线程监听）
* 共享[附加作用]

现在，我们来看看以下代码是否合理：

```swift
let textField: UITextField = ...
let nameLabel: UILabel = ...
let nameSizeLabel: UILabel = ...

let state: Driver<String?> = textField.rx.text.asDriver()

let observer = nameLabel.rx.text
state.drive(observer)

// ... 假设以下代码是在用户输入姓名后运行

let newObserver = nameSizeLabel.rx.text
state.map { $0?.count.description }.drive(newObserver)
```

这个例子只是将用户输入的姓名绑定到对应的标签上。当用户输入姓名后，我们创建了一个新的观察者，用于订阅姓名的字数。那么问题来了，订阅时，展示字数的标签会立即更新吗？

嗯、、、 因为 [Driver] 会对新观察者回放上一个元素（当前姓名），所以这里是会更新的。在对他进行订阅时，标签的默认文本会被刷新。这是合理的。

那如果我们用 [Driver] 来描述点击事件呢，这样合理吗？

```swift
let button: UIButton = ...
let showAlert: (String) -> Void = ...

let event: Driver<Void> = button.rx.tap.asDriver()

let observer: () -> Void = { showAlert("弹出提示框1") }
event.drive(onNext: observer)

// ... 假设以下代码是在用户点击 button 后运行

let newObserver: () -> Void = { showAlert("弹出提示框2") }
event.drive(onNext: newObserver)
```

当用户点击一个按钮后，我们创建一个新的观察者，来响应点击事件。此时会发生什么？[Driver] 会把上一次的点击事件回放给新观察者。所以，这里的 `newObserver` 在订阅时，就会接受到上次的点击事件，然后弹出提示框。这似乎不太合理。

因此像这类型的**事件序列**，用 [Driver] 建模就不合适。于是我们就引入了 **Signal**:

```swift
...

let event: Signal<Void> = button.rx.tap.asSignal()

let observer: () -> Void = { showAlert("弹出提示框1") }
event.emit(onNext: observer)

// ... 假设以下代码是在用户点击 button 后运行

let newObserver: () -> Void = { showAlert("弹出提示框2") }
event.emit(onNext: newObserver)
```

在同样的场景中，**Signal** 不会把上一次的点击事件回放给新观察者，而只会将订阅后产生的点击事件，发布给新观察者。这正是我们所需要的。

### 结论
一般情况下**状态序列**我们会选用 **[Driver]** 这个类型，**事件序列**我们会选用 **Signal** 这个类型。

### 参考

* [Driver]
* [map]

[Driver]:/content/rxswift_core/observable/driver.md
[附加作用]:/content/recipes/pure_function.md
[map]:/content/decision_tree/map.md
