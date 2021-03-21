在 ECMAScript 6 之前，可以使用 `Object` 的对象属性作为键，再使用属性引用值的方式来实现 `key/value` 存储； 而 ECMAScript 6 规范中，新增了 `Map` 和 `WeakMap` 两种集合类型来存储 `key/value`。

## Map

ECMAScript 6 新增的 `Map` 集合可以使用 `new` 和 `Map` 构造函数创建一个空映射：

```javascript
const map = new Map();
```

也可以在创建映射时，就将键值对按照迭代顺序插入到新映射实例中：

```javascript
// 使用嵌套数组初始化映射
const m1 = new Map([
    ["name", "小赵"],
    ["age", 12],
    ["sex", "男"]
]);
alert(m1.size); // 3

// 使用自定义迭代器初始化映射
const m2 = new Map({
    [Symbol.iterator]: function*() {
        yield ["name", "小赵"];
        yield ["age", 12];
        yield ["sex", "男"];
    }
});
alert(m2.size); // 3

// 映射期待的键/值对，无论是否提供
const m3 = new Map([[]]);
alert(m3.has(undefined)); // true
alert(m3.get(undefined)); // undefined
```

### API

初始化之后，在想要往映射表中添加 `key/value`，就需要使用 `set()` 方法：

```javascript
const map = new Map();
map.set("name", "小赵");
map.set("age", 24);
map.set("sex", "男");
```

然而，`set()` 方法返回的是 `Map` 实例，因此可以初始化可以如下声明：

```javascript
const map = new Map().set("name", "小赵");
map.set("age", 24).set("sex", "男");
```

存放进去的 `key/value` 可以通过 `get()` 和 `has()` 方法进行查询，`has()` 方法用于判断 `key` 是否存在 `Map` 中，而 `get()` 用于获取 `key` 对应的 `value`：

```javascript
console.log(map.has("score"));    // false
console.log(map.get("name"));    // "小赵"
```

可以通过 `size` 属性获取 `Map` 中 `key/value` 的数量：

```javascript
console.log(map.size);    // 3
```

如果要删除  `Map` 中的 `key/value`，可以使用 `delete()` 和 `clear()` 方法，`delete()` 方法用于删除指定的 `key`，而 `clear()` 用于清除 `Map` 中的所有 `key/value`：

```javascript
map.delete("age");
console.log(map.size);    // 2
map.clear();
console.log(map.size);    // 0
```

如果需要以插入顺序返回 `Map` 中每个元素的 `[key, value]` 数组，可以使用 `entries()` 方法或者 `Symbol.iterator` 属性获取 `Map` 中的迭代器。

```javascript
const m = new Map([
    ["name", "小赵"],
    ["age", 12],
    ["sex", "男"]
]);
console.log(m.entries === m[Symbol.iterator]); // true
for (let kv of m.entries()) {
    console.log(kv);
}
// [ 'name', '小赵' ]
// [ 'age', 12 ]
// [ 'sex', '男' ]
for (let kv of m[Symbol.iterator]()) {
    console.log(kv);
}
// [ 'name', '小赵' ]
// [ 'age', 12 ]
// [ 'sex', '男' ]
for (let [k, v] of m.entries()) {
    console.log(k + " = " + v);
}
// name = 小赵
// age = 12
// sex = 男
```

由于 `entries()` 是默认迭代器，可以直接对 `Map` 实例使用扩展操作，把 `Map` 转换成数组：

```javascript
const m = new Map([
    ["name", "小赵"],
    ["age", 12],
    ["sex", "男"]
]);
console.log([...m]); // [ [ 'name', '小赵' ], [ 'age', 12 ], [ 'sex', '男' ] ]
```

也可以使用 `forEach(callback, opt_thisArg)` 方法并传入回调，依次迭代每个 `key/value`。传入的回调接收可选的第二个参数，这个参数用于重写回调内部 `this` 的值：


```javascript
m.forEach((val, key) => console.log(`${key} -> ${val}`));
// name -> 小赵
// age -> 12
// sex -> 男
```

`keys()` 和 `values()` 分别返回以插入顺序生成 `key` 和 `value` 的迭代器：

```javascript
for (let k of m.keys()) {
    console.log(k);
}
// name
// age
// sex
for (let v of m.values()) {
    console.log(v);
}
// 小赵
// 12
// 男
```

键和值在迭代器遍历时是可以修改的，但映射内部的引用则无法修改。当然，这并不妨碍修改作为键或值的对象内部的属性，因为这样并不影响它们在映射实例中的身份：

```javascript
const m1 = new Map([
    ["name", "小赵"]
]);
// 作为键的字符串原始值是不能修改的
for (let key of m1.keys()) {
    key = 10010;
    console.log(key); // 10010
    console.log(m1.get("name")); // 小赵
}
const key2 = {id: 1};
const m2 = new Map([
    [key2, "小新"]
]);
// 修改了作为键的对象的属性，但对象在映射内部仍然引用相同的值
for (let key of m2.keys()) {
    key.id = 10086;
    console.log(key); // {id: 10086}
    console.log(m2.get(key2)); // 小新
}
console.log(key2); // {id: 10086}
```

### 键的相等性

在 `Map` 中，键的比较是基于 `someValueZero` 算法：

- `NaN` 是与 `NaN` 相等的（虽然 `NaN !== NaN`），剩下所有其它的值是根据 `===` 运算符的结果判断是否相等。
- 在目前的 ECMAScript 规范中，`-0` 和 `+0` 被认为是相等的，尽管这在早期的草案中并不是这样的。

### 与 Object 比较

`Map` 与 `Object` 类似，都允许按 `key` 存取 `value`、删除 `key`、检测 `key` 是否绑定了 `value`。但 `Map` 和 `Object` 也在内存和性能上确实存在显著的差别。

通常存储单个 `key/value` 所占用的内存会随着 `key` 的数量线性增加，也会受不同浏览器的影响，但是对于规定大小的内存，`Map` 可以比 `Object` 多存储 `key/value`；而要涉及大量插入，专为存储 `key/value` 设计的 `Map`，它的性能更佳。

对于 `Map` 而言，存储 `Map` 的 `key` 可以是任意值，而且 `key` 的顺序是由插入的顺序决定的；`delete()` 方法也比插入和查找要更快。

而 `Object` 的 `key` 只能是 `String` 或 `Symbol`，而且插入的顺序是无序的；而且在 `Object` 中删除属性，会出现一些伪删除，包括把属性值设置为 `undefined` 或 `null`。

## WeakMap

`WeakMap` 也是 ECMAScript 6 新增的映射表，而且还是 `Map` 的子集。但是 `WeakMap` 存放的 `key` 必须是对象类型，而且还是 `“弱映射”`，这会使得 `key` 所指的值没有其他地方引用的时候，它就会 GC 回收；`value` 可以是任意类型。

`WeakMap` 与 `Map` 的 API 基本都相同，但是 `WeakMap` 中的键只能是 `Object` 或者继承自 `Object` 的类型，尝试使用非对象设置键会抛出 `TypeError`，值的类型没有限制。

但 `WeakMap` 已经弃用 `clear()` 方法，但可以通过创建一个空的 `WeakMap` 并替换原对象来实现。

```javascript
let wm = new WeakMap();
vm.set("name", "小钱").set("age", 24).set("sex", "男");

// 清空 WeakMap
vm = new WeakMap();
```

### 弱键

`WeakMap` 中的 `"weak"` 表示的是 `key` 存在，`key/value` 就会存在 `WeakMap` 中，并当作 `value` 的引用，不会被 GC 回收；而 `key` 不存在时，不会阻止 GC 回收。

```javascript
const wm = new WeakMap();
wm.set({}, "weak value");
```

`set()` 方法初始化了一个新对象并将它用作一个字符串的键。因为没有指向这个对象的其他引用，所以当这行代码执行完成后，这个对象键就会被当作垃圾回收。然后，这个键/值对就从弱映射中消失了，使其成为一个空映射。在这个例子中，因为值也没有被引用，所以这对键/值被破坏以后，值本身也会成为垃圾回收的目标。再看一个稍微不同的例子：

```javascript
const wm = new WeakMap();
const container = {
    key: {}
};
wm.set(container.key, "weak val");
function removeReference() {
    container.key = null;
}
```

这一次，`container` 对象维护着一个 `container.key` 的引用，因此这个对象键不会被 GC 回收。不过，如果调用了 `removeReference()`，就会摧毁 `key` 的最后一个引用，就会被 GC 回收。

### 不可迭代键

因为 `WeakMap` 中的 `key/value` 任何时候都可能被销毁，也没有提供 `entries()`、`keys`、`values` 等可迭代的方法，也没有提供 `clear()` 一次性清除所有 `key/value` 的方法。

之所以限制只能用对象作为 `key`，是为了保证只有通过 `key` 的引用才能取得值。如果允许原始值，那就没办法区分初始化时使用的字符串字面量和初始化之后使用的一个相等的字符串了。

### 应用

由于 `WeakMap` 中 `key` 的弱引用，因此可以在相应的许多方面应用它。

#### 私有变量

私有变量是不会被外界访问到的，不能被多个实例共享，因此可以使用 `WeakMap` 来实现。如下所示：

```javascript
const wm = new WeakMap();
class User {
    constructor(id) {
        this.idProperty = Symbol('id');
        this.setId(id);
    }
    setPrivate(property, value) {
        const privateMembers = wm.get(this) || {};
        privateMembers[property] = value;
        wm.set(this, privateMembers);
    }
    getPrivate(property) {
        return wm.get(this)[property];
   }
    setId(id) {
        this.setPrivate(this.idProperty, id);
    }
    getId() {
        return this.getPrivate(this.idProperty);
    }
}
const user = new User(123);
alert(user.getId()); // 123
user.setId(456);
alert(user.getId()); // 456
// 并不是真正私有的
alert(wm.get(user)[user.idProperty]); // 456
```

对于上面的实现，外部只需要拿到对象实例的引用和弱映射，就可以取得 `“私有”` 变量了。为了避免这种访问，可以用一个闭包把 `WeakMap` 包装起来，这样就可以把弱映射与外界完全隔离开了：

```javascript
const User = (() => {
    const wm = new WeakMap();
    class User {
        constructor(id) {
            this.idProperty = Symbol('id');
            this.setId(id);
        }
        setPrivate(property, value) {
            const privateMembers = wm.get(this) || {};
            privateMembers[property] = value;
            wm.set(this, privateMembers);
        }
        getPrivate(property) {
            return wm.get(this)[property];
        }
        setId(id) {
            this.setPrivate(this.idProperty, id);
        }
        getId(id) {
            return this.getPrivate(this.idProperty);
        }
    }
    return User;
})();
const user = new User(123);
alert(user.getId()); // 123
user.setId(456);
alert(user.getId()); // 456
```

这样，拿不到弱映射中的健，也就无法取得弱映射中对应的值。虽然这防止了前面提到的访问，但整个代码也完全陷入了ES6 之前的闭包私有变量模式。

#### DOM 节点元数据

使用 `WeakMap` 实例存储的 `DOM` 元素时在被清除时，对应的 `WeakMap` 记录会被 GC 回收 ，因此非常适合保存关联元数据。来看下面这个例子，其中使用了常规的 `Map`：

```javascript
const m = new Map();
const lb = document.querySelector('#login');
// 给这个节点关联一些元数据
m.set(lb, {disabled: true});
```

假设在上面的代码执行后，页面被 JavaScript 改变了，原来的登录按钮从 `DOM` 树中被删掉了。但由于映射中还保存着按钮的引用，所以对应的 `DOM` 节点仍然会逗留在内存中，除非明确将其从映射中删除或者等到映射本身被销毁。

如果这里使用的是弱映射，如以下代码所示，那么当节点从 `DOM` 树中被删除后，垃圾回收程序就可以立即释放其内存（假设没有其他地方引用这个对象）：

```javascript
const wm = new WeakMap();
const lb = document.querySelector('#login');
// 给这个节点关联一些元数据
wm.set(lb, {disabled: true});
```