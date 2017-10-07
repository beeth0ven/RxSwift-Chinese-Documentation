## Binder

**Binder** 主要有以下两个特征：

* 不会处理错误事件
* 确保绑定都是在给定 [Scheduler] 上执行（默认 **MainScheduler**）

一旦产生错误事件，在调试环境下将执行 `fatalError`，在发布环境下将打印错误信息。

---

### 示例

在介绍 [AnyObserver] 时，我们举了这样一个例子：

```swift
let observer: AnyObserver<Bool> = AnyObserver { [weak self] (event) in
    switch event {
    case .next(let isHidden):
        self?.usernameValidOutlet.isHidden = isHidden
    default:
        break
    }
}

usernameValid
    .bind(to: observer)
    .disposed(by: disposeBag)
```

由于这个观察者是一个 **UI 观察者**，所以它在响应事件时，只会处理 `next` 事件，并且更新 **UI** 的操作需要在主线程上执行。

因此一个更好的方案就是使用 **Binder**：

```swift
let observer: Binder<Bool> = Binder(usernameValidOutlet) { (view, isHidden) in
    view.isHidden = isHidden
}

usernameValid
    .bind(to: observer)
    .disposed(by: disposeBag)
```

**Binder** 可以只处理 `next` 事件，并且保证响应 `next` 事件的代码一定会在给定 [Scheduler] 上执行，这里采用默认的 **MainScheduler**。

---
### 复用

由于**页面是否隐藏**是一个常用的观察者，所以应该让所有的 `UIView` 都提供这种观察者：

```swift
extension Reactive where Base: UIView {
  public var isHidden: Binder<Bool> {
      return Binder(self.base) { view, hidden in
          view.isHidden = hidden
      }
  }
}
```

```swift
usernameValid
    .bind(to: usernameValidOutlet.rx.isHidden)
    .disposed(by: disposeBag)
```

这样你不必为每个 **UI** 控件单独创建该观察者。这就是 `usernameValidOutlet.rx.isHidden` 的由来，许多 **UI 观察者** 都是这样创建的：

* **按钮是否可点击 `button.rx.isEnabled`：**
```swift
extension Reactive where Base: UIControl {
    public var isEnabled: Binder<Bool> {
        return Binder(self.base) { control, value in
            control.isEnabled = value
        }
    }
}
```

* **`label` 的当前文本 `label.rx.text`：**
```swift
extension Reactive where Base: UILabel {
    public var text: Binder<String?> {
        return Binder(self.base) { label, text in
            label.text = text
        }
    }
}
```

你也可以用这种方式来创建自定义的 **UI 观察者**。

[AnyObserver]:any_observer.md
[Scheduler]:/content/rxswift_core/schedulers.md
