## concatMap

**将 `Observable` 的元素转换成其他的 `Observable`，然后将这些 `Observables` 串连起来**

![](/assets/Operator/Operators/concatMap.png)

**concatMap** 操作符将源 `Observable` 的每一个元素应用一个转换方法，将他们转换成 `Observables`。然后让这些 `Observables` 按顺序的发出元素，当前一个 `Observable` 元素发送完毕后，后一个  `Observable` 才可以开始发出元素。等待前一个 `Observable` 产生完成事件后，才对后一个 `Observable` 进行订阅。
