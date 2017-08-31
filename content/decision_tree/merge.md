## merge

**将多个 `Observables` 合并成一个**

![](/assets/WhichOperator/Operators/merge.png)

通过使用 **merge** 操作符你可以将多个 `Observables` 合并成一个，当某一个 `Observable` 发出一个元素时，他就将这个元素发出。

如果，某一个 `Observable` 发出一个 `onError` 事件，那么被合并的  `Observable` 也会将它发出，并且立即终止序列。
