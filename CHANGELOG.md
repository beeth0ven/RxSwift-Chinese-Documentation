# 文档更新日志

文档变更将被记录在此文件内。

---

## master

* 给 [connect](/content/decision_tree/connect.md) 操作符加入演示代码
* 给 [publish](/content/decision_tree/publish.md) 操作符加入演示代码
* 给 [reduce](/content/decision_tree/reduce.md) 操作符加入演示代码
* 给 [skipUntil](/content/decision_tree/skipUntil.md) 操作符加入演示代码
* 给 [skipWhile](/content/decision_tree/skipWhile.md) 操作符加入演示代码
* 给 [skip](/content/decision_tree/skip.md) 操作符加入演示代码

---

## [1.1.0](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/1.1.0)

#### 17年12月7日

* 纠正错别字
* 给 [takeUntil](/content/decision_tree/takeUntil.md) 操作符加入演示代码
* 给 [takeWhile](/content/decision_tree/takeWhile.md) 操作符加入演示代码
* 给 [takeLast](/content/decision_tree/takeLast.md) 操作符加入演示代码
* 加入 [debug](/content/decision_tree/debug.md) 操作符
* 给 [AsyncSubject](/content/rxswift_core/observable_and_observer/async_subject.md) 加入演示代码
* 给 [take](/content/decision_tree/take.md) 操作符加入演示代码
* 给 [elementAt](/content/decision_tree/elementAt.md) 操作符加入演示代码
* 给 [BehaviorSubject](/content/rxswift_core/observable_and_observer/behavior_subject.md) 加入演示代码

---

## [1.0.0](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/1.0.0)

#### 17年10月18日（RxSwift 4）

* 加入[文档电子书下载地址](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/download/1.0.0/RxSwiftChineseDocumentation.epub)
* 去掉学习资源[《如何将代理转换为序列》](https://medium.com/@maxofeden/rxswift-migrate-delegates-to-beautiful-observables-3e606a863048)，因为 RxSwift 4 重构了 **DelegateProxy**  [#1379](https://github.com/ReactiveX/RxSwift/pull/1379)
* 使用 `share(replay: 1)` 替换 `shareReplay(1)`
* 给 [RxJava 演示代码](/content/rxswift_ecosystem.md) 中的变量加上 `final` 关键字，声明为常量
* 示例[多层级的列表页](/content/more_demo/tableView_sectioned_viewController.md)更新到 RxSwift 4，使用新的 RxDataSources 构建方法
* 文档[首页](introduction.md)更新到 RxSwift 4

---

## [0.2.0](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/0.2.0)

#### 17年10月9日

* 给 [ReplaySubject](/content/rxswift_core/observable_and_observer/replay_subject.md) 加入演示代码
* 给 [PublishSubject](/content/rxswift_core/observable_and_observer/publish_subject.md) 加入演示代码
* 给 [distinctUntilChanged](/content/decision_tree/distinctUntilChanged.md) 操作符加入演示代码
* 给 [scan](/content/decision_tree/scan.md) 操作符加入演示代码
* 给 [startWith](/content/decision_tree/startWith.md) 操作符加入演示代码
* 给 [merge](/content/decision_tree/merge.md) 操作符加入演示代码
* **(RxSwift 4)** 使用 [Binder](/content/rxswift_core/observer/binder.md) 替换 **UIBindingObserver**，更简洁实用

## [0.1.1](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/0.1.1)

#### 17年9月18日

* 更新 [RxFeedback](/content/architecture/rxfeedback.md) 配图，与官方保持一致
* 修复 [Maybe](/content/rxswift_core/observable/maybe.md) 中的[描述问题](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/pull/9/files)

---

## [0.1.0](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/0.1.0)

#### 17年9月4日

* 加入学习资源[《泊学 RxSwift 中文视频教程》](https://boxueio.com/series/rxswift-101)
* 给 [concat](/content/decision_tree/concat.md) 操作符加入演示代码
* 给 [concatMap](/content/decision_tree/concatMap.md) 操作符加入演示代码
* 将操作符列表移动到[《如何选择操作符？》](/content/decision_tree.md)章节下，便于查找
* 给 [combineLatest](/content/decision_tree/combineLatest.md) 操作符加入演示代码
* 给 [catchError](/content/decision_tree/catchError.md) 操作符加入演示代码
* 给 [filter](/content/decision_tree/filter.md) 操作符加入演示代码
* 给 [flatMap](/content/decision_tree/flatMap.md) 操作符加入演示代码
* 给 [flatMapLatest](/content/decision_tree/flatMapLatest.md) 操作符加入演示代码
* 给 [map](/content/decision_tree/map.md) 操作符加入演示代码
* 给 [zip](/content/decision_tree/zip.md) 操作符加入演示代码
* 给 [withLatestFrom](/content/decision_tree/withLatestFrom.md) 操作符加入演示代码

---

## [0.0.1](https://github.com/beeth0ven/RxSwift-Chinese-Documentation/releases/tag/0.0.1)

#### 17年9月1日（RxSwift 3.6.1）

* 加入 56 个[操作符](/content/rxswift_core/operator.md)中文说明
* 加入[图片选择器](/content/more_demo/image_picker.md)示例
* 加入[多层级的列表页](/content/more_demo/tableView_sectioned_viewController.md)示例
* 加入[计算器](/content/more_demo/calculator.md)示例
* 加入 [MVVM](/content/architecture/mvvm.md) 架构
* 加入 [RxFeedback](/content/architecture/rxfeedback.md) 架构
* 加入 [ReactorKit](/content/architecture/reactorkit.md) 架构
* 加入 [RxSwift 生态系统和 ReactiveX 生态系统](/content/rxswift_ecosystem.md) 章节
* 加入文档更新日志
* 加入学习资源[《几个 share 操作符的区别》](https://medium.com/@_achou/rxswift-share-vs-replay-vs-sharereplay-bea99ac42168)
* 加入学习资源[《如何将代理转换为序列》](https://medium.com/@maxofeden/rxswift-migrate-delegates-to-beautiful-observables-3e606a863048)
