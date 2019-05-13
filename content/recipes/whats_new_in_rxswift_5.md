# RxSwift 5 更新了什么？

原文：[What’s new in RxSwift 5] 作者：[freak4pc]

译注：**在此感谢 [freak4pc] 为社区所作出的贡献。**

[RxSwift 5] 终于发布了，我（[freak4pc]）认为这是一个很好的机会，来分享这次发布中最有价值的更新。

别担心，这次发布几乎是向前兼容的，只有少数弃用和重命名的 API。但它还包含了许多改进，我（[freak4pc]）将在下面详细介绍。



## [RxRelay] 现在是一个独立的框架

[RxRelay] 是一个在 Subjects 之上很好的抽象层。它可以让我们发出元素，而不用担心 error 和 completed 这样的终止事件。由于它们被添加到 RxSwift 中，并且是 RxCocoa 项目的一部分。

许多开发者对此很不乐意。因为他们如果要使用 Relays 就必须引入 RxCocoa，即便他们编写的代码与 RxCocoa 没有任何关系。这其实是很不合理的。并且这样一来 Linux 用户就无法使用 Relays，因为 Linux 无法导入 RxCocoa。

由于上述原因，[我们将 Relays 拆分成一个独立的框架](https://github.com/ReactiveX/RxSwift/pull/1924) - [RxRelay] -- 并且调整 RxSwift 的依赖图，如下：

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwiftDpendencyGraph.png)
左侧： RxSwift 4 依赖图，右侧： RxSwift 5 依赖图

这样我们可以引入 [RxRelay]，而不需要导入整个 RxCocoa 框架。并且这也和 [RxJava] 保持一致，因为在 [RxJava] 那边[他也是一个独立的框架](https://github.com/JakeWharton/RxRelay)。

注意：这是一个向前兼容的改动，由于 RxCocoa 依赖于 RxRelay。意味着，只要导入 RxCocoa 并不需要导入 RxRelay，一切就会和以前一样正常工作。

## TimeInterval → DispatchTimeInterval

 RxSwift 5 中重构了 [Schedulers]，弃用了 `TimeInterval`，转而使用 `DispatchTimeInterval`。这样就与底层时间 API 保持一致，不会丢失精度。

 他会影响到所有基于时间的操作符，如：[debounce]，[timeout]，[delay]，[take] 等等。作为额外的收获，他还解决了 [take] 入参的歧义，因为之前无法判断参数代表多少秒，还是多少个。

### RxSwift 4

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift4TimeInterval.png)

 ### RxSwift 5

 ![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift5DispatchTimeInterval.png)


## Variable 最终被弃用了

[Variable] 是早期添加到 RxSwift 的概念，通过 “setting” 和 “getting” 他可以帮助我们，从原先命令式的思维方式，过渡到“响应式的思维方式”。

这种做法被证实是有问题的，许多开发者滥用 [Variable]， 来构建 **重度[命令式]** 系统，而不是 Rx 的 **[声明式]** 系统。这对于新手非常常见，并且让他们无法意识到，这是代码的坏味道。所以在 RxSwift 4.x 中 [Variable] 被轻度弃用，仅仅给出一个运行时警告。

在 RxSwift 5.x 中，他被[官方的正式的弃用了](https://github.com/ReactiveX/RxSwift/pull/1922)，并且在需要时，推荐使用 [BehaviorRelay] 或者 [BehaviorSubject]。


### RxSwift 4

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift4Variable.png)

 ### RxSwift 5

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift5Variable.png)

## 补充 `do(on:)` 重载方法

[do] 是一个很棒的操作符，当你想添加一些[附加作用]，如：打印日志。

为了与 RxJava 对齐，RxSwift 现在不仅提供 `do(onNext:)`，而且还[提供 after 重载方法](https://github.com/ReactiveX/RxSwift/pull/1898)，例如：`do(afterNext:)`。`onNext` 代表元素发送了，但**未被转发到下游**。而 `afterNext` 代表元素发送了，并**已经被转发到下游**。

### RxSwift 4

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift4Do.png)

 ### RxSwift 5

![](/assets/Recipes/WhatsNewInRxSwift5/RxSwift5Do.png)

## `bind(to:)` 现在支持多个观察者

## 新增 `compactMap` 操作符

## `toArray()` 现在返回 `Single<T>`

## 更新范型约束名称

## 社区项目

## 综上所叙

### 参考

* [What’s new in RxSwift 5]


[RxJava]:https://github.com/ReactiveX/RxJava
[RxRelay]:/content/recipes/rxrelay.md
[RxSwift 5]:https://github.com/ReactiveX/RxSwift/releases/tag/5.0.0
[freak4pc]:https://github.com/freak4pc
[What’s new in RxSwift 5]:https://medium.com/@freak4pc/whats-new-in-rxswift-5-f7a5c8ee48e7
[Schedulers]:content/rxswift_core/schedulers.md
[timeout]:content/decision_tree/timeout.md
[debounce]:content/decision_tree/debounce.md
[delay]:content/decision_tree/delay.md
[take]:content/decision_tree/take.md

[Variable]:/content/rxswift_core/observable_and_observer/variable.md
[命令式编程]:https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B
[命令式]:https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B
[声明式编程]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B
[声明式]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B
[BehaviorRelay]:/content/recipes/rxrelay.md
[BehaviorSubject]:/content/rxswift_core/observable_and_observer/behavior_subject.md

[do]:/content/decision_tree/do.md

[附加作用]:/content/recipes/pure_function.md