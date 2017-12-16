### deferred

**直到订阅发生，才创建 `Observable`，并且为每位订阅者创建全新的 `Observable`**

![](/assets/WhichOperator/Operators/deferred.png)

**deferred** 操作符将等待观察者订阅它，才创建一个 `Observable`，它会通过一个构建函数为每一位订阅者创建新的 `Observable`。看上去每位订阅者都是对同一个 `Observable` 产生订阅，实际上它们都获得了独立的序列。

在一些情况下，直到订阅时才创建 `Observable` 是可以保证拿到的数据都是最新的。
