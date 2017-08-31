### window

**将 `Observable` 分解为多个子 `Observable`，周期性的将子 `Observable` 发出来**

![](/assets/WhichOperator/Operators/window.png)

**window** 操作符和 [buffer] 十分相似，[buffer] 周期性的将缓存的元素集合发送出来，而 **window** 周期性的将元素集合以 `Observable` 的形态发送出来。

[buffer] 要等到元素搜集完毕后，才会发出元素序列。而 **window** 可以实时发出元素序列。

[buffer]:buffer.md
