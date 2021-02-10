ECMAScript 6 规范新增的迭代器是一种新的遍历机制，能够更清晰、高效、方便地实现迭代。所有通过迭代器访问的结构都实现了 `Iterator` 接口，且能够在 `for...of` 循环中应用。

## 可迭代协议

可迭代协议指的是实现 `Iterable` 接口并同时支持迭代的自我识别能力和创建实现 `Iterator` 接口的对象的能力。任何实现 `Iterable` 接口的对象都有一个 `Symbol.iterator` 属性，这个属性引用默认迭代器。默认迭代器是一个函数，调用之后会产生一个实现 `Iterator` 接口的对象。内置的可迭代对象有 `String`、`Array`、`Map` 和 `Set` 等等。

下面给出几个例子，可以检查是否存在默认迭代器属性：

```javascript
let obj = {};
let num = 3;
// Object 和 Number 都没有实现迭代器函数
console.log(obj[Symbol.iterator]);    // undefined
console.log(num[Symbol.iterator]);    // undefined

let arr = ["小明", "小李"];
let set = new Set().add('小明').add('小李');
// Array 和 Set 都实现了迭代器函数
console.log(arr[Symbol.iterator]);    // [Function: values]
console.log(set[Symbol.iterator]);    // [Function: values]

// 下面获取迭代器
console.log(arr[Symbol.iterator]());    // Object [Array Iterator] {}
console.log(set[Symbol.iterator]());    // [Set Iterator] { '小明', '小李' }
```

实际上，实现可迭代协议的迭代对象都可以在如下场景使用：

- `for-of` 循环
- 数组解构
- 扩展操作符
- `Array.from()`
- 集合与映射
- 由 `Promise` 组成的可迭代对象
- `yield*` 操作符

```javascript
let arr = ["小玲", "小霞", "小星", "小民"];
// for-of循环
for (let v of arr) {
    console.log(v);
}
// 数组解构
let [a1, a2, a3, a4] = arr;
console.log(a1, a2, a3, a4);    // 小玲 小霞 小星 小民
// 扩展操作符
let arr2 = [...arr];
console.log(arr2);    // [ '小玲', '小霞', '小星', '小民' ]
// Array.from()
let arr3 = Array.from(arr);
console.log(arr3);    // [ '小玲', '小霞', '小星', '小民' ]
```

如果对象原型链上的父类实现了 `Iterable` 接口，那这个对象也会实现 `Iterable` 接口：

如果对象原型链上的父类实现了 Iterable 接口，那这个对象也就实现了这个接口：

```javascript
class List extends Array {}
let list = new List("小玲", "小霞", "小星", "小民");
for (let v of list1) {
    console.log(v);
}
// 小玲
// 小霞
// 小星
// 小民
```

## 迭代器协议

迭代器协议定义了产生一些列值的标准方法。而对象只有实现了 `next()` 方法才能成为迭代器。对象使用 `next()` 遍历数据时，每次成功都会返回一个 `IteratorResult` 对象，其中包含迭代返回的下一个值；所有的值都迭代完后，会返回一个默认返回值；若不调用 `next()`，则无法知道迭代器的当前位置。

`next()` 方法返回的 `IteratorResult` 包含两个属性：

- `done`：布尔值。表示是否还可以调用 `next()` 获取下一个值。 迭代器遍历完毕会返回 `true`；否则返回 `false`。
- `value`：迭代器返回的值。`done` 为 `true` 可省略或 `undefined`。

可以通过以下简单的数组来演示：

```javascript
let arr = ["小明", "小李"];
// 获取迭代器
let iter = arr[Symbol.iterator]();
// 执行迭代
console.log(iter.next());    // { value: '小明', done: false }
console.log(iter.next());    // { value: '小李', done: false }
console.log(iter.next());    // { value: undefined, done: true }
console.log(iter.next());    // { value: undefined, done: true }
```

在迭代期间修改了迭代对象，会反映在迭代对象上，如下所示：

```javascript
let arr = ["小明", "小李"];
// 获取迭代器
let iter = arr[Symbol.iterator]();
console.log(iter.next());    // { value: '小明', done: false }
console.log(iter.next());    // { value: '小李', done: false }
arr.splice(2, 0, "小虎");    
console.log(iter.next());    // { value: '小虎', done: false }
console.log(iter.next());    // { value: undefined, done: true }
console.log(iter.next());    // { value: undefined, done: true }
```

这是因为使用 `next()` 获取对象只是使用游标来记录历程。

<small>注意：迭代器维护着一个指向可迭代对象的引用，因此迭代器会阻止垃圾回收程序回收可迭代对象。</small>


## 自定义迭代器

因为每个迭代器也实现了Iterable 接口，所以它们可以用在任何期待可迭代对象的地方，比如for-of 循环：

每个实现了 `Iterable` 接口的迭代器都可以用在任何可迭代对象的地方，如 `for-of` 循环。下面自定义一个实现迭代器的类：

```javascript
class List {
    constructor() {
        this.index = 0;
        this.data = arguments;
    }

    [Symbol.iterator]() {
        return {
            next: () => {
                if (this.index < this.data.length) {
                    return { value: this.data[this.index++], done: false};
                }
                this.index = 0;
                return {done: true, value: undefined};
            }
        }
    }
}

let list = new List("小玲", "小霞", "小星", "小民");
let iter = list[Symbol.iterator]();
console.log(iter.next());    // { value: '小玲', done: false }
console.log(iter.next());    // { value: '小霞', done: false }
console.log(iter.next());    // { value: '小星', done: false }
console.log(iter.next());    // { value: '小民', done: false }
console.log(iter.next());    // { done: true, value: undefined }
```

任何实现 `Iterable` 接口的数据结构都可以实现 `Iterator` 接口的结构 “消费”。

## 提前终止迭代器

当使用 `for-of` 循环迭代器时，如果不想迭代下去，可以通过 `break`、`continue`、`return` 或 `throw` 提前退出。

```javascript
for (let v of list) {
    if (v === '小星') {
        console.log("提前退出");
        break;
    }
    console.log(v);    
}
// 小玲
// 小霞
// 提前退出
```

## 总结

迭代器是 ES6 新增的重要功能，提供了一个可以由任意对象实现的接口，并连续返回迭代对象中的每个值。任何实现 `Iterable` 接口的对象都有一个 `Symbol.iterator` 属性，这个属性引用默认迭代器。当一个对象中存在 `Symbol.iterator` 时，该对象就会被认为是可迭代对象。