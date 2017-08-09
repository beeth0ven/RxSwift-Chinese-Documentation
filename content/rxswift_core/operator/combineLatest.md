## combineLatest

**当多个 `Observables` 中任何一个发出一个元素，就发出一个元素。这个元素是由这些 `Observables` 中最新的元素，通过一个函数组合起来的**

![](/assets/Operator/Operators/combineLatest.png)

**combineLatest** 操作符将多个 `Observables` 中最新的元素通过一个函数组合起来，然后将这个组合的结果发出来。这些源 `Observables` 中任何一个发出一个元素，他都会发出一个元素（前提是，这些 `Observables` 曾经发出过元素）。
