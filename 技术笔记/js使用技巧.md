## 1.生成指定范围的数字

在某些情况下，我们会创建一个处在两个数之间的数组。假设我们要判断某人的生日是否在某个范围的年份内，那么下面是实现它的一个很简单的方法 😎

```
let start = 1900, end = 2000;
[...new Array(end + 1).keys()].slice(start);
// [ 1900, 1901, ..., 2000]

// 还有这种方式，但对于很的范围就不太稳定
Array.from({ length: end - start + 1 }, (_, i) => start + i);
```

## 2.使用值数组作为函数的参数

在某些情况下，我们需要将值收集到数组中，然后将其作为函数的参数传递。 使用 ES6，可以使用扩展运算符(`...`)并从`[arg1, arg2] > (arg1, arg2)`中提取数组:

```
const parts = {
  first: [0, 2],
  second: [1, 3],
}

["Hello", "World", "JS", "Tricks"].slice(...parts.second)
// ["World", "JS"]

复制代码
```

## 3.将值用作 Math 方法的参数

当我们需要在数组中使用`Math.max`或`Math.min`来找到最大或者最小值时，我们可以像下面这样进行操作：

```
const elementsHeight =  [...document.body.children].map(
  el => el.getBoundingClientRect().y
);
Math.max(...elementsHeight);
// 474

const numbers = [100, 100, -1000, 2000, -3000, 40000];
Math.min(...numbers);
// -3000
复制代码
```

## 4.合并/展平数组中的数组

Array 有一个很好的方法，称为`Array.flat`，它是需要一个depth参数，表示数组嵌套的深度，默认值为`1`。 但是，如果我们不知道深度怎么办，则需要将其全部展平，只需将`Infinity`作为参数即可 😎

```
const arrays = [[10], 50, [100, [2000, 3000, [40000]]]]

arrays.flat(Infinity)
// [ 10, 50, 100, 2000, 3000, 40000 ]
复制代码
```

## 5. 防止代码崩溃

在代码中出现不可预测的行为是不好的，但是如果你有这种行为，你需要处理它。

例如，常见错误`TypeError`，试获取`undefined/null`等属性，就会报这个错误。

```
const found = [{ name: "Alex" }].find(i => i.name === 'Jim')

console.log(found.name)
// TypeError: Cannot read property 'name' of undefined
复制代码
```

我们可以这样避免它：

```
const found = [{ name: "Alex" }].find(i => i.name === 'Jim') || {}

console.log(found.name)
// undefined
复制代码
```

## 6. 传递参数的好方法

对于这个方法，一个很好的用例就是`styled-components`，在ES6中，我们可以将模板字符中作为函数的参数传递而无需使用方括号。 如果要实现格式化/转换文本的功能，这是一个很好的技巧：

```
const makeList = (raw) =>
  raw
    .join()
    .trim()
    .split("\n")
    .map((s, i) => `${i + 1}. ${s}`)
    .join("\n");

makeList`
Hello, World
Hello, World
`;
// 1. Hello,World
// 2. World,World
复制代码
```

## 7.交换变量

使用解构赋值语法，我们可以轻松地交换变量 使用解构赋值语法 😬:

```
let a = "hello"
let b = "world"

// 错误的方式
a = b
b = a
// { a: 'world', b: 'world' }

// 正确的做法
[a, b] = [b, a]
// { a: 'world', b: 'hello' }
复制代码
```

## 8.按字母顺序排序

需要在跨国际的项目中，对于按字典排序，一些比较特殊的语言可能会出现问题，如下所示 😨

```
// 错误的做法
["a", "z", "ä"].sort((a, b) => a - b);
// ['a', 'z', 'ä']

// 正确的做法
["a", "z", "ä"].sort((a, b) => a.localeCompare(b));
// [ 'a', 'ä', 'z' ]
复制代码
```

>  localeCompare() :用本地特定的顺序来比较两个字符串。

## 9.隐藏隐私

最后一个技巧是屏蔽字符串,当你需要屏蔽任何变量时(不是密码)，下面这种做法可以快速帮你做到：

```
const password = "hackme";
password.substr(-3).padStart(password.length, "*");
// ***kme
复制代码
```

------

**代码部署后可能存在的BUG没法实时知道，事后为了解决这些BUG，花了大量的时间进行log 调试，这边顺便给大家推荐一个好用的BUG监控工具 [Fundebug](https://www.fundebug.com/?utm_source=xiaozhi)。**