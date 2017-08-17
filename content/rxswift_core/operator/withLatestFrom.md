## withLatestFrom

**将两 `Observables` 最新的元素通过一个函数组合以来，当第一个 `Observable` 发出一个元素，就将组合后的元素发送出来**

![](/assets/Operator/Operators/withLatestFrom.png)

**withLatestFrom** 操作符将两个 `Observables` 中最新的元素通过一个函数组合起来，然后将这个组合的结果发出来。当第一个 `Observable` 发出一个元素时，就立即取出第二个 `Observable` 中最新的元素，通过一个组合函数将两个最新的元素合并后发送出去。
