### single

**限制 `Observable` 只有一个元素，否出发出一个 `error` 事件**

![](/assets/Operator/Operators/single.png)

**ignoreElements** 操作符将限制 `Observable` 只产生一个元素。如果 `Observable` 只有一个元素，它将镜像这个 `Observable` 。如果 `Observable` 没有元素或者元素数量大于一，它将产生一个 `error` 事件。
