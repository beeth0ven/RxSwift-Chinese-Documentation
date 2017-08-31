### groupBy

**将源 `Observable` 分解为多个子 `Observable`，并且每个子 `Observable` 将源 `Observable` 中“相似”的元素发送出来**

![](/assets/WhichOperator/Operators/groupBy.png)

**groupBy** 操作符将源 `Observable` 分解为多个子 `Observable`，然后将这些子 `Observable` 发送出来。

它会将元素通过某个键进行分组，然后将分组后的元素序列以 `Observable` 的形态发送出来。
