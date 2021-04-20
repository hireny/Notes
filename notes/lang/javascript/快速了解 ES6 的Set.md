在 ECMAScript 6 之前，可以使用数组来存储值，而 ECMAScript 6 新增了 `Set` 和 `WeakSet` 两种集合类型。

## Set

`Set` 集合可以使用 `new` 来创建一个空集合：

```javascript
const s = new Set();
```

也可以在创建 `Set` 时就初始化实例：

```javascript
const s = new Set(["小赵", "小明", "小霞"]);
```

也可以在初始化 `Set` 实例时自定义迭代器。

```javascript
const s = new Set({
    [Symbol.iterator]: function*() {
        yield "小赵";
        yield "小明";
        yield "小霞";
    }
});
```

可以使用 `size` 属性获取 `Set` 实例的数量。

初始化后，可以使用 `has()` 方法查询是否存在某元素：

```javascript
s.has("小郑");    // false
```

可以使用 `add()` 方法添加元素：

```javascript
s.add("小玲");
```

使用 `delete()` 方法删除元素，并返回一个 `boolean` 值，用来判断是否存在要删除的值：

```javascript
s.delete("小明");    // true
```

使用 `clear()` 方法清空 `Set` 实例：

```javascript
s.clear();
```

当然，`Set` 与 `Map` 类似，可以包含任何 JavaScript 数据类型作为值，并且使用了 `SameValeZero` 操作，使用严格对象相等的标准来检查值的匹配性。

```javascript
const s = new Set();
// SameValueZero 检查意味着独立的实例不会冲突
alert(s.has(function() {})); // false
```

`Set` 提供一个 `Iterator` 来以插入的顺序生成集合内容，通过 `values()` 方法以及其别名方法 `keys()` 方法获取迭代器，也可以通过 `Symbol.iterator` 属性来获取：

```javascript
const s = new Set(["小赵", "小明", "小霞"]);
s.values === s[Symbol.iterator]    // true
s.keys === s[Symbol.iterator]    // true
for (let value of s.values()) {
    console.log(value);
}
// 小赵
// 小明
// 小霞
for (let value of s[Symbol.iterator]()) {
    console.log(value);
}
// 输出同上
```

也可以将集合转换为数组：

```javascript
const s = new Set(["小赵", "小明", "小霞"]);
console.log([...s]); // ["小赵", "小明", "小霞"]
```

集合也提供了 `entries()` 方法，按照插入顺序返回所有元素的值的 `[value, value]` 数组，与 `Map` 相似，但这里的每个值的键和值相等。

也可以使用 `forEach()` 方法遍历集合：

```javascript
const s = new Set(["小赵", "小明", "小霞"]);
s.forEach((v1, v2) => console.log(`${v1} -> ${v2}`));
// 小赵 -> 小赵
// 小明 -> 小明
// 小霞 -> 小霞
```

## WeakSet

`WeakSet` 也是 ECMAScript 6 新增的集合，也是 `Set` 的子集，但同时，`WeakSet` 中的值没有被引用时，就会被 GC 回收。

这里，使用 `new` 关键字实例化空的 `WeakSet`：

```javascript
const ws = new WeakSet();
```

也可以在创建时就初始化，但是 `WeakSet` 的值只能是 `Object` 或者继承自 `Object` 的子类：

```javascript
const val1 = {id: 1},
val2 = {id: 2},
val3 = {id: 3};
// 使用数组初始化弱集合
const ws1 = new WeakSet([val1, val2, val3]);
```

如果使用非对象的值会抛出 `TypeError`：

```javascript
const ws2 = new WeakSet([val1, "BADVAL", val3]);
// TypeError: Invalid value used in WeakSet
typeof ws2;
```

这里之所以限制只能使用 `Object` 作为值，是为了保证只有通过值对象的引用才能取得值。如果允许原始值，就没办法区分初始化时使用的字符串字面量和初始化之后使用的一个相等的字符串了。

初始化后的操作，也可以同 `Set` 一样，使用 `add()` 方法添加元素，`has()` 方法查询元素，`delete()` 方法删除元素。

### 弱值

`WeakSet` 与 `Set` 不同之处在于集合中的值不被引用时，不会阻止 GC 回收。如下所示：

```javascript
const ws = new WeakSet();
ws.add({});
```

`add()` 方法中添加一个新的元素，但是这个元素没有其它引用，所以该元素会被当作垃圾回收，成为一个空集合。但当 `{}` 元素被一个对象引用时，就不会被当作垃圾回收掉，如下所示：

```javascript
const ws = new WeakSet();
const q = {v:{}};
ws.add(q.v);
```

上面的例子中，`q.v` 就不会成为 GC 回收的目标。但是，将最后一个引用摧毁掉，如将 `q.v` 指向 `null`，GC 就可以把这个值清理回收。

### 不可迭代值

与 `WeakMap` 一样，`WeakSet` 中的值在任何时候也可能会被销毁，所以没有提供其迭代的能力。因此，也就不会在不知道其对象引用的情况下从 `WeakSet` 中取到值。

### 应用

这里可以使用 `WeakSet` 将打上同一标签的元素存储起来，这样在以后的查询中就比较方便，如打上禁用标签的 `DOM` 元素都存储在 `WeakSet` 中：

```javascript
const des = new WeakSet();
const lb = document.querySelector('#login');
// 通过加入对应集合，给这个节点打上“禁用”标签
des.add(lb);
```

这样，在 `DOM` 元素从 `DOM` 树中删除时，`WeakSet` 实例中的元素也会被 GC 回收，释放内存。

这里只所以没有使用 `Set` 是因为，在 `DOM` 树中删除后，`Set` 实例不会让 GC 回收该元素。