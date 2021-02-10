生成器是 ECMAScript 6 新增的一个极为灵活的结构，拥有一个函数块内暂停和恢复代码执行的能力。这种新能力具有深远的影响，比如，使用生成器可以自定义迭代器和实现协程。

## 生成器基础

生成器的形式是一个函数，函数名称前面加一个星号（`*`）表示它是一个生成器。只要是可以定义函数的地方，就可以定义生成器。

```javascript
// 生成器函数声明
function* generatorFn() {}
// 生成器函数表达式
let generatorFn = function* () {}
// 作为对象字面量方法的生成器函数
let foo = {
* generatorFn() {}
}
// 作为类实例方法的生成器函数
class Foo {
* generatorFn() {}
}
// 作为类静态方法的生成器函数
class Bar {
static * generatorFn() {}
}
```

注意：箭头函数不能用来定义生成器函数。

标识生成器函数的星号不受两侧空格的影响：

```javascript
// 等价的生成器函数：
function* generatorFnA() {}
function *generatorFnB() {}
function * generatorFnC() {}
// 等价的生成器方法：
class Foo {
*generatorFnD() {}
* generatorFnE() {}
}
```

调用生成器函数会产生一个生成器对象。生成器对象一开始处于暂停执行（suspended）的状态。与迭代器相似，生成器对象也实现了Iterator 接口，因此具有next()方法。调用这个方法会让生成器开始或恢复执行。

```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
console.log(g.next); // f next() { [native code] }
```

next()方法的返回值类似于迭代器，有一个done 属性和一个value 属性。函数体为空的生成器函数中间不会停留，调用一次next()就会让生成器到达done: true 状态。

```javascript
function* generatorFn() {}
let generatorObject = generatorFn();
console.log(generatorObject); // generatorFn {<suspended>}
console.log(generatorObject.next()); // { done: true, value: undefined }
```

value 属性是生成器函数的返回值，默认值为undefined，可以通过生成器函数的返回值指定：

```javascript
function* generatorFn() {
return 'foo';
}
let generatorObject = generatorFn();
console.log(generatorObject); // generatorFn {<suspended>}
console.log(generatorObject.next()); // { done: true, value: 'foo' }
```

生成器函数只会在初次调用next()方法后开始执行，如下所示：

```javascript
function* generatorFn() {
console.log('foobar');
}
// 初次调用生成器函数并不会打印日志
let generatorObject = generatorFn();
generatorObject.next(); // foobar
```

生成器对象实现了Iterable 接口，它们默认的迭代器是自引用的：

```javascript
function* generatorFn() {}
console.log(generatorFn);
// f* generatorFn() {}
console.log(generatorFn()[Symbol.iterator]);
// f [Symbol.iterator]() {native code}
console.log(generatorFn());
// generatorFn {<suspended>}
console.log(generatorFn()[Symbol.iterator]());
// generatorFn {<suspended>}
const g = generatorFn();
console.log(g === g[Symbol.iterator]());
// true
```

## 通过 yield 中断执行

yield 关键字可以让生成器停止和开始执行，也是生成器最有用的地方。生成器函数在遇到yield关键字之前会正常执行。遇到这个关键字后，执行会停止，函数作用域的状态会被保留。停止执行的生成器函数只能通过在生成器对象上调用next()方法来恢复执行：

```javascript
function* generatorFn() {
yield;
}
let generatorObject = generatorFn();
console.log(generatorObject.next()); // { done: false, value: undefined }
console.log(generatorObject.next()); // { done: true, value: undefined }
```

此时的yield 关键字有点像函数的中间返回语句，它生成的值会出现在next()方法返回的对象里。通过yield 关键字退出的生成器函数会处在done: false 状态；通过return 关键字退出的生成器函数会处于done: true 状态。

```javascript
function* generatorFn() {
yield 'foo';
yield 'bar';
return 'baz';
}
let generatorObject = generatorFn();
console.log(generatorObject.next()); // { done: false, value: 'foo' }
console.log(generatorObject.next()); // { done: false, value: 'bar' }
console.log(generatorObject.next()); // { done: true, value: 'baz' }
```

生成器函数内部的执行流程会针对每个生成器对象区分作用域。在一个生成器对象上调用next()不会影响其他生成器：

```javascript
function* generatorFn() {
yield 'foo';
yield 'bar';
return 'baz';
}
let generatorObject1 = generatorFn();
let generatorObject2 = generatorFn();
console.log(generatorObject1.next()); // { done: false, value: 'foo' }
console.log(generatorObject2.next()); // { done: false, value: 'foo' }
console.log(generatorObject2.next()); // { done: false, value: 'bar' }
console.log(generatorObject1.next()); // { done: false, value: 'bar' }
```

yield 关键字只能在生成器函数内部使用，用在其他地方会抛出错误。类似函数的return 关键字，yield 关键字必须直接位于生成器函数定义中，出现在嵌套的非生成器函数中会抛出语法错误：

```javascript
// 有效
function* validGeneratorFn() {
yield;
}
// 无效
function* invalidGeneratorFnA() {
function a() {
yield;
}
}
// 无效
function* invalidGeneratorFnB() {
const b = () => {
yield;
}
}
// 无效
function* invalidGeneratorFnC() {
(() => {
yield;
})();
}
```

**1. 生成器对象作为可迭代对象**

在生成器对象上显式调用next()方法的用处并不大。其实，如果把生成器对象当成可迭代对象，
那么使用起来会更方便：

```javascript
function* generatorFn() {
yield 1;
yield 2;
yield 3;
}
for (const x of generatorFn()) {
console.log(x);
}
// 1
// 2
// 3
```

在需要自定义迭代对象时，这样使用生成器对象会特别有用。比如，我们需要定义一个可迭代对象，而它会产生一个迭代器，这个迭代器会执行指定的次数。使用生成器，可以通过一个简单的循环来实现：

```javascript
function* nTimes(n) {
while(n--) {
yield;
}
}
for (let _ of nTimes(3)) {
console.log('foo');
}
// foo
// foo
// foo
```

传给生成器的函数可以控制迭代循环的次数。在n 为0 时，while 条件为假，循环退出，生成器函数返回。

**2. 使用 yield 实现输入和输出**

除了可以作为函数的中间返回语句使用，yield 关键字还可以作为函数的中间参数使用。上一次让生成器函数暂停的yield 关键字会接收到传给next()方法的第一个值。这里有个地方不太好理解——第一次调用next()传入的值不会被使用，因为这一次调用是为了开始执行生成器函数：

```javascript
function* generatorFn(initial) {
console.log(initial);
console.log(yield);
console.log(yield);
}
let generatorObject = generatorFn('foo');
generatorObject.next('bar'); // foo
generatorObject.next('baz'); // baz
generatorObject.next('qux'); // qux
```

yield 关键字可以同时用于输入和输出，如下例所示：

```javascript
function* generatorFn() {
return yield 'foo';
}
let generatorObject = generatorFn();
console.log(generatorObject.next()); // { done: false, value: 'foo' }
console.log(generatorObject.next('bar')); // { done: true, value: 'bar' }
```

因为函数必须对整个表达式求值才能确定要返回的值，所以它在遇到yield 关键字时暂停执行并计算出要产生的值："foo"。下一次调用next()传入了"bar"，作为交给同一个yield 的值。然后这个值被确定为本次生成器函数要返回的值。

yield 关键字并非只能使用一次。比如，以下代码就定义了一个无穷计数生成器函数：

```javascript
function* generatorFn() {
for (let i = 0;;++i) {
yield i;
}
}
let generatorObject = generatorFn();
console.log(generatorObject.next().value); // 0
console.log(generatorObject.next().value); // 1
console.log(generatorObject.next().value); // 2
console.log(generatorObject.next().value); // 3
console.log(generatorObject.next().value); // 4
console.log(generatorObject.next().value); // 5
...
```

假设我们想定义一个生成器函数，它会根据配置的值迭代相应次数并产生迭代的索引。初始化一个新数组可以实现这个需求，但不用数组也可以实现同样的行为：

```javascript
function* nTimes(n) {
for (let i = 0; i < n; ++i) {
yield i;
}
}
for (let x of nTimes(3)) {
console.log(x);
}
// 0
// 1
// 2
```

另外，使用while 循环也可以，而且代码稍微简洁一点：

```javascript
function* nTimes(n) {
let i = 0;
while(n--) {
yield i++;
}
}
for (let x of nTimes(3)) {
console.log(x);
}
// 0
// 1
// 2
```

这样使用生成器也可以实现范围和填充数组：

```javascript
function* range(start, end) {
while(end > start) {
yield start++;
}
}
for (const x of range(4, 7)) {
console.log(x);
}
// 4
// 5
// 6
function* zeroes(n) {
while(n--) {
yield 0;
}
}
console.log(Array.from(zeroes(8))); // [0, 0, 0, 0, 0, 0, 0, 0]
```

**3. 产生可迭代对象**

可以使用星号增强 yield 的行为，让它能够迭代一个可迭代对象，从而一次产出一个值：

```javascript
// 等价的generatorFn：
// function* generatorFn() {
// for (const x of [1, 2, 3]) {
// yield x;
// }
// }
function* generatorFn() {
yield* [1, 2, 3];
}
let generatorObject = generatorFn();
for (const x of generatorFn()) {
console.log(x);
}
// 1
// 2
// 3
```

与生成器函数的星号类似，yield 星号两侧的空格不影响其行为：

```javascript
function* generatorFn() {
yield* [1, 2];
yield *[3, 4];
yield * [5, 6];
}
for (const x of generatorFn()) {
console.log(x);
}
// 1
// 2
// 3
// 4
// 5
// 6
```

因为 `yield*` 实际上只是将一个可迭代对象序列化为一连串可以单独产出的值，所以这跟把yield
放到一个循环里没什么不同。下面两个生成器函数的行为是等价的：

```javascript
function* generatorFnA() {
for (const x of [1, 2, 3]) {
yield x;
}
}
for (const x of generatorFnA()) {
console.log(x);
}
// 1
// 2
// 3
function* generatorFnB() {
yield* [1, 2, 3];
}
for (const x of generatorFnB()) {
console.log(x);
}
// 1
// 2
// 3
```

`yield*` 的值是关联迭代器返回done: true 时的value 属性。对于普通迭代器来说，这个值是undefined：

```javascript
function* generatorFn() {
console.log('iter value:', yield* [1, 2, 3]);
}
for (const x of generatorFn()) {
console.log('value:', x);
}
// value: 1
// value: 2
// value: 3
// iter value: undefined
```

对于生成器函数产生的迭代器来说，这个值就是生成器函数返回的值：

```javascript
function* innerGeneratorFn() {
yield 'foo';
return 'bar';
}
function* outerGeneratorFn(genObj) {
console.log('iter value:', yield* innerGeneratorFn());
}
for (const x of outerGeneratorFn()) {
console.log('value:', x);
}
// value: foo
// iter value: bar
```

**4. 使用 yield* 实现递归算法**

`yield*` 最有用的地方是实现递归操作，此时生成器可以产生自身。看下面的例子：

```javascript
function* nTimes(n) {
if (n > 0) {
yield* nTimes(n - 1);
yield n - 1;
}
}
for (const x of nTimes(3)) {
console.log(x);
}
// 0
// 1
// 2
```

在这个例子中，每个生成器首先都会从新创建的生成器对象产出每个值，然后再产出一个整数。结果就是生成器函数会递归地减少计数器值，并实例化另一个生成器对象。从最顶层来看，这就相当于创建一个可迭代对象并返回递增的整数。

使用递归生成器结构和 `yield*` 可以优雅地表达递归算法。下面是一个图的实现，用于生成一个随机的双向图：

```javascript
class Node {
constructor(id) {
this.id = id;
this.neighbors = new Set();
}
connect(node) {
if (node !== this) {
this.neighbors.add(node);
node.neighbors.add(this);
}
}
}
class RandomGraph {
constructor(size) {
this.nodes = new Set();
// 创建节点
for (let i = 0; i < size; ++i) {
this.nodes.add(new Node(i));
}
// 随机连接节点
const threshold = 1 / size;
for (const x of this.nodes) {
for (const y of this.nodes) {
if (Math.random() < threshold) {
x.connect(y);
}
}
}
}
// 这个方法仅用于调试
print() {
for (const node of this.nodes) {
const ids = [...node.neighbors]
.map((n) => n.id)
.join(',');
console.log(`${node.id}: ${ids}`);
}
}
}
const g = new RandomGraph(6);
g.print();
// 示例输出：
// 0: 2,3,5
// 1: 2,3,4,5
// 2: 1,3
// 3: 0,1,2,4
// 4: 2,3
// 5: 0,4
```

图数据结构非常适合递归遍历，而递归生成器恰好非常合用。为此，生成器函数必须接收一个可迭代对象，产出该对象中的每一个值，并且对每个值进行递归。这个实现可以用来测试某个图是否连通，即是否没有不可到达的节点。只要从一个节点开始，然后尽力访问每个节点就可以了。结果就得到了一个非常简洁的深度优先遍历：

```javascript
class Node {
constructor(id) {
...
}
connect(node) {
...
}
}
class RandomGraph {
constructor(size) {
...
}
print() {
...
}
isConnected() {
const visitedNodes = new Set();
function* traverse(nodes) {
for (const node of nodes) {
if (!visitedNodes.has(node)) {
yield node;
yield* traverse(node.neighbors);
}
}
}
// 取得集合中的第一个节点
const firstNode = this.nodes[Symbol.iterator]().next().value;
// 使用递归生成器迭代每个节点
for (const node of traverse([firstNode])) {
visitedNodes.add(node);
}
return visitedNodes.size === this.nodes.size;
}
}
```

## 生成器作为默认迭代器

因为生成器对象实现了Iterable 接口，而且生成器函数和默认迭代器被调用之后都产生迭代器，所以生成器格外适合作为默认迭代器。下面是一个简单的例子，这个类的默认迭代器可以用一行代码产出类的内容：

```javascript
class Foo {
constructor() {
this.values = [1, 2, 3];
}
* [Symbol.iterator]() {
yield* this.values;
}
}
const f = new Foo();
for (const x of f) {
console.log(x);
}
// 1
// 2
// 3
```

这里，for-of 循环调用了默认迭代器（它恰好又是一个生成器函数）并产生了一个生成器对象。这个生成器对象是可迭代的，所以完全可以在迭代中使用。

## 提前终止生成器

与迭代器类似，生成器也支持“可关闭”的概念。一个实现Iterator 接口的对象一定有next()
方法，还有一个可选的return()方法用于提前终止迭代器。生成器对象除了有这两个方法，还有第三个方法：throw()。

```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
console.log(g.next); // f next() { [native code] }
console.log(g.return); // f return() { [native code] }
console.log(g.throw); // f throw() { [native code] }
return()和throw()方法都可以用于强制生成器进入关闭状态。
```

**1. return()**

return()方法会强制生成器进入关闭状态。提供给return()方法的值，就是终止迭代器对象的值：

```javascript
function* generatorFn() {
for (const x of [1, 2, 3]) {
yield x;
}
}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
console.log(g.return(4)); // { done: true, value: 4 }
console.log(g); // generatorFn {<closed>}
```

与迭代器不同，所有生成器对象都有return()方法，只要通过它进入关闭状态，就无法恢复了。后续调用next()会显示done: true 状态，而提供的任何返回值都不会被存储或传播：

```javascript
function* generatorFn() {
for (const x of [1, 2, 3]) {
yield x;
}
}
const g = generatorFn();
console.log(g.next()); // { done: false, value: 1 }
console.log(g.return(4)); // { done: true, value: 4 }
console.log(g.next()); // { done: true, value: undefined }
console.log(g.next()); // { done: true, value: undefined }
console.log(g.next()); // { done: true, value: undefined }
```

for-of 循环等内置语言结构会忽略状态为done: true 的IteratorObject 内部返回的值。

```javascript
function* generatorFn() {
for (const x of [1, 2, 3]) {
yield x;
}
}
const g = generatorFn();
for (const x of g) {
if (x > 1) {
g.return(4);
}
console.log(x);
}
// 1
// 2
```

**2. throw()**

throw()方法会在暂停的时候将一个提供的错误注入到生成器对象中。如果错误未被处理，生成器就会关闭：

```javascript
function* generatorFn() {
for (const x of [1, 2, 3]) {
yield x;
}
}
const g = generatorFn();
console.log(g); // generatorFn {<suspended>}
try {
g.throw('foo');
} catch (e) {
console.log(e); // foo
}
console.log(g); // generatorFn {<closed>}
```

不过，假如生成器函数内部处理了这个错误，那么生成器就不会关闭，而且还可以恢复执行。错误处理会跳过对应的yield，因此在这个例子中会跳过一个值。比如：

```javascript
function* generatorFn() {
for (const x of [1, 2, 3]) {
try {
yield x;
} catch(e) {}
}
}
const g = generatorFn();
console.log(g.next()); // { done: false, value: 1}
g.throw('foo');
console.log(g.next()); // { done: false, value: 3}
```

在这个例子中，生成器在try/catch 块中的yield 关键字处暂停执行。在暂停期间，throw()方
法向生成器对象内部注入了一个错误：字符串"foo"。这个错误会被yield 关键字抛出。因为错误是在生成器的try/catch 块中抛出的，所以仍然在生成器内部被捕获。可是，由于yield 抛出了那个错误，生成器就不会再产出值2。此时，生成器函数继续执行，在下一次迭代再次遇到yield 关键字时产出了值3。

注意：如果生成器对象还没有开始执行，那么调用 throw() 抛出的错误不会在函数内部被捕获，因为这相当于在函数块外部抛出了错误。

## 小结

生成器是一种特殊的函数，调用之后会返回一个生成器对象。生成器对象实现了Iterable 接口，
因此可用在任何消费可迭代对象的地方。生成器的独特之处在于支持yield 关键字，这个关键字能够暂停执行生成器函数。使用yield 关键字还可以通过next()方法接收输入和产生输出。在加上星号之后，yield 关键字可以将跟在它后面的可迭代对象序列化为一连串值。