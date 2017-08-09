## zip

**通过一个函数将多个 `Observables` 的元素组合起来，然后将每一个组合的结果发出来**

![](/assets/Operator/Operators/zip.png)

**zip** 操作符将多个 `Observables` 的元素通过一个函数组合起来，然后将这个组合的结果发出来。它会严格的按照序列的索引数进行组合。例如，返回的 `Observable` 的第一个元素，是由每一个源 `Observables` 的第一个元素组合出来的。它的第二个元素 ，是由每一个源 `Observables` 的第二个元素组合出来的。它的第三个元素 ，是由每一个源 `Observables` 的第三个元素组合出来的，以此类推。它的元素数量等于源 `Observables` 中元素数量最少的那个。
