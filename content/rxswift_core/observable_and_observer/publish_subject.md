## PublishSubject

![](/assets/ObservableAndObserver/PublishSubject.png)

**PublishSubject** 将对观察者发送订阅后产生的元素，而在订阅前发出的元素将不会发送给观察者。如果你希望观察者接收到所有的元素，你可以通过使用 `Observable` 的 `create` 方法来创建 `Observable`，或者使用 [ReplaySubject]。

![](/assets/ObservableAndObserver/PublishSubject1.png)

如果源 `Observable` 因为产生了一个 `error` 事件而中止， **PublishSubject** 就不会发出任何元素，而是将这个 `error` 事件发送出来。

[ReplaySubject]:replay_subject.md
