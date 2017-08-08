## amb

**在多个源 `Observables` 中， 取第一个发出元素或产生事件的 `Observable`，然后只发出它的元素**

![](/assets/Operator/Operators/amb.png)

当你传入多个 `Observables` 到 **amb** 操作符时，它将取其中一个 `Observable`：第一个产生事件的那个 `Observable`，可以是一个 `next`，`error` 或者 `completed` 事件。 **amb** 将忽略掉其他的 `Observables`。
