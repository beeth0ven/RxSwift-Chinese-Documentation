### debounce

**过滤掉高频产生的元素**

![](/assets/WhichOperator/Operators/debounce.png)

`Observable` 在指定时间间隔内没有产生新元素后，**debounce** 操作符将发出 `Observable` 产生的最后一个元素。

---

### 演示

```
// 时间间隔
let dueTime: RxTimeInterval = 5

Observable.of(1,2,3,4,5)
        .concat(Observable.never())
        .debounce(dueTime, scheduler: MainScheduler.instance)
        .subscribe(onNext: {print($0)})
        .addDisposableTo(disposeBag)
```

**经过 dueTime 之后输出：**
```swift
5
```
