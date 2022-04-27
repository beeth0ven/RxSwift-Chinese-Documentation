<img src="/assets/WhyRxSwiftAgain/RxSwiftCommunity.png" width="36" height="36">  RxSwift 生态系统
======================================

**RxCocoa** 给 **UI框架** 提供了 [Rx] 支持，让我们能够使用按钮点击序列，输入框当前文本序列等。不过 **RxCocoa** 也只是 **RxSwift 生态系统** 中的一员。**RxSwift 生态系统**还给其他框架提供了 [Rx] 支持：

* [RxDataSources] - UITableView 和 UICollectionView 数据源
* [RxGesture] -  页面手势
* [RxMKMapView] - 地图
* [RxCoreMotion] - 陀螺仪
* [RxAlamofire] - 网络请求
* [RxCoreData] - CoreData 数据库
* [RxRealm] - Realm 数据库
* [RxMediaPicker] - 图片选择器
* [Action] - 行为
* [RxWebKit] - WebView
* [RxEventHub] - 全局通知
* [RxSwiftExt] - 添加一些有用的操作符
* **...**

### [RxDataSources]
书写 `tabelView` 或 `collectionView` 的数据源是一件非常繁琐的事情，有一大堆的代理方法需要被执行。 [RxDataSources] 可以帮助你简化这一过程。你可以用它来布局多层级的列表页，并且它还可以提供动画支持。

![](/assets/WhyRxSwiftAgain/RxDataSources.gif)

你只需要几行代码就可以布局一个多 `Section` 的 `tabelView`：

```swift
let dataSource = RxTableViewSectionedReloadDataSource<SectionModel<String, Int>>()
Observable.just([SectionModel(model: "title", items: [1, 2, 3])])
    .bind(to: tableView.rx.items(dataSource: dataSource))
    .disposed(by: disposeBag)
```

你可以点击 [RxDataSources] 来了解更多信息。

---

### [RxAlamofire]

[Alamofire] 是一个非常流行的网络请求框架。[RxAlamofire] 是用 [RxSwift] 封装的 [Alamofire]。它使得网络请求调用变得更加平滑，处理请求结果变得更简洁，更高效：

```swift
let stringURL = ""

// 使用 NSURLSession
let session = NSURLSession.sharedSession()

_ = session.rx
        .json(.get, stringURL)
        .observeOn(MainScheduler.instance)
        .subscribe { print($0) }

// 使用 Alamofire 引擎

_ = json(.get, stringURL)
    .observeOn(MainScheduler.instance)
    .subscribe { print($0) }

// 使用 Alamofire manager

let manager = Manager.sharedInstance

_ = manager.rx.json(.get, stringURL)
    .observeOn(MainScheduler.instance)
    .subscribe { print($0) }

// URLHTTPResponse + Validation + String
_ = manager.rx.request(.get, stringURL)
    .flatMap {
        $0
            .validate(statusCode: 200 ..< 300)
            .validate(contentType: ["text/json"])
            .rx.string()
    }
    .observeOn(MainScheduler.instance)
    .subscribe { print($0) }
```

你可以点击 [RxAlamofire] 来了解更多信息。

---

### [RxRealm]

[Realm] 是一个十分前卫的**跨平台**数据库，他想要替换 **Core Data** 和 **SQLite**。[RxRealm] 是用 [RxSwift] 封装的 [Realm]。它使我们可以用 **Rx** 的方式监听数据变化，或者将数据写入数据库。

监听数据：

```swift
let realm = try! Realm()
let laps = realm.objects(Lap.self)

Observable.collection(from: laps)
  .map {
    laps in "\(laps.count) laps"
  }
  .subscribe(onNext: { text  in
    print(text)
  })
```

添加数据：

```swift
let realm = try! Realm()
let messages = [Message("hello"), Message("world")]

Observable.from(messages)
  .subscribe(realm.rx.add())
```

删除数据：

```swift
let realm = try! Realm()
let messages = realm.objects(Message.self)

Observable.from(messages)
  .subscribe(realm.rx.delete())
```

你可以点击 [RxRealm] 来了解更多信息。

---

<img src="/assets/WhyRxSwiftAgain/ReactiveX.png" width="36" height="36">  ReactiveX 生态系统
======================================

我们之前提到过 [RxSwift] 是 [Rx] 的 **Swift** 版本。而 [ReactiveX]（简写: Rx）是一个跨平台框架。它不仅可以用来写 **iOS** ，你还可以用它来写 **Android**，**Web 前端**和**后台**。并且每个平台都和 **RxSwift** 一样有一套 [Rx] 生态系统。[Rx] 支持多种编程语言，如：Swift，Java，JS，C#，Scala，Kotlin，Go 等。只要你掌握了其中一门语言，你很容易就能够熟悉其他的语言。

## Android

[RxJava] 是 **Android** 平台上非常流行的响应式编程框架，它也是 [Rx] 的 **Java** 版本。

我们还是用[输入验证](first_app.md)来做演示：

**iOS（RxSwift） 版：**

```swift
...
let usernameValid = usernameOutlet.rx.text.orEmpty
    .map { $0.count >= minimalUsernameLength }
    .share(replay: 1)

let passwordValid = passwordOutlet.rx.text.orEmpty
    .map { $0.count >= minimalPasswordLength }
    .share(replay: 1)

let everythingValid = Observable
    .combineLatest(usernameValid, passwordValid) { $0 && $1 }
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
...
```

**Android（RxJava） 版：**

```java
...
final Observable<Boolean> usernameValid = RxTextView.textChanges(usernameEditText)
        .map(text -> text.length() >= minimalUsernameLength)
        .compose(Rx.shareReplay(1));

final Observable<Boolean> passwordValid = RxTextView.textChanges(usernameEditText)
        .map(text -> text.length() >= minimalPasswordLength)
        .compose(Rx.shareReplay(1));

final Observable<Boolean> everythingValid = Observable
        .combineLatest(usernameValid, passwordValid, (isUsernameValid, isPasswordValid) -> isUsernameValid && isPasswordValid)
        .compose(Rx.shareReplay(1));

disposables.add(usernameValid
        .subscribe(RxView.enabled(passwordEditText)));

disposables.add(usernameValid
        .subscribe(RxView.visibility(usernameValidTextView)));

disposables.add(passwordValid
        .subscribe(RxView.visibility(passwordValidTextView)));

disposables.add(everythingValid
        .subscribe(RxView.enabled(doSomethingButton)));
...
```

这两段代码的逻辑是一样的，一个是 **iOS（RxSwift）** 版本，另一个是 **Android（RxJava）** 版本。仔细对比以后，你会发现它们的书写方式都是差不多的。

这样一来，你就可以用同一套逻辑来写跨平台应用，而且这个应用是纯原生的。这不仅**节省了开发时间**，而且还**提升了 App 的质量**。

---

## Web 前端

[RxJS] 是 **Web 前端** 平台上非常流行的响应式编程框架，它也是 [Rx] 的 **JS** 版本。而且主流的前端框架都提供了 [Rx] 支持，如：[jQuery]，[RxJS-DOM]，[AngularJS]，[RxEmber]等。

下面这个例子是用 [RxJS] 写的，它和 **GitHub 搜索** 十分相似，只不过他搜索的是维基百科：

```javascript
var $input = $('#input'),
    $results = $('#results');

Rx.Observable.fromEvent($input, 'keyup')
    .map(e => e.target.value)
    .filter(text => text.length > 2)
    .throttle(500 /* ms */);
    .distinctUntilChanged();
    .flatMapLatest(searchWikipedia);
    .subscribe(data => {
        var res = data[1];
        $results.empty();
        $.each(res, (_, value) => $('<li>' + value + '</li>').appendTo($results));
    }, error => {
        $results.empty();
        $('<li>Error: ' + error + '</li>').appendTo($results);
    });
```

当用户输入一个稳定的关键字后，向维基百科请求搜索结果，然后显示出来。

即便你没有学过 **Web 前端**开发，但是只要你熟悉 [Rx] ，以上代码你也能够看懂。

---

# 总结

由于 [Rx] 支持多种**后台语言**，如：**Java**，**JS**，**Go**。所以你也可以用它来写后台。

如果你已经能够熟练使用 [RxSwift] ，那么你就已经具有某种“天赋”，这种“天赋”可以帮助你快速上手其他平台。你只需要学习一些和平台相关的知识，就可以写出交互相当复杂的应用程序。因为你的 [Rx] 技巧是可以跨平台复用的。

另外，你的学习效率也会更高，如果你在 [RxSwift] 中学到了某些技巧，那么这个技巧通常也可以被应用到 **Android** 或者其他的平台。如果你在 [RxJava] 中学到了某些技巧，那么这个技巧通常也可以被应用到 **iOS** 平台。因此，你的学习资源也就不再局限于 [RxSwift]，你还可以浏览其他平台上关于 [Rx] 的教程。

下一章将提供一些关于 [RxSwift 的学习资源](resource.md)。

---

[RxDataSources]:https://github.com/RxSwiftCommunity/RxDataSources
[RxAlamofire]:https://github.com/RxSwiftCommunity/RxAlamofire
[Alamofire]:https://github.com/Alamofire/Alamofire
[RxSwift]:https://github.com/ReactiveX/RxSwift
[RxJava]:https://github.com/ReactiveX/RxJava
[RxJS]:https://github.com/Reactive-Extensions/RxJS
[Rx]:https://github.com/Reactive-Extensions/Rx.NET
[ReactiveX]:http://reactivex.io/
[Realm]:https://realm.io/docs/
[RxRealm]:https://github.com/RxSwiftCommunity/RxRealm

[RxGesture]:https://github.com/RxSwiftCommunity/RxGesture
[RxMKMapView]:https://github.com/RxSwiftCommunity/RxMKMapView
[RxCoreMotion]:https://github.com/RxSwiftCommunity/RxCoreMotion
[RxCoreData]:https://github.com/RxSwiftCommunity/RxCoreData
[RxMediaPicker]:https://github.com/RxSwiftCommunity/RxMediaPicker
[Action]:https://github.com/RxSwiftCommunity/Action
[RxWebKit]:https://github.com/RxSwiftCommunity/RxWebKit
[RxEventHub]:https://github.com/RxSwiftCommunity/RxEventHub
[RxSwiftExt]:https://github.com/RxSwiftCommunity/RxSwiftExt

[jQuery]:https://github.com/Reactive-Extensions/RxJS-jQuery
[RxJS-DOM]:https://github.com/Reactive-Extensions/RxJS-DOM
[AngularJS]:https://github.com/Reactive-Extensions/rx.angular.js
[RxEmber]:https://github.com/benlesh/RxEmber

[响应式编程]:think_reactive.md
