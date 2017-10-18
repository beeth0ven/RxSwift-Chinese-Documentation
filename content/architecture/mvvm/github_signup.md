## Github Signup

![](/assets/Architecture/MVVM/GithubSignupFull.gif)

这是一个模拟用户注册的程序，你可以在这里下载[这个例子](https://github.com/ReactiveX/RxSwift/tree/master/RxExample/RxExample/Examples/GitHubSignup/UsingVanillaObservables)。

---

### 简介

这个 App 主要有这样几个交互：

* 当用户输入户名时，验证用户名是否有效，是否已被占用，将验证结果显示出来。
* 当用户输入密码时，验证密码是否有效，将验证结果显示出来。
* 当用户输入重复密码时，验证重复密码是否相同，将验证结果显示出来。
* 当所有验证都有效时，注册按钮才可点击。
* 当点击注册按钮后发起注册请求（模拟），然后将结果显示出来。

---

### Service

![](/assets/Architecture/MVVM/Service.png)

```swift
// GitHub 网络服务
protocol GitHubAPI {
    func usernameAvailable(_ username: String) -> Observable<Bool>
    func signup(_ username: String, password: String) -> Observable<Bool>
}

// 输入验证服务
protocol GitHubValidationService {
    func validateUsername(_ username: String) -> Observable<ValidationResult>
    func validatePassword(_ password: String) -> ValidationResult
    func validateRepeatedPassword(_ password: String, repeatedPassword: String) -> ValidationResult
}

// 弹框服务
protocol Wireframe {
    func open(url: URL)
    func promptFor<Action: CustomStringConvertible>(_ message: String, cancelAction: Action, actions: [Action]) -> Observable<Action>
}
```

这里需要集成三个服务：

* **GitHubAPI** 提供 GitHub 网络服务
* **GitHubValidationService** 提供输入验证服务
* **Wireframe** 提供弹框服务



这个例子目前只提供了这三个服务，实际上这一层还可以包含其他的一些服务，例如：数据库，定位，蓝牙...

---

### ViewModel

![](/assets/Architecture/MVVM/GithubSignupViewModel.png)

**ViewModel** 需要集成这些服务，并且将用户输入，转换为状态输出：

```swift
class GithubSignupViewModel1 {

    // 输出
    let validatedUsername: Observable<ValidationResult>
    let validatedPassword: Observable<ValidationResult>
    let validatedPasswordRepeated: Observable<ValidationResult>
    let signupEnabled: Observable<Bool>
    let signedIn: Observable<Bool>
    let signingIn: Observable<Bool>

    // 输入 -> 输出
    init(input: (       // 输入
            username: Observable<String>,
            password: Observable<String>,
            repeatedPassword: Observable<String>,
            loginTaps: Observable<Void>
        ),
        dependency: (   // 服务
            API: GitHubAPI,
            validationService: GitHubValidationService,
            wireframe: Wireframe
        )
    ) {
        ...

        validatedUsername = ...

        validatedPassword = ...

        validatedPasswordRepeated = ...

        ...

        self.signingIn = ...

        ...

        signedIn = ...

        signupEnabled = ...
    }
}
```

集成服务：

* **API** GitHub 网络服务
* **validationService** 输入验证服务
* **wireframe** 弹框服务

输入：

* **username** 输入的用户名
* **password** 输入的密码
* **repeatedPassword** 重复输入的密码
* **loginTaps** 点击登录按钮

输出：

* **validatedUsername** 用户名校验结果
* **validatedPassword** 密码校验结果
* **validatedPasswordRepeated** 重复密码校验结果
* **signupEnabled** 是否允许登录
* **signedIn** 登录结果
* **signingIn** 是否正在登录

在 `init` 方法内部，将输入转换为输出。

---

### ViewController

![](/assets/Architecture/MVVM/GitHubSignupViewController.png)

**ViewController** 主要负责数据绑定：

```swift
...
class GitHubSignupViewController1 : ViewController {
    @IBOutlet weak var usernameOutlet: UITextField!
    @IBOutlet weak var usernameValidationOutlet: UILabel!

    @IBOutlet weak var passwordOutlet: UITextField!
    @IBOutlet weak var passwordValidationOutlet: UILabel!

    @IBOutlet weak var repeatedPasswordOutlet: UITextField!
    @IBOutlet weak var repeatedPasswordValidationOutlet: UILabel!

    @IBOutlet weak var signupOutlet: UIButton!
    @IBOutlet weak var signingUpOulet: UIActivityIndicatorView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let viewModel = GithubSignupViewModel1(
            input: (
                username: usernameOutlet.rx.text.orEmpty.asObservable(),
                password: passwordOutlet.rx.text.orEmpty.asObservable(),
                repeatedPassword: repeatedPasswordOutlet.rx.text.orEmpty.asObservable(),
                loginTaps: signupOutlet.rx.tap.asObservable()
            ),
            dependency: (
                API: GitHubDefaultAPI.sharedAPI,
                validationService: GitHubDefaultValidationService.sharedValidationService,
                wireframe: DefaultWireframe.shared
            )
        )

        // bind results to  {
        viewModel.signupEnabled
            .subscribe(onNext: { [weak self] valid  in
                self?.signupOutlet.isEnabled = valid
                self?.signupOutlet.alpha = valid ? 1.0 : 0.5
            })
            .disposed(by: disposeBag)

        viewModel.validatedUsername
            .bind(to: usernameValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.validatedPassword
            .bind(to: passwordValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.validatedPasswordRepeated
            .bind(to: repeatedPasswordValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.signingIn
            .bind(to: signingUpOulet.rx.isAnimating)
            .disposed(by: disposeBag)

        viewModel.signedIn
            .subscribe(onNext: { signedIn in
                print("User signed in \(signedIn)")
            })
            .disposed(by: disposeBag)
        //}

        let tapBackground = UITapGestureRecognizer()
        tapBackground.rx.event
            .subscribe(onNext: { [weak self] _ in
                self?.view.endEditing(true)
            })
            .disposed(by: disposeBag)
        view.addGestureRecognizer(tapBackground)
    }
}

```

将**用户行为**传入给 **ViewModel**：

* **username** 将用户名输入框的当前文本传入
* **password** 将密码输入框的当前文本传入
* **...**

将 **ViewModel** 的**输出状态**显示出来：

* **validatedUsername** 用对应的 `label` 将用户名验证结果显示出来
* **validatedPassword** 用对应的 `label` 将密码验证结果显示出来
* **...**

### 整体结构

以下是全部的核心代码：

```swift
// ViewModel
class GithubSignupViewModel1 {
    // outputs {

    let validatedUsername: Observable<ValidationResult>
    let validatedPassword: Observable<ValidationResult>
    let validatedPasswordRepeated: Observable<ValidationResult>

    // Is signup button enabled
    let signupEnabled: Observable<Bool>

    // Has user signed in
    let signedIn: Observable<Bool>

    // Is signing process in progress
    let signingIn: Observable<Bool>

    // }

    init(input: (
            username: Observable<String>,
            password: Observable<String>,
            repeatedPassword: Observable<String>,
            loginTaps: Observable<Void>
        ),
        dependency: (
            API: GitHubAPI,
            validationService: GitHubValidationService,
            wireframe: Wireframe
        )
    ) {
        let API = dependency.API
        let validationService = dependency.validationService
        let wireframe = dependency.wireframe

        /**
         Notice how no subscribe call is being made.
         Everything is just a definition.

         Pure transformation of input sequences to output sequences.
        */

        validatedUsername = input.username
            .flatMapLatest { username in
                return validationService.validateUsername(username)
                    .observeOn(MainScheduler.instance)
                    .catchErrorJustReturn(.failed(message: "Error contacting server"))
            }
            .share(replay: 1)

        validatedPassword = input.password
            .map { password in
                return validationService.validatePassword(password)
            }
            .share(replay: 1)

        validatedPasswordRepeated = Observable.combineLatest(input.password, input.repeatedPassword, resultSelector: validationService.validateRepeatedPassword)
            .share(replay: 1)

        let signingIn = ActivityIndicator()
        self.signingIn = signingIn.asObservable()

        let usernameAndPassword = Observable.combineLatest(input.username, input.password) { ($0, $1) }

        signedIn = input.loginTaps.withLatestFrom(usernameAndPassword)
            .flatMapLatest { (username, password) in
                return API.signup(username, password: password)
                    .observeOn(MainScheduler.instance)
                    .catchErrorJustReturn(false)
                    .trackActivity(signingIn)
            }
            .flatMapLatest { loggedIn -> Observable<Bool> in
                let message = loggedIn ? "Mock: Signed in to GitHub." : "Mock: Sign in to GitHub failed"
                return wireframe.promptFor(message, cancelAction: "OK", actions: [])
                    // propagate original value
                    .map { _ in
                        loggedIn
                    }
            }
            .share(replay: 1)

        signupEnabled = Observable.combineLatest(
            validatedUsername,
            validatedPassword,
            validatedPasswordRepeated,
            signingIn.asObservable()
        )   { username, password, repeatPassword, signingIn in
                username.isValid &&
                password.isValid &&
                repeatPassword.isValid &&
                !signingIn
            }
            .distinctUntilChanged()
            .share(replay: 1)
    }
}

// ViewController
class GitHubSignupViewController1 : ViewController {
    @IBOutlet weak var usernameOutlet: UITextField!
    @IBOutlet weak var usernameValidationOutlet: UILabel!

    @IBOutlet weak var passwordOutlet: UITextField!
    @IBOutlet weak var passwordValidationOutlet: UILabel!

    @IBOutlet weak var repeatedPasswordOutlet: UITextField!
    @IBOutlet weak var repeatedPasswordValidationOutlet: UILabel!

    @IBOutlet weak var signupOutlet: UIButton!
    @IBOutlet weak var signingUpOulet: UIActivityIndicatorView!

    override func viewDidLoad() {
        super.viewDidLoad()

        let viewModel = GithubSignupViewModel1(
            input: (
                username: usernameOutlet.rx.text.orEmpty.asObservable(),
                password: passwordOutlet.rx.text.orEmpty.asObservable(),
                repeatedPassword: repeatedPasswordOutlet.rx.text.orEmpty.asObservable(),
                loginTaps: signupOutlet.rx.tap.asObservable()
            ),
            dependency: (
                API: GitHubDefaultAPI.sharedAPI,
                validationService: GitHubDefaultValidationService.sharedValidationService,
                wireframe: DefaultWireframe.shared
            )
        )

        // bind results to  {
        viewModel.signupEnabled
            .subscribe(onNext: { [weak self] valid  in
                self?.signupOutlet.isEnabled = valid
                self?.signupOutlet.alpha = valid ? 1.0 : 0.5
            })
            .disposed(by: disposeBag)

        viewModel.validatedUsername
            .bind(to: usernameValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.validatedPassword
            .bind(to: passwordValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.validatedPasswordRepeated
            .bind(to: repeatedPasswordValidationOutlet.rx.validationResult)
            .disposed(by: disposeBag)

        viewModel.signingIn
            .bind(to: signingUpOulet.rx.isAnimating)
            .disposed(by: disposeBag)

        viewModel.signedIn
            .subscribe(onNext: { signedIn in
                print("User signed in \(signedIn)")
            })
            .disposed(by: disposeBag)
        //}

        let tapBackground = UITapGestureRecognizer()
        tapBackground.rx.event
            .subscribe(onNext: { [weak self] _ in
                self?.view.endEditing(true)
            })
            .disposed(by: disposeBag)
        view.addGestureRecognizer(tapBackground)
    }
}
```

[这里](https://github.com/ReactiveX/RxSwift/tree/master/RxExample/RxExample/Examples/GitHubSignup/UsingDriver)还有一个 **Driver** 版的演示代码，有兴趣的同学可以了解一下。
