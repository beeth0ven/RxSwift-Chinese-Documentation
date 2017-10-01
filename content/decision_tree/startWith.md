## startWith

**将一些元素插入到序列的头部**

![](/assets/WhichOperator/Operators/startWith.png)

**startWith** 操作符会在 `Observable` 头部插入一些元素。

（如果你想在尾部加入一些元素可以用[concat](concat.md)）

---

### 演示

```swift
let disposeBag = DisposeBag()

Observable.of("🐶", "🐱", "🐭", "🐹")
    .startWith("1")
    .startWith("2")
    .startWith("3", "🅰️", "🅱️")
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)
```

**输出结果：**

```swift
3
🅰️
🅱️
2
1
🐶
🐱
🐭
🐹
```
