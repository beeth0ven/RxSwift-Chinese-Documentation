# ImagePicker - 图片选择器

![](/assets/MoreDemo/ImagePicker/ImagePickerFull.gif)

这是一个图片选择器的演示，你可以在这里下载[这个例子](https://github.com/ReactiveX/RxSwift/tree/master/RxExample/RxExample/Examples/ImagePicker)。

### 简介

这个 App 主要有这样几个交互：

* 当用户点击相机按钮时，让用户拍一张照片，然后显示出来。
* 当用户点击相册按钮时，让用户从相册中选出照片，然后显示出来。
* 当用户点击裁剪按钮时，让用户从相册中选出照片编辑，然后显示出来。

### 整体结构

![](/assets/MoreDemo/ImagePicker/All.png)

```swift
...
override func viewDidLoad() {
    super.viewDidLoad()

    ...

    cameraButton.rx.tap
        .flatMapLatest { [weak self] _ in
            return UIImagePickerController.rx.createWithParent(self) { picker in
                picker.sourceType = .camera
                picker.allowsEditing = false
            }
            .flatMap { $0.rx.didFinishPickingMediaWithInfo }
            .take(1)
        }
        .map { info in
            return info[.originalImage] as? UIImage
        }
        .bind(to: imageView.rx.image)
        .disposed(by: disposeBag)

    ...    
}
```

我们忽略一些细节，看一下序列的转换过程：

```swift
cameraButton.rx.tap
    .flatMapLatest { () -> Observable<[InfoKey: AnyObject]> ... } // 点击 -> 图片信息
    .map { [InfoKey : AnyObject] -> UIImage? ... } // 图片信息 -> 图片
    .bind(to: imageView.rx.image) // 数据绑定
    .disposed(by: disposeBag)
```

最开始的**按钮点击**是一个 `Void` 序列，接着用 [flatMapLatest] 将它**异步**转化为**图片信息序列** `[String : AnyObject]`，然后用 [map] **同步**的从图片信息中取出图片，从而得到了一个**图片序列** `UIImage?`，最后将这个图片序列绑定到 `imageView` 上：

![](/assets/MoreDemo/ImagePicker/Operator.png)

这是相机按钮点击后需要执行的操作。另外两个按钮（相册和裁剪）和它十分相似，只不过传入了不同的参数，通过不同的键取出图片：

```swift
...
override func viewDidLoad() {
    super.viewDidLoad()

    ...

    // 相机
    cameraButton.rx.tap
        .flatMapLatest { [weak self] _ in
            return UIImagePickerController.rx.createWithParent(self) { picker in
                picker.sourceType = .camera
                picker.allowsEditing = false
            }
            .flatMap { $0.rx.didFinishPickingMediaWithInfo }
            .take(1)
        }
        .map { info in
            return info[UIImagePickerControllerOriginalImage] as? UIImage
        }
        .bind(to: imageView.rx.image)
        .disposed(by: disposeBag)

    // 相册
    galleryButton.rx.tap
        .flatMapLatest { [weak self] _ in
            return UIImagePickerController.rx.createWithParent(self) { picker in
                picker.sourceType = .photoLibrary
                picker.allowsEditing = false
            }
            .flatMap {
                $0.rx.didFinishPickingMediaWithInfo
            }
            .take(1)
        }
        .map { info in
            return info[.originalImage] as? UIImage
        }
        .bind(to: imageView.rx.image)
        .disposed(by: disposeBag)

    // 裁剪
    cropButton.rx.tap
        .flatMapLatest { [weak self] _ in
            return UIImagePickerController.rx.createWithParent(self) { picker in
                picker.sourceType = .photoLibrary
                picker.allowsEditing = true
            }
            .flatMap { $0.rx.didFinishPickingMediaWithInfo }
            .take(1)
        }
        .map { info in
            return info[.editedImage] as? UIImage
        }
        .bind(to: imageView.rx.image)
        .disposed(by: disposeBag)
}
...
```

---

### 参考

* [flatMap]
* [flatMapLatest]
* [map]
* [take]

[flatMap]:/content/decision_tree/flatMap.md
[flatMapLatest]:/content/decision_tree/flatMapLatest.md
[map]:/content/decision_tree/map.md
[take]:/content/decision_tree/take.md
