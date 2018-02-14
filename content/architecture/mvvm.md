## MVVM

**MVVM** 是 **Model-View-ViewModel** 的简写。如果你已经对 [MVC] 非常熟悉了，那么上手 **MVVM** 也是非常容易的。

---

### MVC

![](/assets/Architecture/MVVM/MVC.png)

**MVC** 是 **Model-View-Controller** 的简写。**MVC** 主要有三层：

  * **Model** 数据层，读写数据，保存 App 状态
  * **View** 页面层，和用户交互，向用户显示页面，反馈用户行为
  * **ViewController** 逻辑层，更新数据，或者页面，处理业务逻辑

**MVC** 可以帮助你很好的将数据，页面，逻辑的代码分离开来。使得每一层相对独立。这样你就能够将一些可复用的功能抽离出来，化繁为简。只不过，一旦 App 的交互变复杂，你就会发现 **ViewController** 将变得十分臃肿。大量代码被添加到**控制器**中，使得**控制器**负担过重。此时，你就需要想办法将**控制器**里面的代码进一步地分离出来，对 APP 进行重新分层。而 **MVVM** 就是一种进阶的分层方案。

---

### MVVM

![](/assets/Architecture/MVVM/MVVM.png)

**MVVM** 和 **MVC** 十分相识。只不过他的分层更加详细：

* **Model** 数据层，读写数据，保存 App 状态
* **View** 页面层，提供用户输入行为，并且显示输出状态
* **ViewModel** 逻辑层，它将用户输入行为，转换成输出状态
* **ViewController** 主要负责数据绑定

没错，**ViewModel** 现在是逻辑层，而**控制器**只需要负责数据绑定。如此一来**控制器**的负担就减轻了许多。并且 **ViewModel** 与**控制器**以及**页面**相独立。那么，你就可以跨平台使用它。你也可以很容易地测试它。

---

### 示例

这里我们将用 **MVVM** 来重构[输入验证](/content/first_app.md)。

![](/assets/SimpleValid/SimpleValidationFull.gif)

重构前：

```swift
class SimpleValidationViewController : ViewController {

    ...

    override func viewDidLoad() {
        super.viewDidLoad()

        ...

        let usernameValid = usernameOutlet.rx.text.orEmpty
            .map { $0.characters.count >= minimalUsernameLength }
            .share(replay: 1)

        let passwordValid = passwordOutlet.rx.text.orEmpty
            .map { $0.characters.count >= minimalPasswordLength }
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

    ...

}
```

---

### ViewModel

![](/assets/Architecture/MVVM/ViewModel.png)

**ViewModel** 将用户输入行为，转换成输出的状态：

```swift
class SimpleValidationViewModel {

    // 输出
    let usernameValid: Observable<Bool>
    let passwordValid: Observable<Bool>
    let everythingValid: Observable<Bool>

    // 输入 -> 输出
    init(
        username: Observable<String>,
        password: Observable<String>
        ) {

        usernameValid = username
            .map { $0.characters.count >= minimalUsernameLength }
            .share(replay: 1)

        passwordValid = password
            .map { $0.characters.count >= minimalPasswordLength }
            .share(replay: 1)

        everythingValid = Observable.combineLatest(usernameValid, passwordValid) { $0 && $1 }
            .share(replay: 1)

    }
}
```

输入：

* username 输入的用户名
* passwordValid 输入的密码

输出:

* usernameValid 用户名是否有效
* passwordValid 密码是否有效
* everythingValid 所有输入是否有效

在 `init` 方法内部，将输入转换为输出。

---

### ViewController

**ViewController** 主要负责数据绑定：

```swift
class SimpleValidationViewController : ViewController {

    ...

    private var viewModel: SimpleValidationViewModel!

    override func viewDidLoad() {
        super.viewDidLoad()

        ...

        viewModel = SimpleValidationViewModel(
            username: usernameOutlet.rx.text.orEmpty.asObservable(),
            password: passwordOutlet.rx.text.orEmpty.asObservable()
        )

        viewModel.usernameValid
            .bind(to: passwordOutlet.rx.isEnabled)
            .disposed(by: disposeBag)

        viewModel.usernameValid
            .bind(to: usernameValidOutlet.rx.isHidden)
            .disposed(by: disposeBag)

        viewModel.passwordValid
            .bind(to: passwordValidOutlet.rx.isHidden)
            .disposed(by: disposeBag)

        viewModel.everythingValid
            .bind(to: doSomethingOutlet.rx.isEnabled)
            .disposed(by: disposeBag)

        doSomethingOutlet.rx.tap
            .subscribe(onNext: { [weak self] in self?.showAlert() })
            .disposed(by: disposeBag)
    }

    ...

}
```

输入：
* username 将输入的用户名传入 **ViewModel**
* password 将输入的密码传入 **ViewModel**

输出：
* usernameValid 用用户名是否有效，来控制提示语是否隐藏，密码输入框是否可用
* passwordValid 用密码是否有效，来控制提示语是否隐藏
* everythingValid 用两者是否同时有效，来控制按钮是否可点击

当 App 的交互变复杂时，你仍然可以保持**控制器**结构清晰。这样可以大大的提升代码可读性。将来代码维护起来也就会容易许多了。

---

### 示例

下一节将用 [Github Signup] 来演示如何使用 **MVVM**。

_注意⚠️：这里介绍的 **MVVM** 并不是严格意义上的 **MVVM**。但我们通常都管它叫 **MVVM**，而且它配合 **RxSwift** 使用起来非常方便。如需了解什么是严格意义上的 **MVVM**，请参考**微软**的 [The MVVM Pattern]。_

[MVC]:https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html
[Github Signup]:mvvm/github_signup.md

[The MVVM Pattern]:https://msdn.microsoft.com/en-us/library/hh848246.aspx
