## Observable 可被监听的序列

![1](/assets/Observable/Obervable.png)

之前我们提到，`Observable` 可以用于描述元素异步产生的序列。这样我们生活中许多事物都可以通过它来表示，例如：

* `Observable<Double>` 温度

  ![1](/assets/Observable/Temperature.png)

  你可以将温度看作是一个序列，然后监测这个温度值，最后对这个值做出响应。例如：当室温高于 33 度时，打开空调降温。

* `Observable<OnePieceEpisode>` 《海贼王》动漫

  ![1](/assets/Observable/OnePiece.png)

  你也可以把《海贼王》的动漫看作是一个序列。然后当《海贼王》更新一集时，我们就立即观看这一集。

* `Observable<JSON>` JSON

  ![1](/assets/Observable/JSON.png)

  你可以把网络请求的返回的 JSON 看作是一个序列。然后当取到 JSON 时，将它打印出来。

* `Observable<Void>` 回调

  ![1](/assets/Observable/Callback.png)

  你可以把任务回调看作是一个序列。当任务结束后，提示用户任务已完成。
