### throttle

**过滤掉高频产生的元素**

![](/assets/WhichOperator/Operators/throttle.png)

**throttle** 与 [debounce](debounce.md) 类似，区别在于：throttle在一个时间区间内，会取第一个，及最后一个元素出来。



---

### 演示

```swift
let disposeBag = DisposeBag()

Observable<String>.create { ob -> Disposable in
    delay(time: 100) { ob.onNext("🍐") }
    delay(time: 500) { ob.onNext("🍎") }
    delay(time: 1000) { ob.onNext("🍊") }
    delay(time: 1200) { ob.onNext("🍇") }
    return Disposables.create()
}.throttle(.milliseconds(1000), scheduler: MainScheduler.instance)
.subscribe(onNext: { v in
    print(v)
})
.disposed(by: disposeBag)
```

**输出结果：**

```swift
🍐
🍊
🍇
```

