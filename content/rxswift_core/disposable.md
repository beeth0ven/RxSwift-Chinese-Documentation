## Disposable - 可被清除的资源

![](/assets/Disposable/Disposable.png)

通常来说，一个序列如果发出了 `error` 或者 `completed` 事件，那么所有内部资源都会被释放。如果你需要提前释放这些资源或取消订阅的话，那么你可以对返回的 **可被清除的资源（Disposable）** 调用 `dispose` 方法：

```swift
var disposable: Disposable?

override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)

    self.disposable = textField.rx.text.orEmpty
        .subscribe(onNext: { text in print(text) })
}

override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)

    self.disposable?.dispose()
}
```

调用 `dispose` 方法后，订阅将被取消，并且内部资源都会被释放。通常情况下，你是不需要手动调用 `dispose` 方法的，这里只是做个演示而已。我们推荐使用 **清除包（DisposeBag）** 或者 **takeUntil 操作符** 来管理订阅的生命周期。

## DisposeBag - 清除包

![](/assets/Disposable/DisposeBag.png)

因为我们用的是 **Swift** ，所以我们更习惯于使用 [ARC](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID48) 来管理内存。那么我们能不能用 **ARC** 来管理订阅的生命周期了。答案是肯定了，你可以用 **清除包（DisposeBag）** 来实现这种订阅管理机制：

```swift
var disposeBag = DisposeBag()

override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)

    textField.rx.text.orEmpty
        .subscribe(onNext: { text in print(text) })
        .disposed(by: self.disposeBag)
}

override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)

    self.disposeBag = DisposeBag()
}
```

当 **清除包** 被释放的时候，**清除包** 内部所有 **可被清除的资源（Disposable）** 都将被清除。在[输入验证](/content/first_app.md)中我们也多次看到 **清除包** 的身影：

```swift
var disposeBag = DisposeBag() // 来自父类 ViewController

override func viewDidLoad() {
    super.viewDidLoad()

    ...

    usernameValid
        .bind(to: passwordOutlet.rx.isEnabled)
        .disposed(by: disposeBag)

    usernameValid
        .bind(to: usernameValidOutlet.rx.isHidden)
        .disposed(by: disposeBag)

    passwordValid
        .bind(to: passwordValidOutlet.rx.isHidden)
        .disposed(by: disposeBag)

    everythingValid
        .bind(to: doSomethingOutlet.rx.isEnabled)
        .disposed(by: disposeBag)

    doSomethingOutlet.rx.tap
        .subscribe(onNext: { [weak self] in self?.showAlert() })
        .disposed(by: disposeBag)
}
```

这个例子中 `disposeBag` 和 `ViewController` 具有相同的生命周期。当退出页面时， `ViewController` 就被释放，`disposeBag` 也跟着被释放了，那么这里的 5 次绑定（订阅）也就被取消了。这正是我们所需要的。

## [takeUntil]

![](/assets/Disposable/TakeUntil.png)

另外一种实现自动取消订阅的方法就是使用 [takeUntil] 操作符，上面那个[输入验证](/content/first_app.md)的演示代码也可以通过使用  [takeUntil] 来实现：

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    ...

    _ = usernameValid
        .takeUntil(self.rx.deallocated)
        .bind(to: passwordOutlet.rx.isEnabled)

    _ = usernameValid
        .takeUntil(self.rx.deallocated)
        .bind(to: usernameValidOutlet.rx.isHidden)

    _ = passwordValid
        .takeUntil(self.rx.deallocated)
        .bind(to: passwordValidOutlet.rx.isHidden)

    _ = everythingValid
        .takeUntil(self.rx.deallocated)
        .bind(to: doSomethingOutlet.rx.isEnabled)

    _ = doSomethingOutlet.rx.tap
        .takeUntil(self.rx.deallocated)
        .subscribe(onNext: { [weak self] in self?.showAlert() })
}

```

这将使得订阅一直持续到控制器的 **dealloc** 事件产生为止。

_注意⚠️：这里配图中所使用的 `Observable` 都是“热” `Observable`，它使我们更容易理解订阅的生命周期。如果你想要了解更多关于 “冷热” `Observable` 之间的区别，可以参考官方文档 [Hot and Cold Observables]。_

[takeUntil]:/content/decision_tree/takeUntil.md
[Hot and Cold Observables]:https://github.com/ReactiveX/RxSwift/blob/master/Documentation/HotAndColdObservables.md
