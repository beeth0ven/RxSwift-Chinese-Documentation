## withLatestFrom

**将两个 `Observables` 最新的元素通过一个函数组合起来，当第一个 `Observable` 发出一个元素，就将组合后的元素发送出来**

![](/assets/WhichOperator/Operators/withLatestFrom.png)

**withLatestFrom** 操作符将两个 `Observables` 中最新的元素通过一个函数组合起来，然后将这个组合的结果发出来。当第一个 `Observable` 发出一个元素时，就立即取出第二个 `Observable` 中最新的元素，通过一个组合函数将两个最新的元素合并后发送出去。

---

### 演示
当第一个 `Observable` 发出一个元素时，就立即取出第二个 `Observable` 中最新的元素，然后把第二个 `Observable` 中最新的元素发送出去。
```swift
let disposeBag = DisposeBag()
let firstSubject = PublishSubject<String>()
let secondSubject = PublishSubject<String>()

firstSubject
     .withLatestFrom(secondSubject)
     .subscribe(onNext: { print($0) })
     .disposed(by: disposeBag)

firstSubject.onNext("🅰️")
firstSubject.onNext("🅱️")
secondSubject.onNext("1")
secondSubject.onNext("2")
firstSubject.onNext("🆎")
```

**输出结果：**

```swift
2
```

---

当第一个 `Observable` 发出一个元素时，就立即取出第二个 `Observable` 中最新的元素，将第一个 `Observable` 中最新的元素 `first` 和第二个 `Observable` 中最新的元素`second`组合，然后把组合结果 `first+second`发送出去。
```swift
let disposeBag = DisposeBag()
let firstSubject = PublishSubject<String>()
let secondSubject = PublishSubject<String>()

firstSubject
     .withLatestFrom(secondSubject) {
          (first, second) in
          return first + second
     }
     .subscribe(onNext: { print($0) })
     .disposed(by: disposeBag)

firstSubject.onNext("🅰️")
firstSubject.onNext("🅱️")
secondSubject.onNext("1")
secondSubject.onNext("2")
firstSubject.onNext("🆎")
```

**输出结果：**

```swift
🆎2
```
