# 函数响应式编程

![](/assets/FunctionalReactiveProgramming/FunctionalReactiveProgramming.png)

**函数响应式编程**是种编程范式。它是通过构建函数操作数据序列，然后对这些序列做出响应的编程方式。它结合了**函数式编程**以及**响应式编程**

这里先介绍一下**函数式编程**。

## 函数式编程
**函数式编程**是种编程范式，它需要我们将函数作为参数传递，或者作为返回值返还。我们可以通过组合不同的函数来得到想要的结果。

我们来看一下这几个例子：

```swift
// 全校学生
let allStudents: [Student] = getSchoolStudents()

// 三年二班的学生
let gradeThreeClassTwoStudents: [Student] = allStudents
    .filter { student in student.grade == 3 && student.class == 2 }
```

由于我们想要得到三年二班的学生，所以我们把三年二班的判定函数作为参数传递给 `filter` 方法，这样就能从全校学生中过滤出三年二班的学生。

```swift
// 三年二班的每一个男同学唱一首《一剪梅》
gradeThreeClassTwoStudents
    .filter { student in student.sex == .male }
    .forEach { boy in boy.singASong(name: "一剪梅") }
```

同样的我们将性别的判断函数传递给 `filter` 方法，这样就能从三年二班的学生中过滤出男同学，然后将唱歌作为函数传递给 `forEach` 方法。于是每一个男同学都要唱《一剪梅》😄。

```swift
// 三年二班学生成绩高于90分的家长上台领奖
gradeThreeClassTwoStudents
    .filter { student in student.score > 90 }
    .map { student in student.parent }
    .forEach { parent in parent.receiveAPrize() }
```

用分数判定来筛选出90分以上的同学，然后用`map`转换为学生家长，最后用`forEach`让每个家长上台领奖。

```swift
// 由高到低打印三年二班的学生成绩
gradeThreeClassTwoStudents
    .sorted { student0, student1 in student0.score > student1.score }
    .forEach { student in print("score: \(student.score), name: \(student.name)") }
```

将排序逻辑的函数传递给 `sorted`方法，这样学生就按成绩高低排序，最后用`forEach`将成绩和学生名字打印出来。

### 整体结构

![](/assets/FunctionalReactiveProgramming/FunctionalProgramming.png)

值得注意的是，我们先从三年二班筛选出男同学，后来又从三年二班筛选出分数高于90的学生。都是用的 `filter` 方法，只是传递了不同的判定函数，从而得出了不同的筛选结果。如果现在要实现这个需求：二年一班分数不足60的学生唱一首《我有罪》。

相信大家要不了多久就可以找到对应的实现方法。

这就是**函数式编程**，它使我们可以通过组合不同的方法，以及不同的函数来获取目标结果。你可以想象如果我们用传统的 for 循环来完成相同的逻辑，那将会是一件多么繁琐的事情。所以函数试编程的优点是显而易见的：

* 灵活
* 高复用
* 简洁
* 易维护
* 适应各种需求变化

## 函数式编程 -> 函数响应式编程

现在大家已经了解我们是如何运用**函数式编程**来操作序列的。其实我们可以把这种操作序列的方式在升华一下。例如，你可以把一个按钮的点击事件看作是一个序列：

![](/assets/FunctionalReactiveProgramming/TapArray.png)

```swift
// 假设用户在进入页面到离开页面，总共点击 3 次。

// 按钮点击序列。
let taps: Array<Void> = [(), (), ()]

// 每次点击后弹出提示框
taps.forEach { showAlert() }
```

这样处理点击事件是非常理想的，但是问题是这个序列里面的元素（点击事件）是异步产生的，传统序列是无法描叙这种元素异步产生的序列。为了解决这个问题，于是就产生了可被监听的序列 `Observable<Element>`。它也是一个序列，只不过这个序列里面的元素可以是异步产生的:

![](/assets/FunctionalReactiveProgramming/TapObservable.png)

```swift
// 按钮点击序列
let taps: Observable<Void> = button.rx.tap.asObservable()

// 每次点击后弹出提示框
taps.subscribe(onNext: { showAlert() })
```

这里 `taps` 就是按钮点击事件的序列。然后我们通过弹出提示框，来对每一次点击事件做出响应。这种编程方式叫做**响应式编程**。我们结合**函数式编程**以及**响应式编程**就得到了**函数响应式编程**：

![](/assets/SimpleValid/PasswordValid.png)

```swift
passwordOutlet.rx.text.orEmpty
    .map { $0.characters.count >= minimalPasswordLength }
    .bind(to: passwordValidOutlet.rx.isHidden)
    .disposed(by: disposeBag)
```

## 数据绑定

在**函数响应式编程**里有一个比较重要的概念就是**数据绑定**。也就是将**可被监听的序列**绑定到**观察者**上：

![](/assets/FunctionalReactiveProgramming/Binding.png)

我们对比一下这两段代码：

```swift
let image: UIImage = UIImage(named: ...)
imageView.image = image
```

```swift
let image: Observable<UIImage> = Observable.from(...)
image.bind(to: imageView.rx.image)
```

第一段代码是我们非常熟悉的，它就是将一个单独的图片设置到`imageView`上。

第二段代码则是将一个照片序列同步到`imageView`上。这个序列里面的照片可以是异步产生的。这里定义的 `image` 就是上图中蓝色部分（**可被监听的序列**），`imageView.rx.image`就是上图中橙色部分（**观察者**）。而这种同步机制就是**数据绑定**。
