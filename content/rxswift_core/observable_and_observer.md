## Observable & Observer 既是可监听序列也是观察者

![](/assets/ObservableAndObserver/ObservableAndObserver.png)

在我们所遇到的事物中，有一部分非常特别。它们既是**可监听序列**也是**观察者**。

例如：`textField`的当前文本。它可以看成是由用户输入，而产生的一个**文本序列**。也可以是由外部文本序列，来控制当前显示内容的**观察者**：

```swift
// 作为可监听序列
let observable = textField.rx.text
observable.subscribe(onNext: { text in show(text: text) })
```

```swift
// 作为观察者
let observer = textField.rx.text
let text: Observable<String?> = ...
text.bind(to: observer)
```

有许多 UI 控件都存在这种特性，例如：`switch`的开关状态，`segmentedControl`的选中索引号，`datePicker`的选中日期等等。

## 参考

另外，框架里面定义了一些辅助类型，它们既是**可监听序列**也是**观察者**。如果你能合适的应用这些辅助类型，它们就可以帮助你更准确的描述事物的特征：

* [AsyncSubject](observable_and_observer/async_subject.md)
* [PublishSubject](observable_and_observer/publish_subject.md)
* [ReplaySubject](observable_and_observer/replay_subject.md)
* [BehaviorSubject](observable_and_observer/behavior_subject.md)
* [Variable](observable_and_observer/variable.md)
* [ControlProperty](observable_and_observer/control_property.md)
