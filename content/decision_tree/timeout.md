### timeout

**如果源 `Observable` 在规定时间内没有发任何出元素，就产生一个超时的 `error` 事件**

![](/assets/WhichOperator/Operators/timeout.png)

**timeout** 操作符将使得序列发出一个 `error` 事件，只要 `Observable` 在一段时间内没有产生元素。
