# 如何选择操作符？

下面这个**决策树**可以帮助你找到需要的操作符。

**我想要创建一个 `Observable`**

* 产生特定的一个元素：**just**
  * 经过一段延时：**timer**
* 从一个序列拉取元素：**from**
* 重复的产生某一个元素：**repeatElement**
* 存在自定义逻辑：**create**
* 每次订阅时产生：**defer**
* 每隔一段时间，发出一个元素：**interval**
  * 在一段延时后：**timer**
* 一个空序列，只有一个完成事件：**empty**
* 一个任何事件都没有产生的序列：**never**


**我想要创建一个 `Observable` 通过组合其他的 `Observables`**

* 任意一个 `Observable` 产生了元素，就发出这个元素：**merge**
* 让这些 `Observables` 一个接一个的发出元素，当上一个 `Observable` 元素发送完毕后，下一个  `Observable` 才能开始发出元素：**concat**
* 组合多个 `Observables` 的元素
  * 当每一个 `Observable` 都发出一个新的元素：**zip**
  * 当任意一个 `Observable` 发出一个新的元素：**combineLatest**


**我想要转换 `Observable` 的元素后，再将它们发出来**

* 对每个元素直接转换：**map**
* 转换到另一个 `Observable`：**flatMap**
  * 只接收第一个元素转换的 `Observable` 所产生的元素：**flatMapFirst**
  * 只接收最新的元素转换的 `Observable` 所产生的元素：**flatMapLatest**
  * 每一个元素转换的 `Observable` 按顺序产生元素：**concatMap**
* 基于所有遍历过的元素： **scan**

**我想要将产生的每一个元素，拖延一段时间后在发出**

* ：**delay**

**我想要将产生的事件封装成元素发送出来**

* 将他们封装成 `Event<Element>`：**materialize**
  * 然后可以通过这个方法解封出来：**dematerialize**

**我想要忽略掉所有的 `next` 事件，只接收 `completed` 和 `error` 事件**

* ：**ignoreElements**

**我想创建一个新的 `Observable` 在原有的序列前面加入一些元素**

* ：**startWith**

**我想从 `Observable` 中收集元素，缓存这些元素之后在发出**

* ：**buffer**

**我想将 `Observable` 拆分成多个 `Observables`： **window**

* 基于元素的共同特征：**groupBy**

**我想只接收 `Observable` 中特定的元素**

* 发出唯一的元素：**single**

**我想重新从 `Observable` 中发出某些元素**

* 通过判定条件过滤出一些元素：**filter**

* 仅仅发出头几个元素：**take**  

* 仅仅发出尾部的几个元素：**takeLast**  

* 仅仅发出第 n 个元素：**elementAt**  

* 跳过头几个元素  

  * 跳过头 n 个元素：**skip**

  * 跳过头几个满足判定的元素：**skipWhile**，**skipWhileWithIndex**

  * 跳过某段时间内产生的头几个元素：**skip**

  * 跳过头几个元素直到另一个 `Observable` 发出一个元素：**skipUntil**

* 只取头几个元素  

  * 只取头几个满足判定的元素：**takeWhile**，**takeWhileWithIndex**

  * 只取某段时间内产生的头几个元素：**take**

  * 只取头几个元素直到另一个 `Observable` 发出一个元素：**takeUntil**

* 周期性的对 `Observable` 抽样：**sample**

* 发出那些元素，这些元素产生后的特定的时间内，没有新的元素产生：**debounce**

* 直到元素的值发生变化，才发出新的元素：**distinctUntilChanged**

  * 并提供元素是否相等的判定函数：**distinctUntilChanged**

* 在开始发出元素时，延时后进行订阅：**delaySubscription**

*

*
