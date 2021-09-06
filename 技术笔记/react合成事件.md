合成事件：react会把jsx上绑定的事件全部都绑定到document上

所以如果通过代码绑定的document事件（document.addEventListener）是无法通过合成事件阻止冒泡的。

解决方法：e.stopPropagation() 改为event.nativeEvent.stopImmediatePropagation()

