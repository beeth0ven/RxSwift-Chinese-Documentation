## ControlEvent

**ControlEvent** 专门用于描述 **UI** 控件所产生的事件，它具有以下特征：

* 不会产生 `error` 事件
* 一定在 `MainScheduler` 订阅（主线程订阅）
* 一定在 `MainScheduler` 监听（主线程监听）
* 共享[附加作用]
  
[附加作用]:/content/recipes/pure_function.md
