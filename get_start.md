## 必备条件

* Xcode 8.0

* Swift 3.0

### [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html)

已通过测试** **`pod --version`**: **`1.1.1`

```ruby
# Podfile
use_frameworks!

target 'YOUR_TARGET_NAME' do
    pod 'RxSwift',    '~> 3.0'
    pod 'RxCocoa',    '~> 3.0'
end
```

替换 `YOUR_TARGET_NAME` 然后在 `Podfile` 目录下, 终端输入：

```bash
$ pod install
```

### [Carthage](https://github.com/Carthage/Carthage)

已通过测试** **`carthage version`**: **`0.18.1`

添加到 `Cartfile`

```
github "ReactiveX/RxSwift" ~> 3.0
```

```bash
$ carthage update
```

### [Swift Package Manager](https://github.com/apple/swift-package-manager)

已通过测试**  **`swift build --version`**: **`3.0.0 (swiftpm-19)`

创建`Package.swift` 文件。

```swift
import PackageDescription

let package = Package(
    name: "RxTestProject",
    targets: [],
    dependencies: [
        .Package(url: "https://github.com/ReactiveX/RxSwift.git", majorVersion: 3)
    ]
)
```

```bash
$ swift build
```

### 使用 submodules 手动集成

* 添加 RxSwift 作为子模块

```bash
$ git submodule add git@github.com:ReactiveX/RxSwift.git
```

* 拖拽 `Rx.xcodeproj` 到项目中
* 前往 `Project > Targets > Build Phases > Link Binary With Libraries`, 点击 `+` 并且选中 `RxSwift-[Platform]` 和 `RxCocoa-[Platform]` 



