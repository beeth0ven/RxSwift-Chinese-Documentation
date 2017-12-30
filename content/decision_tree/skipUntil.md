### skipUntil

**跳过 `Observable` 中头几个元素，直到另一个 `Observable` 发出一个元素**

![](/assets/WhichOperator/Operators/skipUntil.png)

**skipUntil** 操作符可以让你忽略源 `Observable` 中头几个元素，直到另一个 `Observable` 发出一个元素后，它才镜像源 `Observable`。

---

### 演示

```swift
let disposeBag = DisposeBag()

let sourceSequence = PublishSubject<String>()
let referenceSequence = PublishSubject<String>()

sourceSequence
    .skipUntil(referenceSequence)
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)

sourceSequence.onNext("🐱")
sourceSequence.onNext("🐰")
sourceSequence.onNext("🐶")

referenceSequence.onNext("🔴")

sourceSequence.onNext("🐸")
sourceSequence.onNext("🐷")
sourceSequence.onNext("🐵")
```

**输出结果：**

```swift
🐸
🐷
🐵
```
