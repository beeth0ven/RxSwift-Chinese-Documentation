## materialize

**将序列产生的事件，转换成元素**

![](/assets/WhichOperator/Operators/materialize.png)

通常，一个有限的 `Observable` 将产生零个或者多个 `onNext` 事件，然后产生一个 `onCompleted` 或者 `onError` 事件。

**materialize** 操作符将 `Observable` 产生的这些事件全部转换成元素，然后发送出来。
