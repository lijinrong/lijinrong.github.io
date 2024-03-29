# react hook 原理

## 摘要

当使用 Hook 特性编写组件的时候时候，总能感觉到它的简洁和方便。当然，「天下没有免费的午餐」，它牺牲了可读性并且存在内存泄漏风险（最后有提到）。但这并不妨碍探索它的魔力。

在正式开始前，希望您读过 Hook 的文档或者在项目使用过它。但如果只对函数编程感兴趣，也可能有所收获。

为了让行文更流畅，我打算先抛出几个问题，这些问题会在源码实现的过程中，逐步解决：

- 🤔️ useState 的实现原理
- 🤔️ 为什么不能在循环、判断内部使用 Hook
- 🤔️ useEffect 的实现原理
- 🤔️ useEffect 的应用场景
- 🤔️ `Class` vs `Hooks`

## useState 的实现原理

当调用 useState 的时候，会返回形如 `(变量, 函数)` 的一个元祖。并且 state 的初始值就是外部调用 useState 的时候，传入的参数。

理清楚了传参和返回值，再来看下 useState 还做了些什么。正如下面代码所示，当点击按钮的时候，执行`setNum`，状态 num 被更新，**并且 UI 视图更新**。显然，useState 返回的用于更改状态的函数，自动调用了`render`方法来触发视图更新。

```javascript
function App() {
  const [num, setNum] = useState < number > 0;

  return (
    <div>
      <div>num: {num}</div>
      <button onClick={() => setNum(num + 1)}>加 1</button>
    </div>
  );
}
```

有了上面的探索，借助闭包，封装一个 `setState` 如下：

```javascript
function render() {
  ReactDOM.render(<App />, document.getElementById("root"));
}

let state: any;

function useState<T>(initialState: T): [T, (newState: T) => void] {
  state = state || initialState;

  function setState(newState: T) {
    state = newState;
    render();
  }

  return [state, setState];
}

render(); // 首次渲染
```

先不要考虑题目提及的问题。思路还是回到如何让 useState 支持多个 state。[《React hooks: not magic, just arrays》](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)中提及，React Hook 看起来非常 Magic 的实现，本质上还是通过 Array 来实现的。

前面 useState 的简单实现里，初始的状态是保存在一个全局变量中的。以此类推，多个状态，应该是保存在一个专门的全局容器中。这个容器，就是一个朴实无华的 Array 对象。具体过程如下：

- 第一次渲染时候，根据 useState 顺序，逐个声明 state 并且将其放入全局 Array 中。每次声明 state，都要将 cursor 增加 1。
- 更新 state，触发再次渲染的时候。**cursor 被重置为 0**。按照 useState 的声明顺序，依次拿出最新的 state 的值，视图更新。

请看下面这张图，每次使用 useState，都会向 STATE 容器中添加新的状态。

![img](../assets/1-8887713.)

实现的代码如下：

```javascript
import React from "react";
import ReactDOM from "react-dom";

const states: any[] = [];
let cursor: number = 0;

function useState<T>(initialState: T): [T, (newState: T) => void] {
  const currenCursor = cursor;
  states[currenCursor] = states[currenCursor] || initialState; // 检查是否渲染过

  function setState(newState: T) {
    states[currenCursor] = newState;
    render();
  }

  ++cursor; // update: cursor
  return [states[currenCursor], setState];
}

function App() {
  const [num, setNum] = useState < number > 0;
  const [num2, setNum2] = useState < number > 1;

  return (
    <div>
      <div>num: {num}</div>
      <div>
        <button onClick={() => setNum(num + 1)}>加 1</button>
        <button onClick={() => setNum(num - 1)}>减 1</button>
      </div>
      <hr />
      <div>num2: {num2}</div>
      <div>
        <button onClick={() => setNum2(num2 * 2)}>扩大一倍</button>
        <button onClick={() => setNum2(num2 / 2)}>缩小一倍</button>
      </div>
    </div>
  );
}

function render() {
  ReactDOM.render(<App />, document.getElementById("root"));
  cursor = 0; // 重置cursor
}

render(); // 首次渲染
```

此时，如果想在循环、判断等不在函数组件顶部的地方使用 Hook，如下所示：

```
let tag = true;

function App() {
  const [num, setNum] = useState < number > 0;

  // 只有初次渲染，才执行
  if (tag) {
    const [unusedNum] = useState < number > 1;
    tag = false;
  }

  const [num2, setNum2] = useState < number > 2;

  return (
    <div>
      <div>num: {num}</div>
      <div>
        <button onClick={() => setNum(num + 1)}>加 1</button>
        <button onClick={() => setNum(num - 1)}>减 1</button>
      </div>
      <hr />
      <div>num2: {num2}</div>
      <div>
        <button onClick={() => setNum2(num2 * 2)}>扩大一倍</button>
        <button onClick={() => setNum2(num2 / 2)}>缩小一倍</button>
      </div>
    </div>
  );
}
```

由于在条件判断的逻辑中，重置了`tag=false`，因此此后的渲染不会再进入条件判断语句。看起来好像没有问题？但是，由于 useState 是基于 Array+Cursor 来实现的，第一次渲染时候，state 和 cursor 的对应关系如下表：

| 变量名    | cursor |
| --------- | ------ |
| num       | 0      |
| unusedNum | 1      |
| num2      | 2      |

当点击事件触发再次渲染，并不会进入条件判断中的 useState。所以，cursor=2 的时候对应的变量是 num2。而其实 num2 对应的 cursor 应该是 3。就会导致`setNum2`并不起作用。

到此，解决了文章开头提出的「🤔️ 为什么不能在循环、判断内部使用 Hook」。在使用 Hook 的时候，请在函数组件顶部使用！


## useEffect 的实现原理

在探索 useEffect 原理的时候，一直被一个问题困扰：useEffect 作用和用途是什么？当然，用于函数的副作用这句话谁都会讲。举个例子吧：

```
function App() {
  const [num, setNum] = useState(0);

  useEffect(() => {
    // 模拟异步请求后端数据
    setTimeout(() => {
      setNum(num + 1);
    }, 1000);
  }, []);

  return <div>{!num ? "请求后端数据..." : `后端数据是 ${num}`}</div>;
}
复制代码
```

这段代码，虽然这样组织可读性更高，毕竟可以将这个请求理解为函数的副作用。**但这并不是必要的**。完全可以不使用`useEffect`，直接使用`setTimeout`，并且它的回调函数中更新函数组件的 state。

这段代码，虽然这样组织可读性更高，毕竟可以将这个请求理解为函数的副作用。**但这并不是必要的**。完全可以不使用`useEffect`，直接使用`setTimeout`，并且它的回调函数中更新函数组件的 state。

在阅读[A Complete Guide to useEffect](https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/)和[构建你自己的 Hooks](http://react.html.cn/docs/hooks-custom.html)之后，我才理解 useEffect 的存在的必要性和意义。

在 useEffect 的第二个参数中，我们可以指定一个数组，如果下次渲染时，数组中的元素没变，那么就不会触发这个副作用（可以类比 Class 类的关于 nextprops 和 prevProps 的生命周期）。好处显然易见，**相比于直接裸写在函数组件顶层，useEffect 能根据需要，避免多余的 render**。

下面是一个不包括销毁副作用功能的 useEffect 的 TypeScript 实现：

```javascript
// 还是利用 Array + Cursor的思路
const allDeps: any[][] = [];
let effectCursor: number = 0;

function useEffect(callback: () => void, deps: any[]) {
  if (!allDeps[effectCursor]) {
    // 初次渲染：赋值 + 调用回调函数
    allDeps[effectCursor] = deps;
    ++effectCursor;
    callback();
    return;
  }

  const currenEffectCursor = effectCursor;
  const rawDeps = allDeps[currenEffectCursor];
  // 检测依赖项是否发生变化，发生变化需要重新render
  const isChanged = rawDeps.some(
    (dep: any, index: number) => dep !== deps[index]
  );
  if (isChanged) {
    callback();
    allDeps[effectCursor] = deps; // 感谢 juejin@carlzzz 的指正
  }
  ++effectCursor;
}

function render() {
  ReactDOM.render(<App />, document.getElementById("root"));
  effectCursor = 0; // 注意将 effectCursor 重置为0
}
```

对于 useEffect 的实现，配合下面案例的使用会更容易理解。当然，你也可以在这个 useEffect 中发起异步请求，并在接受数据后，调用 state 的更新函数，不会发生爆栈的情况。

```javascript
function App() {
  const [num, setNum] = useState < number > 0;
  const [num2] = useState < number > 1;

  // 多次触发
  // 每次点击按钮，都会触发 setNum 函数
  // 副作用检测到 num 变化，会自动调用回调函数
  useEffect(() => {
    console.log("num update: ", num);
  }, [num]);

  // 仅第一次触发
  // 只会在compoentDidMount时，触发一次
  // 副作用函数不会多次执行
  useEffect(() => {
    console.log("num2 update: ", num2);
  }, [num2]);

  return (
    <div>
      <div>num: {num}</div>
      <div>
        <button onClick={() => setNum(num + 1)}>加 1</button>
        <button onClick={() => setNum(num - 1)}>减 1</button>
      </div>
    </div>
  );
}
```

⚠️ useEffect 第一个回调函数可以返回一个用于销毁副作用的函数，相当于 Class 组件的 unmount 生命周期。这里为了方便说明，没有进行实现。

在这一小节中，尝试解答了 「🤔️ useEffect 的实现原理」和 「🤔️ useEffect 的应用场景」这两个问题。

## Class VS Hooks

虽然 Hooks 看起来更酷炫，更简洁。但是在实际开发中我更倾向于使用 Class 来声明组件。两种方法的对比如下：

| Class                                          | Hooks                |
| ---------------------------------------------- | -------------------- |
| 代码逻辑清晰（构造函数、componentDidMount 等） | 需要配合变量名和注释 |
| 不容易内存泄漏                                 | 容易发生内存泄漏     |

总的来说，Hooks 对代码编写的要求较高，在没有有效机制保证代码可读性、规避风险的情况下，Class 依然是我的首选。关于内存泄漏，下面是一个例子（目前还没找到方法规避这种向全局传递状态更新函数的做法）：

```
import React, { useState } from "react";
import ReactDOM from "react-dom";

let func: any;
setInterval(() => {
  typeof func === "function" && func(Date.now());
  console.log("interval");
}, 1000);

function App() {
  const [num, setNum] = useState < number > 0;
  if (typeof func !== "function") {
    func = setNum;
  }
  return <div>{num}</div>;
}

function render() {
  ReactDOM.render(<App />, document.getElementById("root"));
}

render();
```