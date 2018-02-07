### timeout

**如果源 `Observable` 在规定时间内没有发出任何元素，就产生一个超时的 `error` 事件**

![](/assets/WhichOperator/Operators/timeout.png)

如果 `Observable` 在一段时间内没有产生元素，**timeout** 操作符将使它发出一个 `error` 事件。
