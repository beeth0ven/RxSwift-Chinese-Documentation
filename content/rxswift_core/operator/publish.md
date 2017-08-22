## publish

**将 `Observable` 转换为可被连接的 `Observable`**

![](/assets/Operator/Operators/publish.png)

**publish** 会将 `Observable` 转换为可被连接的 `Observable`。可被连接的 `Observable` 和普通的 `Observable` 十分相似，不过在被订阅后不会发出元素，直到 [connect] 操作符被应用为止。这样一来你可以控制 `Observable` 在什么时候**开始**发出元素。

[connect]:connect.md
