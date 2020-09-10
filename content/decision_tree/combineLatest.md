## combineLatest

**当多个 `Observables` 中任何一个发出一个元素，就发出一个元素。这个元素是由这些 `Observables` 中最新的元素，通过一个函数组合起来的**

![](/assets/WhichOperator/Operators/combineLatest.png)

**combineLatest** 操作符将多个 `Observables` 中最新的元素通过一个函数组合起来，然后将这个组合的结果发出来。这些源 `Observables` 中任何一个发出一个元素，他都会发出一个元素（前提是，这些 `Observables` 曾经都发出过元素）。

---

### 演示

**tips:** 可与 [zip](zip.md) 比较学习

```swift
let disposeBag = DisposeBag()

let first = PublishSubject<String>()
let second = PublishSubject<String>()

Observable.combineLatest(first, second) { $0 + $1 }
          .subscribe(onNext: { print($0) })
          .disposed(by: disposeBag)

first.onNext("1")
second.onNext("A")
first.onNext("2")
second.onNext("B")
second.onNext("C")
second.onNext("D")
first.onNext("3")
first.onNext("4")
```

**输出结果：**

```swift
1A
2A
2B
2C
2D
3D
4D
```
