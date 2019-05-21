## 数据绑定（订阅）

![](/assets/FunctionalReactiveProgramming/Binding.png)

在 **RxSwift** 里有一个比较重要的概念就是**数据绑定（订阅）**。就是指将**可监听序列**绑定到**观察者**上：

我们对比一下这两段代码：

```swift
let image: UIImage = UIImage(named: ...)
imageView.image = image
```

```swift
let image: Observable<UIImage> = ...
image.bind(to: imageView.rx.image)
```

第一段代码我们非常熟悉，它就是将一个单独的图片设置到`imageView`上。

第二段代码则是将一个图片序列 **“同步”** 到`imageView`上。这个序列里面的图片可以是**异步**产生的。这里定义的 `image` 就是上图中蓝色部分（**可监听序列**），`imageView.rx.image`就是上图中橙色部分（**观察者**）。而这种 **“同步机制”** 就是**数据绑定（订阅）**。
