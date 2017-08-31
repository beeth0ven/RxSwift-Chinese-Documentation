## timer

**创建一个 `Observable` 在一段延时后，产生唯一的一个元素**

![](/assets/WhichOperator/Operators/timer.png)

**timer** 操作符将创建一个 `Observable`，它在经过设定的一段时间后，产生唯一的一个元素。

这里存在其他版本的 `timer` 操作符。

----

## timer

![](/assets/WhichOperator/Operators/timer1.png)

**创建一个 `Observable` 在一段延时后，每隔一段时间产生一个元素**

```swift
public static func timer(
  _ dueTime: RxTimeInterval,  // 初始延时
  period: RxTimeInterval?,    // 时间间隔
  scheduler: SchedulerType
  ) -> Observable<E>
```
