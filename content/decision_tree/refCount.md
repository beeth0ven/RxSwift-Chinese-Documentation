## refCount

**将可被连接的 `Observable` 转换为普通  `Observable`**

![](/assets/WhichOperator/Operators/refCount.png)

**可被连接的 `Observable`** 和普通的 `Observable` 十分相似，不过在被订阅后不会发出元素，直到 [connect] 操作符被应用为止。这样一来你可以控制 `Observable` 在什么时候**开始**发出元素。

**refCount** 操作符将自动连接和断开**可被连接的 `Observable`**。它将**可被连接的 `Observable`** 转换为普通  `Observable`。当第一个观察者对它订阅时，那么底层的 `Observable` 将被连接。当最后一个观察者离开时，那么底层的 `Observable` 将被断开连接。

[connect]:connect.md
