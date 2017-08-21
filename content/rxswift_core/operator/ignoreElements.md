### ignoreElements

**忽略掉所有的元素，只发出 `error` 或 `completed` 事件**

![](/assets/Operator/Operators/ignoreElements.png)

**ignoreElements** 操作符将阻止 `Observable` 发出 `next` 事件，但是允许他发出 `error` 或 `completed` 事件。

如果你并不关心 `Observable` 的任何元素，你只想知道 `Observable` 在什么时候终止，那就可以使用 **ignoreElements** 操作符。
