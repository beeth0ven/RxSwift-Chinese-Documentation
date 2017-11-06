## debug

**打印所有的订阅，事件以及销毁信息**

---

### 演示

```swift
let disposeBag = DisposeBag()

let sequence = Observable<String>.create { observer in
    observer.onNext("🍎")
    observer.onNext("🍐")
    observer.onCompleted()
    return Disposables.create()
}

sequence
    .debug("Fruit")
    .subscribe()
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
2017-11-06 20:49:43.187: Fruit -> subscribed
2017-11-06 20:49:43.188: Fruit -> Event next(🍎)
2017-11-06 20:49:43.188: Fruit -> Event next(🍐)
2017-11-06 20:49:43.188: Fruit -> Event completed
2017-11-06 20:49:43.189: Fruit -> isDisposed
```
