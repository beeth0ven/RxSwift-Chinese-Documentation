## Operator - 操作符

![](/assets/Operator/Operator.png)

**操作符**可以帮助大家创建新的序列，或者变化组合原有的序列，从而生成一个新的序列。

我们之前在[输入验证](/content/first_app.md)例子中就多次运用到操作符。例如，通过 [map] 方法将**输入的用户名**，转换为**用户名是否有效**。然后用这个转化后来的序列来控制红色提示语是否隐藏。我们还通过 [combineLatest] 方法，将**用户名是否有效**和**密码是否有效**合并成**两者是否同时有效**。然后用这个合成后来的序列来控制按钮是否可点击。

这里 [map] 和 [combineLatest] 都是**操作符**，它们可以帮助我们构建所需要的序列。现在，我们再来看几个例子：

### [filter] - 过滤

  ![](/assets/Operator/filter.png)

  你可以用 [filter] 创建一个新的序列。这个序列只发出温度大于 33 度的元素。

### [map] - 转换

  ![](/assets/Operator/map.png)

  你可以用 [map] 创建一个新的序列。这个序列将原有的 **JSON** 转换成 **Model** 。这种转换实际上就是解析 **JSON** 。

### [zip] - 配对

  ![](/assets/Operator/zip.png)

  你可以用 [zip] 来合成一个新的序列。这个序列将汉堡序列的元素和薯条序列的元素配对后，生成一个新的套餐序列。

### 如何使用操作符

使用操作符是非常容易的。你可以直接调用实例方法，或者静态方法：

* 温度过滤

  ```swift
  // 温度
  let rxTemperature: Observable<Double> = ...

  // filter 操作符
  rxTemperature.filter { temperature in temperature > 33 }
      .subscribe(onNext: { temperature in
          print("高温：\(temperature)度")
      })
      .disposed(by: disposeBag)
  ```

* 解析 JSON

  ```swift
  // JSON
  let json: Observable<JSON> = ...

  // map 操作符
  json.map(Model.init)
      .subscribe(onNext: { model in
          print("取得 Model: \(model)")
      })
      .disposed(by: disposeBag)
  ```

* 合成套餐

  ```swift
  // 汉堡
  let rxHamburg: Observable<Hamburg> = ...
  // 薯条
  let rxFrenchFries: Observable<FrenchFries> = ...

  // zip 操作符
  Observable.zip(rxHamburg, rxFrenchFries)
      .subscribe(onNext: { (hamburg, frenchFries) in
          print("取得汉堡: \(hamburg) 和薯条：\(frenchFries)")
      })
      .disposed(by: disposeBag)
  ```

### 决策树

[Rx](https://github.com/Reactive-Extensions/Rx.NET) 提供了充分的**操作符**来帮我们创建序列。当然如果内置操作符无法满足你的需求时，你还可以创建自定义的操作符。

如果你不确定该如何选择操作符，可以参考 [决策树](/content/decision_tree.md)。它会引导你找出合适的操作符。

### 操作符列表

>26个英文字母我都认识，可是连成一个句子我就不怎么认得了...

这里提供一个操作符列表，它们就好比是**26个英文字母**。你如果要将它们的作用全部都发挥出来，是需要学习如何将它们**连成一个句子**的：

* [amb]
* [buffer]
* [catchError]
* [combineLatest]
* [concat]
* [concatMap]
* [connect]
* [create]
* [debounce]
* [debug]
* [deferred]
* [delay]
* [delaySubscription]
* [dematerialize]
* [distinctUntilChanged]
* [do]
* [elementAt]
* [empty]
* [error]
* [filter]
* [flatMap]
* [flatMapLatest]
* [from]
* [groupBy]
* [ignoreElements]
* [interval]
* [just]
* [map]
* [merge]
* [materialize]
* [never]
* [observeOn]
* [publish]
* [reduce]
* [refCount]
* [repeatElement]
* [replay]
* [retry]
* [sample]
* [scan]
* [shareReplay]
* [single]
* [skip]
* [skipUntil]
* [skipWhile]
* [startWith]
* [subscribeOn]
* [take]
* [takeLast]
* [takeUntil]
* [takeWhile]
* [timeout]
* [timer]
* [using]
* [window]
* [withLatestFrom]
* [zip]


[amb]:/content/decision_tree/amb.md
[buffer]:/content/decision_tree/buffer.md
[catchError]:/content/decision_tree/catchError.md
[combineLatest]:/content/decision_tree/combineLatest.md
[concat]:/content/decision_tree/concat.md
[concatMap]:/content/decision_tree/concatMap.md
[connect]:/content/decision_tree/connect.md
[create]:/content/decision_tree/create.md
[debounce]:/content/decision_tree/debounce.md
[debug]:/content/decision_tree/debug.md
[deferred]:/content/decision_tree/deferred.md
[delay]:/content/decision_tree/delay.md
[delaySubscription]:/content/decision_tree/delaySubscription.md
[dematerialize]:/content/decision_tree/dematerialize.md
[distinctUntilChanged]:/content/decision_tree/distinctUntilChanged.md
[do]:/content/decision_tree/do.md
[elementAt]:/content/decision_tree/elementAt.md
[empty]:/content/decision_tree/empty.md
[error]:/content/decision_tree/error.md
[filter]:/content/decision_tree/filter.md
[flatMap]:/content/decision_tree/flatMap.md
[flatMapLatest]:/content/decision_tree/flatMapLatest.md
[from]:/content/decision_tree/from.md
[groupBy]:/content/decision_tree/groupBy.md
[ignoreElements]:/content/decision_tree/ignoreElements.md
[interval]:/content/decision_tree/interval.md
[just]:/content/decision_tree/just.md
[map]:/content/decision_tree/map.md
[merge]:/content/decision_tree/merge.md
[materialize]:/content/decision_tree/materialize.md
[never]:/content/decision_tree/never.md
[observeOn]:/content/decision_tree/observeOn.md
[publish]:/content/decision_tree/publish.md
[reduce]:/content/decision_tree/reduce.md
[refCount]:/content/decision_tree/refCount.md
[repeatElement]:/content/decision_tree/repeatElement.md
[replay]:/content/decision_tree/replay.md
[retry]:/content/decision_tree/retry.md
[sample]:/content/decision_tree/sample.md
[scan]:/content/decision_tree/scan.md
[shareReplay]:/content/decision_tree/shareReplay.md
[single]:/content/decision_tree/single.md
[skip]:/content/decision_tree/skip.md
[skipUntil]:/content/decision_tree/skipUntil.md
[skipWhile]:/content/decision_tree/skipWhile.md
[startWith]:/content/decision_tree/startWith.md
[subscribeOn]:/content/decision_tree/subscribeOn.md
[take]:/content/decision_tree/take.md
[takeLast]:/content/decision_tree/takeLast.md
[takeUntil]:/content/decision_tree/takeUntil.md
[takeWhile]:/content/decision_tree/takeWhile.md
[timeout]:/content/decision_tree/timeout.md
[timer]:/content/decision_tree/timer.md
[using]:/content/decision_tree/using.md
[window]:/content/decision_tree/window.md
[withLatestFrom]:/content/decision_tree/withLatestFrom.md
[zip]:/content/decision_tree/zip.md
