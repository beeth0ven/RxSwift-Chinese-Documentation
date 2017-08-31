## concat

**让两个或多个 `Observables` 按顺序串连起来**

![](/assets/WhichOperator/Operators/concat.png)

**concat** 操作符将多个 `Observables` 按顺序串联起来，当前一个 `Observable` 元素发送完毕后，后一个  `Observable` 才可以开始发出元素。

**concat** 将等待前一个 `Observable` 产生完成事件后，才对后一个 `Observable` 进行订阅。如果后一个是“热” `Observable` ，在它前一个 `Observable` 产生完成事件前，所产生的元素将不会被发送出来。

[startWith](startWith.md) 和它十分相似。但是[startWith](startWith.md)不是在后面添加元素，而是在前面插入元素。

[merge](merge.md) 和它也是十分相似。[merge](merge.md)并不是将多个 `Observables` 按顺序串联起来，而是将他们合并到一起，不需要 `Observables` 按先后顺序发出元素。
