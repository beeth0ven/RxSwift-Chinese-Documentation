## Variable （已弃用）

**Variable** 是早期添加到 RxSwift 的概念，通过 “setting” 和 “getting”， 他可以帮助我们从原先**命令式的思维方式**，过渡到**响应式的思维方式**。

但这只是我们一厢情愿的。许多开发者滥用 **Variable**， 来构建 **重度[命令式]** 系统，而不是 Rx 的 **[声明式]** 系统。这对于新手很常见，并且他们无法意识到，这是代码的坏味道。所以在 RxSwift 4.x 中 **Variable** 被轻度弃用，仅仅给出一个运行时警告。

在 RxSwift 5.x 中，他被[官方的正式的弃用了](https://github.com/ReactiveX/RxSwift/pull/1922)，并且在需要时，推荐使用 [BehaviorRelay] 或者 [BehaviorSubject]。


[命令式编程]:https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B
[命令式]:https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B
[声明式编程]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B
[声明式]:https://zh.wikipedia.org/wiki/%E5%AE%A3%E5%91%8A%E5%BC%8F%E7%B7%A8%E7%A8%8B
[BehaviorRelay]:/content/recipes/rxrelay.md
[BehaviorSubject]:/content/rxswift_core/observable_and_observer/behavior_subject.md
[操作符]:/content/rxswift_core/operator.md
