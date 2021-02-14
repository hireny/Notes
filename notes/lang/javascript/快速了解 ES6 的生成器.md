生成器是 ECMAScript 6 新增的一个极为灵活的结构，拥有一个函数块内暂停和恢复代码执行的能力。

生成器函数提供了一个强大的选择：它允许你定义一个包含自有迭代算法的函数，同时它可以自动维护自己的状态。

使用 `function*`语法定义生成器函数，而且 `*` 不受两侧空格的影响。只要是可以定义函数的地方，就可以定义生成器。

```javascript
function* generator1() {}
```

<small>注意：箭头函数不能用来定义生成器函数。</small>

## 方法

调用生成器函数会产生一个符合可迭代协议和迭代器协议的生成器对象。生成器对象一开始处于暂停执行的状态。与迭代器相似，生成器也实现了 `Iterator` 接口。

### Generator.prototype.next()

`Generator` 对象具有 `next()` 方法，调用这个方法会让生成器开始或恢复执行，而且返回的是一个 `IteratorResult` 对象，具有 `done` 属性和 `value` 属性。默认情况下，返回的值为 `{ done: true, value: undefined }`。

```javascript
function* generator() {
    return 'sample';
}
const v1 = generator();
// 默认的迭代器是自引用的
console.log(v1);    // generator {<suspended>}
console.log(generator()[Symbol.iterator]());    // generator {<suspended>}
console.log(v1.next());    // { value: 'sample', done: true }
```

生成器还可以通过 `next(value)` 方法向内部传参，并且这个参数会变成 `yield` 的结果。

```javascript
function* generator() {
    let v = yield 10;
    yield v;
}
let v = generator();
console.log(v.next(1));    // { value: 10, done: false }
console.log(v.next(2));    // { value: 2, done: false }
```

第一次调用 `next()` 传入的值不会被使用，因为这一次调用时为了开始执行生成器函数，它会执行并返回第一个 `yield 10` 的结果。第二次 `next()` 调用，获得了 `2` 作为结果`let v = 2` 并返回 `yield v`。

### Generator.prototype.return()

`Generator` 提供的 `return()` 方法返回给定的值并结束生成器。因此可以使用 `return()` 方法提前终止生成器。

```javascript
function* generator() {
    yield 'sample';
    yield ;
    yield 'example';
    return 'instance';
}
const v1 = generator();
console.log(v1); // generator {<suspended>}
console.log(v1.return("quit")); // { done: true, value: "quit" }
console.log(v1); // generator {<closed>}
```

当 `return()` 方法没有提供参数，则返回对象的 `value` 属性的值为 `undefined`。

当使用 `return()` 方法关闭了生成器后就无法恢复，后续调用 `next()` 方法返回的值为 `{done: true, value: undefined}`。

```javascript
console.log(v1.next()); // {done: true, value: undefined}
```

`for-of` 循环等内置语言结构会忽略状态为 `done: true` 的 `IteratorObject` 内部返回的值。

```javascript
function* generator() {
    yield 'sample';
    yield ;
    yield 'example';
    return 'instance';
}
let v1 = generator1();
for (const x of v1) {
    if (x === 'example') {
        v1.return("quit");
    }
    console.log(x);
}
// sample
// undefined
// example
```

### Generator.prototype.throw()

生成器还提供了 `throw()` 方法用来向生成器中注入一个错误。如果内部未处理该错误，那么生成器就会关闭。

```javascript
function* generator() {
    yield 'sample';
    yield ;
    yield 'example';
    return 'instance';
}

const v = generator();
console.log(v);    // // generator {<suspended>}
try {
    v.throw(new Error("Throw Error"));
} catch (e) {
    console.log(e);    // Error: Throw Error
}
console.log(v);    // generator {<closed>}
```

但是生成器函数内部处理了这个错误，那么生成器就不会关闭，还可以恢复执行。错误处理会跳过对应的 `yield`，因此在这个例子中会跳过一个值。如下所示：

```javascript
function* generator1() {
    for (const x of ["sample", "example", "instance"]) {
        try {
            yield x;
        } catch (e) {
            console.log("Error caught!");
        }
    }
}
const v = generator();
console.log(v.next());    // { value: "sample", done: false}
v.throw(new Error("Throw Error"));    // 
console.log(v);    // generator {<suspended>}
console.log(v.next);    // { value: "instance", done: false}
```

在这里，向生成器内部注入一个错误，该错误会被 `yield` 关键字抛出，并且在生成器内部的 `try-catch` 语句块中处理了该错误。此时，生成器函数还是会继续执行，但下次调用 `next()` 方法就不会产生 `example` 值，而是产生 `instance` 值。

<small>注意：如果生成器对象还没有开始执行，那么调用 `throw()` 抛出的错误不会在函数内部被捕获，因为这相当于在函数块外部抛出了错误。</small>


## yield

ECMAScript 6 提供了 `yield` 关键字用来让生成器函数暂停，并且函数作用域的状态会保留。生成器对象会通过调用 `next()` 方法让生成器函数恢复执行。`yield` 省略表达式会返回 `undefined`：

```javascript
function* generator() {
    yield 'sample';
    yield ;
    yield 'example';
    return 'instance';
}
let v1 = generator();
console.log(v1.next());    // { value: 'sample', done: false }
console.log(v1.next());    // { value: undefined, done: false }
console.log(v1.next());    // { value: 'example', done: false }
console.log(v1.next());    // { value: 'instance', done: true }
console.log(v1.next());    // { value: undefined, done: true }
```

生成器对象可当成可迭代对象，可以使用 `for...of` 循环。

```javascript
function* generator() {
    for (const x of ["sample", "example", "instance"]) {
        yield x;
    }
}
for (const x of generator1()) {
    console.log(x);
}
// sample
// example
// instance
```

<small>注意：`yield` 关键字只能在生成器函数内部使用且必须位于生成器函数定义中，出现在嵌套的非生成器函数中会抛出错误。</small>

还可以使用 `yield*` 语法增强 `yield` 的行为，用于委托给另一个 `generator` 或可迭代的对象。

```javascript
function* generator() {
    for (const v of [1, 2, 3]) {
        yield v;
    }
}
```

等价于

```javascript
function* generator() {
    yield* [1, 2, 3];
}
```

等价于

```javascript
function* generator1() {
        yield 1;
        yield 2;
    }
function* generator2() {
    yield* generator1();
    yield 3;
}
```

<small>注意，`yield` 与 `*` 两侧的空格不影响其行为。</small>

由于 `yield*` 可以调用另外一个生成器，所以通过 `yield*` 可以实现递归调用。

```javascript
function* recs(n) {
    if (n > 1) {
        yield* f(n-1);
    }
    yield n;
}
for (const x of recs(3)) {
    console.log(x);
}
// 1
// 2
// 3
```

使用递归生成器结构和 `yield*` 可以优雅地表达递归算法。

## 小结

生成器是一种特殊的函数，调用之后会返回一个生成器对象。生成器对象实现了 `Iterable` 接口，因此应用场景与迭代器一样。生成器支持 `yield` 关键字，用于暂停执行生成器函数，与 `next()` 方法搭配产生一系列值。`yield*` 表达式可以在生成器中调用其它生成器。