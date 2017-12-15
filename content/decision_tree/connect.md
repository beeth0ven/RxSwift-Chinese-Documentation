## connect

**通知 `ConnectableObservable` 可以开始发出元素了**

![](/assets/WhichOperator/Operators/publish.png)

** `ConnectableObservable`** 和普通的 `Observable` 十分相似，不过在被订阅后不会发出元素，直到 **connect** 操作符被应用为止。这样一来你可以等所有观察者全部订阅完成后，才发出元素。
