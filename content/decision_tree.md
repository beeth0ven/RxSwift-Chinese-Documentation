# 如何选择操作符？

![](/assets/WhichOperator/WhichOperator.png)

下面这个**决策树**可以帮助你找到需要的操作符。

---

### 决策树

**我想要创建一个 `Observable`**
* 产生特定的一个元素：[just](decision_tree/just.md)
  * 经过一段延时：[timer](decision_tree/timer.md)
* 从一个序列拉取元素：[from](decision_tree/from.md)
* 重复的产生某一个元素：[repeatElement](decision_tree/repeatElement.md)
* 存在自定义逻辑：[create](decision_tree/create.md)
* 每次订阅时产生：[defer](decision_tree/defer.md)
* 每隔一段时间，发出一个元素：[interval](decision_tree/interval.md)
  * 在一段延时后：[timer](decision_tree/timer.md)
* 一个空序列，只有一个完成事件：[empty](decision_tree/empty.md)
* 一个任何事件都没有产生的序列：[never](decision_tree/never.md)


**我想要创建一个 `Observable` 通过组合其他的 `Observables`**
* 任意一个 `Observable` 产生了元素，就发出这个元素：[merge](decision_tree/merge.md)
* 让这些 `Observables` 一个接一个的发出元素，当上一个 `Observable` 元素发送完毕后，下一个  `Observable` 才能开始发出元素：[concat](decision_tree/concat.md)
* 组合多个 `Observables` 的元素
  * 当每一个 `Observable` 都发出一个新的元素：[zip](decision_tree/zip.md)
  * 当任意一个 `Observable` 发出一个新的元素：[combineLatest](decision_tree/combineLatest.md)


**我想要转换 `Observable` 的元素后，再将它们发出来**
* 对每个元素直接转换：[map](decision_tree/map.md)
* 转换到另一个 `Observable`：[flatMap](decision_tree/flatMap.md)
  * 只接收最新的元素转换的 `Observable` 所产生的元素：[flatMapLatest](decision_tree/flatMapLatest.md)
  * 每一个元素转换的 `Observable` 按顺序产生元素：[concatMap](decision_tree/concatMap.md)
* 基于所有遍历过的元素： [scan](decision_tree/scan.md)

**我想要将产生的每一个元素，拖延一段时间后在发出：[delay](decision_tree/delay.md)**

**我想要将产生的事件封装成元素发送出来**
* 将他们封装成 `Event<Element>`：[materialize](decision_tree/materialize.md)
  * 然后解封出来：[dematerialize](decision_tree/dematerialize.md)

**我想要忽略掉所有的 `next` 事件，只接收 `completed` 和 `error` 事件：[ignoreElements](decision_tree/ignoreElements.md)**

**我想创建一个新的 `Observable` 在原有的序列前面加入一些元素：[startWith](decision_tree/startWith.md)**

**我想从 `Observable` 中收集元素，缓存这些元素之后在发出：[buffer](decision_tree/buffer.md)**

**我想将 `Observable` 拆分成多个 `Observables`：[window](decision_tree/window.md)**
* 基于元素的共同特征：[groupBy](decision_tree/groupBy.md)

**我想只接收 `Observable` 中特定的元素**
* 发出唯一的元素：[single](decision_tree/single.md)

**我想重新从 `Observable` 中发出某些元素**
* 通过判定条件过滤出一些元素：[filter](decision_tree/filter.md)
* 仅仅发出头几个元素：[take](decision_tree/take.md)
* 仅仅发出尾部的几个元素：[takeLast](decision_tree/takeLast.md)
* 仅仅发出第 n 个元素：[elementAt](decision_tree/elementAt.md)
* 跳过头几个元素  
  * 跳过头 n 个元素：[skip](decision_tree/skip.md)
  * 跳过头几个满足判定的元素：[skipWhile](decision_tree/skipWhile.md)，[skipWhileWithIndex](decision_tree/skipWhile.md)
  * 跳过某段时间内产生的头几个元素：[skip](decision_tree/skip.md)
  * 跳过头几个元素直到另一个 `Observable` 发出一个元素：[skipUntil](decision_tree/skipUntil.md)
* 只取头几个元素
  * 只取头几个满足判定的元素：[takeWhile](decision_tree/takeWhile.md)，[takeWhileWithIndex](decision_tree/takeWhile.md)
  * 只取某段时间内产生的头几个元素：[take](decision_tree/take.md)
  * 只取头几个元素直到另一个 `Observable` 发出一个元素：[takeUntil](decision_tree/takeUntil.md)
* 周期性的对 `Observable` 抽样：[sample](decision_tree/sample.md)
* 发出那些元素，这些元素产生后的特定的时间内，没有新的元素产生：[debounce](decision_tree/debounce.md)
* 直到元素的值发生变化，才发出新的元素：[distinctUntilChanged](decision_tree/distinctUntilChanged.md)
  * 并提供元素是否相等的判定函数：[distinctUntilChanged](decision_tree/distinctUntilChanged.md)
* 在开始发出元素时，延时后进行订阅：[delaySubscription](decision_tree/delaySubscription.md)

**我想要从一些 `Observables` 中，只取第一个产生元素的 `Observable`：[amb](decision_tree/amb.md)**

**我想评估 `Observable` 的全部元素**
* 并且对每个元素应用聚合方法，待所有元素都应用聚合方法后，发出结果：[reduce](decision_tree/reduce.md)
* 并且对每个元素应用聚合方法，每次应用聚合方法后，发出结果：[scan](decision_tree/scan.md)

**我想把 `Observable` 转换为其他的数据结构：as...**

**我想在某个 [Scheduler](rxswift_core/schedulers.md) 应用操作符：[subscribeOn](decision_tree/subscribeOn.md)**
* 在某个 [Scheduler](rxswift_core/schedulers.md) 监听：[observeOn](decision_tree/observeOn.md)

**我想要 `Observable` 发生某个事件时, 采取某个行动：[do](decision_tree/do.md)**

**我想要 `Observable` 发出一个 `error` 事件：[error](decision_tree/error.md)**
* 如果规定时间内没有产生元素：[timeout](decision_tree/timeout.md)

**我想要 `Observable` 发生错误时，优雅的恢复**
* 如果规定时间内没有产生元素，就切换到备选 `Observable` ：[timeout](decision_tree/timeout.md)
* 如果产生错误，将错误替换成某个元素 ：[catchErrorJustReturn](decision_tree/catchError.md)
* 如果产生错误，就切换到备选 `Observable` ：[catchError](decision_tree/catchError.md)
* 如果产生错误，就重试 ：[retry](decision_tree/retry.md)

**我创建一个 `Disposable` 资源，使它与 `Observable` 具有相同的寿命：[using](decision_tree/using.md)**

**我创建一个 `Observable`，直到我通知它可以产生元素后，才能产生元素：[publish](decision_tree/publish.md)**
* 并且，就算是在产生元素后订阅，也要发出全部元素：[replay](decision_tree/replay.md)
* 并且，一旦所有观察者取消观察，他就被释放掉：[refCount](decision_tree/refCount.md)
* 通知它可以产生元素了：[connect](decision_tree/connect.md)
