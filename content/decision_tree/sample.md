### sample

**不定期的对 `Observable` 取样**

![](/assets/WhichOperator/Operators/sample.png)

**sample** 操作符将不定期的对源 `Observable` 进行取样操作。通过第二个 `Observable` 来控制取样时机。一旦第二个 `Observable` 发出一个元素，就从源 `Observable` 中取出最后产生的元素。
