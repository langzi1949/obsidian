#前端  #JavaScript #JS 

# 1. 异步编程

早期的版本中，JS实现的异步函数有很多的问题，他依赖引擎内置函数`setTimeout()`，用于来调度这个异步的方法

```js
function find(value) {
  setTimeout(() => console.log(value), 2000);
}
find(5);  // 5,  大约在2s之后能够执行
```

如果需要使用到异步返回值，采用回调函数的方式
```js
function find(value, callback) {
  setTimeout(() => callback(value * 2), 2000);
}

find(5, (x) => {
  console.log(`Final Result = ${x}`);
});  // Final Result = 10。大约2s后执行
```

处理异常逻辑
```js
function find(value, success, failure) {
  setTimeout(() => {
    try {
      if (typeof value !== 'number') {
        throw '必须要是数字';
      }
      success(value * 2);
    } catch (e) {
      failure(e);
    }
  }, 2000);
}

const successCallback = (x) => {
  console.log(`Success: ${x}`);
};
const failureCallback = (x) => {
  console.log(`Failure: ${x}`);
};

find(5, successCallback, failureCallback);  // Success: 10
find('x', successCallback, failureCallback);  // Failure: 必须要是数字
```

# 2. Promise

由于传统的异步函数会出现各种问题，最大的问题就是嵌套回调，就是俗称的『回调地狱』。
所以在ES6新增了`promise`，可以这么理解`promise`

- 你向别人提出一个请求，别人立马给你一个Promise承诺
- 这个承诺一开始是`Pending`状态
- 过了一段时间，这个承诺可以成功`fulfilled`或者`resolved`， 带着成功的结果
- 或者，这个承诺被拒绝`rejected`，带着失败的原因

所以`promise`有三种状态
1. **pending**
2. **fulfilled/resolved**
3. **rejected**


```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    const flag = true;

    if (flag) {
      resolve();
    } else {
      reject();
    }
  }, 2000);
});

p.then(
  (v) => {
    console.log('Success');
  },
  (reason) => {
    console.log('Error');
  }
);
```

## 2.1 promise状态属性

promise的状态`[[PromiseState]]`  三种可能值
1. **pending**   初始状态
2. **fulfilled/resolved**   代表成功
3. **rejected**   代表失败

promise的状态只能改变一次，无论成功还是失败，都会有一个结果数据，一般来说成功的结果数据称为`value`，失败的结果数据称为`reason`

## 2.2 promise的结果属性
promise的最终会有一个结果属性`[[PromiseResult]]` 
这个结果属性保存Promise对象的『成功/失败』的结果

这个结果的值是从何而来？只能是resolve和reject的函数调用的结果而来

## 2.3 Promise的相关API

### 2.3.1 构造函数

```js
let p = new Promise((resolve, reject) => {})
// resolve函数： 内部定义成功时候去调用的函数
// reject函数：  内部定义失败的时候去调用的函数

let p = new Promise((resolve, reject) => {
 /* 这句话会立马执行*/
  console.log('同步立马执行');
});
```

> [!danger]
> 函数内部会立即同步调用，异步执行操作在执行器中执行

需要注意的是
```js
let p1 = new Promise((resolve, reject) => {
  console.log('zmg');
});
// 这个p1会永远处于pending状态
```
### 2.3.2 then方法

`Promise.prototype.then()`方法
```js
p.then(
  (value) => {
    console.log('Success');
  },
  (reason) => {
    console.log('Error');
  }
);
```

1. 第一个是onResolve函数：是处理成功的时候的回调函数
2. 第二个是onReject函数：是处理失败的时候的回调函数
3. 这个then的结果最终是返回一个新的promise对象，所以就才有后续的调用链的逻辑，可以一致then.then.then这种链式调用

### 2.3.3 cath方法

`Promise.prototype.catch`  
catch方法只能处理失败的回调函数
```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject();
  }, 2000);
});

p.catch((reason) => {
  console.log('ERROR');
});
```

### 2.3.4 resolve方法

注意这个是Promise的函数方法，`Promise.resolve`  这个不是那个回调函数

```js
// 如果传入的参数是非promise对象，则返回的结果是一个成功的promise对象
let result = Promise.resolve(23);
console.log(result);  // 一个成功的promise对象，结果为23

// 如果传入的参数是一个promise对象，则返回的结果就由promise对象的结果而定，但不管怎么说，他肯定返回的是promise对象
let p = Promise.resolve(
  new Promise((resolve, reject) => {
    // resolve('OK');  // 如果
    reject('ERROR');
  })
);

p.catch((reason) => {
  console.log(reason);
});
```

### 2.3.5 reject方法

同样的和`resolve`的方法，同样属于Promise的函数方法
返回的结果是一个失败的promise对象

### 2.3.6 all 方法

`Promise.all()`参数是n个promise的数组
返回一个新的promise，只有所有promise都成功才能成功，只要有一个失败就会直接失败

```js
let p1 = new Promise((resolve, reject) => {
  resolve('OK');
});
let p2 = new Promise((resolve, reject) => {
  resolve('Success');
});
let p3 = new Promise((resolve, reject) => {
  resolve('Success2');
});

let p = Promise.all([p1, p2, p3]);
p.then((value) => {
  console.log(value);
}); 
// [ 'OK', 'Success', 'Success2' ]
```

### 2.3.7 race() 方法

`Promise.race`参数 包含n个promise数组
返回一个新的promise，第一个完成的promise的结果状态就是最终的结果状态

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('OK');
  }, 2000);
});

let p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Success01');
  }, 4000);
});
let p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Success02');
  }, 6000);
});

let p = Promise.race([p1, p2, p3]);
p.then((value) => {
  console.log(value);
});
// 输出OK, 因为p1是第一个处理成功的
```


## 2.4 Promise相关的关键问题

### 2.4.1 如何改变promise的状态
- resolve(value) : 将当前的pending状态变更为==fulfilled==
- reject(reason) ： 将当前的pending状态变更为==reject==
- 抛出异常： 当前的pending转为==reject==
除了以上的方法，在JS中没有其他的办法去修改promise的状态

### 2.4.2 一个promise指定多个成功/失败回调函数，都会调用吗？

答案是肯定的，多个成功的回调或者失败回调函数都会去调用

```js
let p = new Promise((resolve, reject) => {
  resolve('OK');
});

p.then((value) => {
  console.log(value);
});
p.then((value) => {
  console.log(value);
});

// 执行了两次回调，最后都会输出结果
```

### 2.4.3 改变proise状态和指定回调函数谁先谁后？

1. 都有可能，正常情况下现制定回调再改变状态，但也有可能先改变状态再指定回调
2. 如何先改变状态再指定回调？
	1. 在执行器中直接调用resolve或者reject，本质就是让他尽快执行
	2. 延长更长的时间才去调用then
3. 什么时候得到数据？
	1. 如果先指定的回调，那么当状态发生改变时，回调函数就会调用，得到数据
	2. 如果先改变的状态，那么当指定回调的时候，回调函数就会被调用，得到数据

无论`resolve`/`reject`和`.then()`/`.catch()`哪个先发生，Promise的回调函数总是在==**微任务队列中异步执行**==

- a. 它不会阻塞主线程
- b. 它们会在当前宏任务完成后，在渲染更新或者下一个宏任务之前执行

### 2.4.4 promise.then()返回的新promise的结果状态有谁决定？

通俗的讲： 由then()中调用的那个回调函数来决定，resolve或者reject回调函数

```js
let p = new Promise((resolve, reject) => {
  resolve('OK');
});

let result = p.then(
  (value) => {
    // 1. 没有返回值的话，result的结果也是fulfilled， 但是值是undefined
    // console.log(value);
    // 2. 抛出异常的话，reuslt的结果就是reject，结果就是抛出异常的结果
    // throw 'Error';
    // 3. 返回非promise对象，则结果就是fulfilled, 结果就是返回的结果值
    // return 234;
    // 4. 返回的是一个promise对象，则结果就是该promise对象的
    return new Promise((resolve, reject) => {
      resolve('OK');
    });
  },
  (reason) => {}
);
console.log(result);
```

1. 没有返回值的话，result的结果也是fulfilled， 但是值是undefined
2. 抛出异常的话，reuslt的结果就是reject，结果就是抛出异常的结果
3. 返回非promise对象，则结果就是fulfilled, 结果就是返回的结果值
4. 返回的是一个promise对象，则结果就是该promise对象的结果

### 2.4.5 串联多个任务

因为then方法也是返回的promise对象，所以他就可以链式执行

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('OK');
  }, 2000);
});

let p2 = new Promise((resolve, reject) => {
  resolve('Success');
});

p1.then((value) => {
  return p2;
}).then((value) => {
  console.log(value);
});

```

### 2.4.6 异常穿透

只需要在最后面指定失败的回调即可，使用`catch()方法`

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('OK');
  }, 2000);
});

let p2 = new Promise((resolve, reject) => {
  reject('Error');
});

p1.then((value) => {
  return p2;
})
  .then((value) => {
    console.log(value);
  })
  .catch((reason) => {
    console.error(reason);
  });
```

### 2.4.7 如何中断promise链

如果需要的话，在某个then方法中返回一个pending状态的promise，那么pending状态的promise怎么表达呢？
`new Promise(() => {})`

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('OK');
  }, 2000);
});

let p2 = new Promise((resolve, reject) => {
  resolve('Success');
});

p1.then((value) => {
  return new Promise(() => {});  // 直接中断后面的链，因为这个是返回一个pending状态的promise
})
  .then((value) => {
    console.log(value);
  })
  .catch((reason) => {
    console.error(reason);
  });

```


# 3. 自己写一个Promise

自己写一个Promise，包含他所有的功能，这个难度有点高了，需要有点功力的

# 4. async 和 await

他们提供了一种更同步、更加易读的方式来编写和理解异步代码。

## 4.1 async 关键字

`async` 只能声明一个异步函数，这个函数有如下特点
- **返回值一定是Promise**： 无论`async` 函数内部返回什么，最后都会bai包装chen搞一个Promise对象

```js
// 示例 1: 返回一个非 Promise 值
async function greet() {
  return "Hello, Async!";
}

greet().then(message => {
  console.log(message); // 输出: Hello, Async!
});

// 示例 2: 返回一个 Promise
async function fetchData() {
  return new Promise(resolve => {
    setTimeout(() => resolve("Data fetched!"), 1000);
  });
}

fetchData().then(data => {
  console.log(data); // 输出: Data fetched! (1秒后)
});

// 示例 3: 抛出错误
async function throwError() {
  throw new Error("Something went wrong!");
}

throwError().catch(error => {
  console.error(error.message); // 输出: Something went wrong!
});
```

## 4.2 await 关键字

`await`关键字只能在`async`函数体内使用。他会暂停`async`函数的执行，直到后面的promise处理完毕，成功或者失败都可以，后面的promise处理成功即可

核心特点：
- **等待Promise处理完毕**  `await`表达式的值就是该Promise解决后的结果
- 不会阻塞主线程，只会在`async`函数内部阻塞而已
- **错误处理**： 如果`await`后面的promise处理失败了，它会像同步代码一样抛出错误，如果需要我们用`try...catch`进行捕获处理
```js
function simulateAsyncOperation(value, delay, shouldFail = false) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (shouldFail) {
        reject(new Error(`Operation failed for: ${value}`));
      } else {
        resolve(`Result of ${value}`);
      }
    }, delay);
  });
}

// 使用 async/await
async function processSteps() {
  console.log('Start processing...');

  try {
    // await 暂停 processSteps 函数的执行，直到 simulateAsyncOperation 解决
    const step1Result = await simulateAsyncOperation('Step 1', 2000);
    console.log(step1Result); // 2秒后输出: Result of Step 1

    const step2Result = await simulateAsyncOperation('Step 2', 1000);
    console.log(step2Result); // 1秒后输出: Result of Step 2

    // 尝试一个会失败的操作
    const step3Result = await simulateAsyncOperation('Step 3 (fail)', 500, true);
    console.log(step3Result); // 这行不会被执行，因为上面会抛出错误

    console.log('All steps completed successfully!'); // 这行也不会被执行

  } catch (error) {
    // 捕获 await 抛出的错误
    console.error('Error during processing:', error.message); // 输出: Error during processing: Operation failed for: Step 3 (fail)
  } finally {
    console.log('Processing finished.');
  }
}

processSteps(); // 调用异步函数

console.log('Main thread continues executing immediately.');
// 程序的执行顺序是：
// 1. Start processing...
// 2. Main thread continues executing immediately.
// 3. (2秒后) Result of Step 1
// 4. (1秒后) Result of Step 2
// 5. (0.5秒后) Error during processing: Operation failed for: Step 3 (fail)
// 6. Processing finished.
```

`async`和`await`的优势：
- 代码可读性大大增强
- 错误处理，比之前的promise.catch的方式更加直观
- 调试友好，可以进行debug
- 消除了回调地狱
- 减少了中间变量，避免多个Promise链中的`.then()`传递结果冗余

## 4.3 最佳实践

进行异步并行处理多个任务

```js
async function fetchMultipleData() {
  console.log('Fetching multiple data concurrently...');
  try {
    // 并行发起请求
    const promise1 = simulateAsyncOperation('Data A', 1500);
    const promise2 = simulateAsyncOperation('Data B', 1000);
    const promise3 = simulateAsyncOperation('Data C', 2000);

    // 等待所有 Promise 完成，无论顺序
    const results = await Promise.all([promise1, promise2, promise3]);
    console.log('All data fetched:', results);
  } catch (error) {
    console.error('Error fetching multiple data:', error.message);
  }
}
fetchMultipleData();
```