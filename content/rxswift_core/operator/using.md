## using

**创建一个可被清除的资源，它和 `Observable` 具有相同的寿命**

![](/assets/Operator/Operators/using.png)

通过使用 **using** 操作符创建 `Observable` 时，同时创建一个可被清除的资源，一旦 `Observable` 终止了，那么这个资源就会被清除掉了。
