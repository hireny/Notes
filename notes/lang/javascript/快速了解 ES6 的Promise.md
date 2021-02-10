ECMAScript 6 增加了对 `Promises/A+` 规范的完善支持，即 `Promise` 类型。一经推出，`Promise` 就大受欢迎，成为了主导性的异步编程机制。所有现代浏览器都支持 ES6 期约，很多其他浏览器 API 也以期约为基础。

`Promise` 是 ECMAScript 6 新增的引用类型，表示一个异步操作的最终完成或者失败。`Promise` 创建对象时的语法如下：

```javascript
let promise = new Promise(function(resolve, reject) => {
    // 异步处理
    //   resolve(value)              // fulfilled
    // or
    //   reject(error)    // rejected
});
```

由以上可知，`Promise` 是一个有状态的对象，返回的 `Promise` 对象可能处于以下几种状态之一：

- 待定（`pending`）：初始状态，即没有被兑现，也没有被拒绝。
- 兑现（`fulfilled`，有时也称为“解决”，`resolved`）：意味着操作成功完成。
- 已拒绝（`rejected`）：意味着操作失败。

待定（pending）是 `Promise` 的最初始状态。在待定状态下， `Promise` 可以落定（settled）为代表成功的兑现（fulfilled）状态，或者代表失败的拒绝（rejected）状态。无论落定为哪种状态都是不可逆的。只要从待定转换为兑现或拒绝，`Promise`  的状态就不再改变。而且，也不能保证 `Promise` 必然会脱离待定状态。因此，组织合理的代码无论 `Promise` 解决（resolve）还是拒绝（reject），甚至永远处于待定（pending）状态，都应该具有恰当的行为。

重要的是， `Promise` 的状态是私有的，不能直接通过JavaScript 检测到。这主要是为了避免根据读取到的 `Promise` 的状态，以同步方式处理 `Promise` 实例。另外，`Promise`  的状态也不能被外部JavaScript 代码修改。这与不能读取该状态的原因是一样的： `Promise` 故意将异步行为封装起来，从而隔离外部的同步代码。

## Promise 实例方法

`Promise` 实例的方法是连接外部同步代码与内部异步代码之间的桥梁。这些方法可以访问异步操作返回的数据，处理 `Promise` 对象成功和失败的结果，连续对 `Promise` 求值，或者添加只有 `Promise` 进入终止状态时才会执行的代码。

### Promise.prototype.then()

`Promise.prototype.then()` 方法返回一个 `Promise` 实例，是为 `Promise` 实例添加处理的主要方法。

```javascript
Promise.prototype.then(onFulfilled, onRejected)
```

- `onFulfilled`：可选参数。当 `Promise` 状态变为 `fulfilled` 时调用的函数。传入的任何非函数类型都会被静默忽略。
- `onRejected`：可选参数。当 `Promise` 状态变为 `rejected` 时调用的函数。传入的任何非函数类型都会被静默忽略。 

当值提供 `onRejected` 参数时，需要在 `onFulfilled` 参数位置上传 `undefined`。有助于避免在内存中创建多余的对象，对期待函数参数的类型系统也是一个交代。

### Promise.prototype.catch()

`Promise.prototype.catch()`方法返回一个 `Promise` 实例，并且处理拒绝的情况。

```javascript
Promise.prototype.catch(onRejected)
```

- `onRejected`：当 `Promise` 被 `rejected` 时，被调用的一个 `Function`。该函数拥有一个参数 `reason`，表示 `rejection` 的原因。

如果 `onRejected` 抛出一个错误或返回一个本身失败的 `Promise`，通过 `catch()` 返回的 `Promise` 被 `rejected`；否则，它将显示为成功（`resolved`）。该方法可以用于 `promise` 组合中的错误处理。事实上，这个方法就是一个语法糖，调用它就相当于 `Promise.prototype.then(null, onRejected)`。

### Promise.prototype.finally()

`Promise.prototype.finally()` 方法返回一个 `Promise` 实例，并且在 `Promise` 处理结束时，无论什么状态，都会执行指定的回调函数。这为在 `Promise` 是否成功完成后都需要执行的代码提供了一种方式。也避免了在 `then()` 和 `catch()` 中出现冗余代码。

```javascript
Promise.prototype.finally(onFinally)
```

- `onFinally`：当 `Promise` 接收后调用的 `Function`。

但是上述的 `onFinally` 函数中无法知道 `Promise` 是处于什么状态，所以这个方法主要用于添加清理代码。

### 错误处理

状态为 `rejected` 的 `Promise` 类似于 `throw()` 表达式，都代表一种程序状态，即需要中断或者特殊处理。`Promise` 执行期间抛出错误会导致 `rejected`，对应的 `Error` 会成为拒绝的理由。

```javascript
let p = new Promise((resolve, reject) => reject(throw Error('Error Value')));
```

在 `Promise` 实例中抛出错误会从消息队列中异步抛出，并不会阻止运行时继续执行同步指令：


```javascript
Promise.reject(Error('Error Value'));
console.log('initial value');
// initial value
// Uncaught (in promise) Error: Error Value
```

因此，`Promise` 只能通过 `catch` 实例方法捕获错误，`try-catch` 语句块不好使。`Promise` 的 `then` 和 `catch` 方法就相当于 `try-catch` 了。

## Promise 静态方法

在 `Promise` 引用类型中存在多个静态方法，除了 `Promise.reject()` 与 `Promise.resolve()` 外，都会接收一个 `Promise` 的 `iterable` 类型的参数，合成的 `Promise` 行为取决于内部 `Promise` 的行为。

<small>注意：`Promise.any()` 方法还是实验性，尚未完全支持。</small>

### Promise.reject()

`Promise.reject()` 会返回一个状态为 `rejected` 的 `Promise` 实例并抛出一个异步错误。

```javascript
Promise.reject(reason);
```

- `reason`：表示 `Promise` 实例的失败信息。

使用 `Promise.reject()` 与 `new Promise((resolve, reject) => reject())` 相比，实际上是一样的。而在传入错误原因时，可以传入一个 `Error` 实例，使它成为返回的 `Promise` 实例的错误理由：

```javascript
Promise.reject(new Error("Promise instance failed")).then(function() {
    // not called
}, function(error) {
    console.error(error);    // Stacktrace
});
```

```javascript
setTimeout(console.log, 0, Promise.reject(Promise.resolve()));
// Promise <rejected>: Promise <resolved>
```

### Promise.resolve()

通过 `Promise.resolve()` 静态方法，可以实例化一个状态为 `fulfilled` 的 `Promise` 实例。与 `new Promise((resolve, reject) => resolve())` 实际上是一样的。

```javascript
Promise.resolve(value)
```

- `value`：将被 `Promise` 实例对象解析的参数，也可以是一个 `Promise` 实例，或者是一个 `thenable`。

使用这个静态方法，可以把任何值都转换为状态是 `fulfilled` 的 `Promise` 的实例，而传入多余的值会被忽略。

```javascript
setTimeout(console.log, 0, Promise.resolve());
// Promise <resolved>: undefined
setTimeout(console.log, 0, Promise.resolve(4, 5, 6));
// Promise <resolved>: 4
```

当传入的参数本身是一个 `Promise` 实例时，该行为类似于一个空包装。因此，`Promise.resolve()` 可以说是一个幂等方法。

```javascript
let p = Promise.resolve(7);
setTimeout(console.log, 0, p === Promise.resolve(p));
// true
setTimeout(console.log, 0, p === Promise.resolve(Promise.resolve(p)));
// true
```

这个幂等性会保留传入期约的状态：

```javascript
let p = new Promise(() => {});
setTimeout(console.log, 0, p); // Promise <pending>
setTimeout(console.log, 0, Promise.resolve(p)); // Promise <pending>
setTimeout(console.log, 0, p === Promise.resolve(p)); // true
```

然而，本身能够包装任何非 `Promise` 值，包括错误对象，并将其转为解决的 `Promise` 实例，这会导致不符合预期的行为，这点要注意：

```javascript
let p = Promise.resolve(new Error('foo'));
setTimeout(console.log, 0, p);
// Promise <resolved>: Error: foo
```

### Promise.all()

`Promise.all()` 方法接收具有 `Promise` 实例的 `iterable` 类型的集合，返回一个新 `Promise` 实例。

当所有的 `Promise` 实例状态都变为 `fulfilled`，返回的 `Promise` 实例的状态变为 `fulfilled`，而且解决值就是所有包含 `Promise` 实例解决值的数组，按照迭代器顺序。

```javascript
let p = Promise.all([
    Promise.resolve(3),
    Promise.resolve(),
    Promise.resolve(4)
]);
p.then((values) => setTimeout(console.log, 0, values)); // [3, undefined, 4]
```

当有一个 `Promise` 实例的状态变为 `rejected`，返回的 `Promise` 实例的状态变为 `rejected`，且返回的拒绝理由为状态变为 `rejected` 的 `Promise` 实例的拒绝理由。之后的 `Promise` 实例再拒绝不会影响最终 `Promise` 的拒绝理由。不过，这并不影响所有包含 `Promise` 实例正常的拒绝操作。`Promise.all`返回的 `Promise` 实例会静默处理所有包含 `Promise` 实例的拒绝操作，如下所示：

```javascript
// 虽然只有第一个期约的拒绝理由会进入
// 拒绝处理程序，第二个期约的拒绝也
// 会被静默处理，不会有错误跑掉
let p = Promise.all([
    Promise.reject(3),
    new Promise((resolve, reject) => setTimeout(reject, 1000))
]);
p.catch((reason) => setTimeout(console.log, 0, reason)); // 3
// 没有未处理的错误
```


### Promise.allSettled()

ECMAScript 2020 新增了 `Promise.allSettled()` 方法。`Promise.allSettled()` 方法接收的 `iterable` 集合中，`Promise` 实例的状态都为 `fulfilled` 或 `rejected`。

```javascript
Promise.allSettled([
    Promise.resolve(3),
    new Promise((resolve, reject) => {
        setTimeout(reject, 1000);
    }),
    Promise.reject('reject promise')
]).then((results) => console.log(results));

/**
 * 输出结果
 * [
 *   { status: 'fulfilled', value: 3 },
 *   { status: 'rejected', reason: undefined },
 *   { status: 'rejected', reason: 'reject promise' }
 * ]
 */
```

当想要确保所有 `Promise` 实例都结束时，`Promise.allSettled()` 方法就很有用。 

### Promise.race()

`Promise.race()` 方法接收的 `iterable` 集合中，只要有一个 `Promise` 实例的状态变为 `fulfilled` 或者 `rejected`，就会包装其解决值或拒绝理由并返回新 `Promise` 实例。

```javascript
// 解决先发生，超时后的拒绝被忽略
let p1 = Promise.race([
    Promise.resolve(3),
    new Promise((resolve, reject) => setTimeout(reject, 1000))
]);
setTimeout(console.log, 0, p1); // Promise <resolved>: 3
// 拒绝先发生，超时后的解决被忽略
let p2 = Promise.race([
    Promise.reject(4),
    new Promise((resolve, reject) => setTimeout(resolve, 1000))
]);
setTimeout(console.log, 0, p2); // Promise <rejected>: 4
```

与 `Promise.all()` 类似，拒绝的 `Promise` 实例中的拒绝理由会成为 `Promise.race()` 的拒绝理由，`iterable` 集合中余下的 `Promise` 实例会被静默处理，不会有错误跑掉。

```javascript
// 虽然只有第一个期约的拒绝理由会进入，拒绝处理程序，第二个期约的拒绝也会被静默处理，不会有错误跑掉
let p = Promise.race([
    Promise.reject(3),
    new Promise((resolve, reject) => setTimeout(reject, 1000))
]);
p.catch((reason) => setTimeout(console.log, 0, reason)); // 3
// 没有未处理的错误
```

## 链式调用

`Promise` 提供的 `then()`、`catch()` 和 `finally()` 方法都会返回一个新的 `Promise` 实例，新的 `Promise` 实例又可以调用这些方法，可以连缀方法调用构成链式调用。如下所示：

```javascript
let p = new Promise((resolve, reject) => {
    console.log('initial promise');
    reject();
}).catch(() => {
    console.log('reject handler');
}).then(() => {
    console.log('resolve handler');
}).finally(() => {
    console.log('finally handler');
});

/**
 * 输出结果
 * initial promise
 * reject handler
 * resolve handler
 * finally handler
 */
```

链式调用会按照顺序执行，每个 `Promise` 都会等待前一个 `Promise` 解决，然后实例化一个新的 `Promise` 实例返回它，这样的结构可以简洁地将异步任务串行化。

```javascript
let p = new Promise((resolve, reject) => {
    console.log('initial promise');
    setTimeout(resolve, 1000);
}).then(() => new Promise((resolve, reject) => {
    console.log('first promise');
    setTimeout(resolve, 3000);
})).then(() => new Promise((resolve, reject) => {
    console.log("second promise");
    setTimeout(resolve, 2000);
})).then(() => new Promise((resolve, reject) => {
    console.log("third promise");
    setTimeout(resolve, 1000);
}));
/**
 * 输出结果
 * initial promise
 * first promise
 * second promise
 * third promise
 */
```