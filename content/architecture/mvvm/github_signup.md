## Github Signup

![](/assets/Architecture/MVVM/GithubSignupFull.gif)

这是一个模拟用户注册的程序。

---

### 简介

这个 App 主要有这样几个交互：

* 当用户输入户名时，验证用户名是否有效，是否已被占用，将验证结果显示出来。
* 当用户输入密码时，验证密码是否有效，将验证结果显示出来。
* 当用户输入重复密码时，验证重复密码是否相同，将验证结果显示出来。
* 当所有验证都有效时，按钮才可点击。
* 当点击绿色按钮后发起注册请求（模拟），然后将注册结果显示出来。

---

### Service

![](/assets/Architecture/MVVM/Service.png)

这里需要集成三个服务：

* GitHubAPI 提供 GitHub 网络服务
* GitHubValidationService 提供输入验证服务
* Wireframe 提供弹框服务

这个例子目前只提供了这三个服务，实际上这一层可以包含其他的许多服务，例如：数据库，定位，蓝牙...

---

### ViewModel

![](/assets/Architecture/MVVM/GithubSignupViewModel.png)


---

### ViewController
