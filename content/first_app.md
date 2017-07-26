# 我的第一个 RxSwift 应用程序

## 我们现在就用 RxSwift 来写一个小程序 - 输入验证：

你可以在这个[文件夹](https://github.com/ReactiveX/RxSwift/tree/master/RxExample/RxExample/Examples/SimpleValidation)找到该例子。

![](/assets/SimpleValid/SimpleValidationFull.png)

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
          .map { $0.characters.count >= minimalUsernameLength }
          .shareReplay(1)

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

* 当密码输入不到 5 个字时给出提示文字

  ![](/assets/SimpleValid/PasswordValid.png)

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      ...

      // 密码是否有效
      let passwordValid = passwordOutlet.rx.text.orEmpty
          // 密码 -> 密码是否有效
          .map { $0.characters.count >= minimalPasswordLength }
          .shareReplay(1)

      ...

      // 密码是否有效 -> 密码提示语是否隐藏
      passwordValid
          .bind(to: passwordValidOutlet.rx.isHidden)
          .disposed(by: disposeBag)

      ...
  }
  ```

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
          .shareReplay(1)

      ...

      // 所有输入是否有效 -> 绿色按钮是否可点击
      everythingValid
          .bind(to: doSomethingOutlet.rx.isEnabled)
          .disposed(by: disposeBag)

      ...
  }
  ```

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

这样 4 个交互都完成了，现在我们纵观全局看下到底是一个什么样的结构:

  ![](/assets/SimpleValid/All.png)

然后看一下完整的代码:

  ```swift
  override func viewDidLoad() {
      super.viewDidLoad()

      usernameValidOutlet.text = "Username has to be at least \(minimalUsernameLength) characters"
      passwordValidOutlet.text = "Password has to be at least \(minimalPasswordLength) characters"

      let usernameValid = usernameOutlet.rx.text.orEmpty
          .map { $0.characters.count >= minimalUsernameLength }
          .shareReplay(1)

      let passwordValid = passwordOutlet.rx.text.orEmpty
          .map { $0.characters.count >= minimalPasswordLength }
          .shareReplay(1)

      let everythingValid = Observable.combineLatest(
            usernameValid,
            passwordValid
          ) { $0 && $1 }
          .shareReplay(1)

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
