#### 问题原因：
>数据绑定为对象时，对象的属性可新增。由于Vue只会在初始化页面时会使用Object.defineProperty()劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。但是对象新增的属性并没有重写setter、getter方法，导致双向绑定无效。

##### 解决方案：
>给所绑定的对象新增属性时，不要直接定义属性，要用Vue.$set方法新增属性。

##### 参考：
[Vue.$set介绍](https://cn.vuejs.org/v2/api/#vm-set)
[剖析Vue原理&实现双向绑定MVVM - 前端足迹 - SegmentFault](https://segmentfault.com/a/1190000006599500)
