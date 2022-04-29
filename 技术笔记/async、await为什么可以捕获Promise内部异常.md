# async await 为什么可以捕获到Promise内部异常

## 源代码vs编译后的代码

源代码如下：

```javascript
async function test() {
  try {
    await Promise.reject('bad');
} catch (err) {
    console.log(err);
  }
}
```

编译后的代码如下：

```javascript
function asyncGeneratorStep(gen, resolve, reject, _next, _throw, key, arg) {
  try {
    var info = gen[key](arg);
    var value = info.value;
  } catch (error) {
    reject(error);
    return;
  }
  if (info.done) {
    resolve(value);
  } else {
    Promise.resolve(value).then(_next, _throw);
  }
}

function _asyncToGenerator(fn) {
  return function () {
    var self = this,
      args = arguments;
    return new Promise(function (resolve, reject) {
      var gen = fn.apply(self, args);
      function _next(value) {
        asyncGeneratorStep(gen, resolve, reject, _next, _throw, 'next', value);
      }
      function _throw(err) {
        asyncGeneratorStep(gen, resolve, reject, _next, _throw, 'throw', err);
      }
      _next(undefined);
    });
  };
}

function test() {
  return _test.apply(this, arguments);
}

function _test() {
  _test = _asyncToGenerator(function* () {
    try {
      yield Promise.reject('bad');
    } catch (err) {
      console.log(err);
    }
  });
  return _test.apply(this, arguments);
}
```

逐步分析：

* 函数执行，实际上执行_asyncToGenerator返回的函数，_asyncToGenerator传入的fn为一个迭代器
* asyncToGenerator返回的函数，返回了一个Promise，并开始递归执行fn迭代器
* 当yield 返回一个fullfilled的Promise时，会调用_next方法，当返回一个rejected的Promise时，会调用_throw方法
* 当调用_throw方法时，会调用迭代器的throw方法抛出一个异常，这个异常会先被迭代器内部的try catch捕获，即我们代码写的try catch