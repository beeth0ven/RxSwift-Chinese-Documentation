## flatMapLatest

**将 `Observable` 的元素转换成其他的 `Observable`，然后取这些 `Observables` 中最新的一个**

![](/assets/Operator/Operators/flatMapLatest.png)

**flatMapLatest** 操作符将源 `Observable` 的每一个元素应用一个转换方法，将他们转换成 `Observables`。一旦转换出一个新的 `Observable`，就只发出它的元素，旧的 `Observables` 的元素将被忽略掉。
