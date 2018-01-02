# 你好 RxSwift！

## 我的第一个 RxSwift 应用程序 - 输入验证：

这是一个模拟用户登录的程序。
* 当用户输入用户名时，如果用户名不足 5 个字就给出红色提示语，并且无法输入密码，当用户名符合要求时才可以输入密码。
* 同样的当用户输入的密码不到 5 个字时也给出红色提示语。
* 当用户名和密码有一个不符合要求时底部的绿色按钮不可点击，只有当用户名和密码同时有效时按钮才可点击。
* 当点击绿色按钮后弹出一个提示框，这个提示框只是用来做演示而已。

你可以下载[这个例子](https://github.com/ReactiveX/RxSwift/tree/master/RxExample/RxExample/Examples/SimpleValidation)并在模拟器上运行，这样可以帮助于你理解整个程序的交互：

  ![](/assets/SimpleValid/SimpleValidationFull.gif)

### 这个页面主要由 5 各元素组成：

  1. 用户名输入框
  2. 用户名提示语（红色）
  3. 密码输入框
  4. 密码提示语（红色）
  5. 操作按钮（绿色）

```swift
class SimpleValidationViewController : ViewController {

    @IBOutlet weak var usernameOutlet: UITextField!
    @IBOutlet weak var usernameValidOutlet: UILabel!

    @IBOutlet weak var passwordOutlet: UITextField!
    @IBOutlet weak var passwordValidOutlet: UILabel!

    @IBOutlet weak var doSomethingOutlet: UIButton!
    ...
}
```

### 这里需要完成 4 个交互：

* 当用户名输入不到 5 个字时显示提示语，并且无法输入密码

  ![](/assets/SimpleValid/UsernameValid.png)

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      ...

      // 用户名是否有效
      let usernameValid = usernameOutlet.rx.text.orEmpty
          // 用户名 -> 用户名是否有效
          .map { $0.count >= minimalUsernameLength }
          .share(replay: 1)

      ...

      // 用户名是否有效 -> 密码输入框是否可用
      usernameValid
          .bind(to: passwordOutlet.rx.isEnabled)  
          .disposed(by: disposeBag)

      // 用户名是否有效 -> 用户名提示语是否隐藏
      usernameValid
          .bind(to: usernameValidOutlet.rx.isHidden)
          .disposed(by: disposeBag)

      ...
  }
  ```

  当用户修改用户名输入框的内容时就会产生一个新的用户名, 然后通过 `map` 方法将它转化成用户名是否有效, 最后通过 `bind(to: ...)` 来决定密码输入框是否可用以及提示语是否隐藏。

* 当密码输入不到 5 个字时显示提示文字

  ![](/assets/SimpleValid/PasswordValid.png)

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      ...

      // 密码是否有效
      let passwordValid = passwordOutlet.rx.text.orEmpty
          // 密码 -> 密码是否有效
          .map { $0.count >= minimalPasswordLength }
          .share(replay: 1)

      ...

      // 密码是否有效 -> 密码提示语是否隐藏
      passwordValid
          .bind(to: passwordValidOutlet.rx.isHidden)
          .disposed(by: disposeBag)

      ...
  }
  ```
  这个和用用户名来控制提示语的逻辑是一样的。

* 当用户名和密码都符合要求时，绿色按钮才可点击

  ![](/assets/SimpleValid/LoginButtonValid.png)

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      ...

      // 用户名是否有效
      let usernameValid = ...

      // 密码是否有效
      let passwordValid = ...

      ...

      // 所有输入是否有效
      let everythingValid = Observable.combineLatest(
            usernameValid,
            passwordValid
          ) { $0 && $1 } // 取用户名和密码同时有效
          .share(replay: 1)

      ...

      // 所有输入是否有效 -> 绿色按钮是否可点击
      everythingValid
          .bind(to: doSomethingOutlet.rx.isEnabled)
          .disposed(by: disposeBag)

      ...
  }
  ```
  通过 `Observable.combineLatest(...) { ... }` 来将`用户名是否有效`以及`密码是都有效`合并出`两者是否同时有效`,然后用它来控制绿色按钮是否可点击。

* 点击绿色按钮后，弹出一个提示框

  ![](/assets/SimpleValid/LoginAction.png)

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      ...

      // 点击绿色按钮 -> 弹出提示框
      doSomethingOutlet.rx.tap
          .subscribe(onNext: { [weak self] in self?.showAlert() })
          .disposed(by: disposeBag)
  }

  func showAlert() {
      let alertView = UIAlertView(
          title: "RxExample",
          message: "This is wonderful",
          delegate: nil,
          cancelButtonTitle: "OK"
      )

      alertView.show()
  }
  ```

  在点击绿色按钮后，弹出一个提示框

这样 4 个交互都完成了，现在我们纵观全局看下这个程序是一个什么样的结构:

  ![](/assets/SimpleValid/All.png)

然后看一下完整的代码:

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    usernameValidOutlet.text = "Username has to be at least \(minimalUsernameLength) characters"
    passwordValidOutlet.text = "Password has to be at least \(minimalPasswordLength) characters"

    let usernameValid = usernameOutlet.rx.text.orEmpty
        .map { $0.count >= minimalUsernameLength }
        .share(replay: 1)

    let passwordValid = passwordOutlet.rx.text.orEmpty
        .map { $0.count >= minimalPasswordLength }
        .share(replay: 1)

    let everythingValid = Observable.combineLatest(
          usernameValid,
          passwordValid
        ) { $0 && $1 }
        .share(replay: 1)

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

func showAlert() {
    let alertView = UIAlertView(
        title: "RxExample",
        message: "This is wonderful",
        delegate: nil,
        cancelButtonTitle: "OK"
    )

    alertView.show()
}
```

你会发现你可以用几行代码完成如此复杂的交互。这可以大大提升我们的开发效率。

### 更多疑问

* `share(replay: 1)` 是用来做什么的？

  我们用 `usernameValid` 来控制用户名提示语是否隐藏以及密码输入框是否可用。[shareReplay] 就是让他们共享这一个源，而不是为他们单独创建新的源。这样可以减少不必要的开支。

* `disposed(by: disposeBag)` 是用来做什么的？

  和我们所熟悉的对象一样，每一个绑定也是有生命周期的。并且这个绑定是可以被清除的。`disposed(by: disposeBag)`就是将绑定的生命周期交给 `disposeBag` 来管理。当 `disposeBag` 被释放的时候，那么里面尚未清除的绑定也就被清除了。这就相当于是在用 [ARC](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html#//apple_ref/doc/uid/TP40014097-CH20-ID48) 来管理绑定的生命周期。 这个内容会在 [Disposable](rxswift_core/disposable.md) 章节详细介绍。

[shareReplay]:rxswift_core/operator/shareReplay.md
