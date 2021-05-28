# input标签默认宽度

`input` 标签默认是一个 `inline-block` 元素，它的宽度应该由内容撑开。可是 `input` 标签里并没有嵌套其他元素或者文本，我们平时看到 `input` 元素是默认是有宽度的，那么它到底是由什么”撑开“宽度的呢？

[MDN文档](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input)：

>**`size`**
>控件的初始大小。以像素为单位。但**当 type 属性为 text 或 password 时, 它表示输入的字符的长度**。从HTML5开始, 此属性仅适用于当 type 属性为 text,search,tel,url,email,或 password；否则会被忽略。 此外，**它的值必须大于0**。 如果未指定大小，则使用**默认值20**。 HTML5 概述 "用户代理应该确保至少大部分字符是可见的", 但是不同的字符的用不同的字体表示可能会导致宽度不同。在某些浏览器中，一串带有x的字符即使定义了到x的大小也将显示不完整。

所以，`input` 默认是有20个字符的宽度的，那也就是代表着，除了 `size` 以外，`font-size` 也决定了 `input` 的宽度。那我们可以把 `input` 看成是默认装了20个字符的行内块元素。

