## flatMapLatest

**将 `Observable` 的元素转换成其他的 `Observable`，然后取这些 `Observables` 中最新的一个**

![](/assets/WhichOperator/Operators/flatMapLatest.png)

**flatMapLatest** 操作符将源 `Observable` 的每一个元素应用一个转换方法，将他们转换成 `Observables`。一旦转换出一个新的 `Observable`，就只发出它的元素，旧的 `Observables` 的元素将被忽略掉。

---

### 演示

**tips：**与 [flatMap](flatMap.md) 比较更容易理解

```swift
let disposeBag = DisposeBag()
let first = BehaviorSubject(value: "👦🏻")
let second = BehaviorSubject(value: "🅰️")
let subject = BehaviorSubject(value: first)

subject.asObservable()
        .flatMapLatest { $0 }
        .subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)

first.onNext("🐱")
subject.onNext(second)
second.onNext("🅱️")
first.onNext("🐶")
```

**输出结果：**

```swift
👦🏻
🐱
🅰️
🅱️
```
