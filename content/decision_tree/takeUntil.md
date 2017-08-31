## takeUntil

**忽略一部分元素，这些元素是在第二个 `Observable` 产生事件后发出的**

![](/assets/WhichOperator/Operators/takeUntil.png)

**takeUntil** 操作符将镜像源 `Observable`，它同时观测第二个 `Observable`。一旦第二个 `Observable` 发出一个元素或者产生一个终止事件，那个镜像的 `Observable` 将立即终止。
