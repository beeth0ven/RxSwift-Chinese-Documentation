### shareReplay

**使观察者共享 `Observable`，观察者会立即收到最新的元素，即使这些元素是在订阅前产生的**

![](/assets/Operator/Operators/replay.png)

**shareReplay** 操作符将使得观察者共享源 `Observable`，并且缓存最新的 **n** 个元素，将这些元素直接发送给新的观察者。
