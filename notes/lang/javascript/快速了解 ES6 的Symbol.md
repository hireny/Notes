`Symbol` 是 ECMAScript 6 新增的基本数据类型。`Symbol` 提供的实例是唯一、不可变的。它的用途可以确保对象属性使用唯一标识符，不会发生属性冲突的危险。

`Symbol` 提供 `Symbol()` 函数来返回 `Symbol` 类型的值。语法如下所示：

```javascript
Symbol(desc)
```

- `desc`    可选的字符串类型的参数。是用来描述 `Symbol` 的。

通过 `Symbol()` 函数初始化，可以通过 `typeof` 运算符识别 `Symbol` 类型，当然也可以传入参数对 `Symbol` 进行描述，但与其 `Symbol` 定义或标识完全无关。

```javascript
let s1 = Symbol();
console.log(typeof s1);    // symbol
let s2 = Symbol('symbol instance');
let s3 = Symbol('symbol instance');
console.log(s1 == s2);    // false
console.log(s2 == s3);    // false
```

注意，`Symbol` 不能通过 `new` 关键字像构造函数一样创建实例，并抛出 `TypeError` 错误。这样做是为了避免创建 `Symbol` 包装对象。

```javascript
let s = new Symbol();    // TypeError: Symbol is not a constructor
```

但是，可以使用 `Object()` 函数创建一个 `Symbol` 包装器对象。

```javascript
let s = Symbol("symbol instance");
console.log(typeof s);    // symbol
let o = Object(s);
console.log(typeof o);    // object
```

## 全局共享的 Symbol

`Symbol()` 函数创建的 `Symbol` 实例不是全局可用的 `Symbol` 类型。如果需要全局可用的 `Symbol` 实例，可以使用 `Symbol.for(key)` 方法和 `Symbol.keyFor(key)` 方法。

`Symbol.for()` 方法创建的 `Symbol` 会被放入一个全局的 `Symbol` 注册表中。`Symbol.for()` 并不是每次都会创建一个新的 `Symbol` 实例，它会首先检查给定的 `key` 是否已经在注册表中了。如果是，则会直接返回上次存储的`Symbol` 值。否则，它会在新键一个。

```javascript
let gs1 = Symbol.for('gloal symbol');       //  创建新符号 
let gs2 = Symbol.for('global symbol');  //  重用已有符号
console.log(gs1 === gs2);  // true 
```

可以使用 `Symbol.keyFor()` 来查询全局注册表，如果查到与 `key` 对应的 `Symbol` 实例，则返回该 `Symbol` 实例的 `key` 值，否则返回 `undefined`。

```javascript
//  创建全局符号 
let s = Symbol.for('foo'); 
console.log(Symbol.keyFor(s));   // foo 
//  创建普通符号 
let s2 = Symbol('bar'); 
console.log(Symbol.keyFor(s2));  // undefined 
```

如果传给 `Symbol.keyFor()` 的不是符号，则该方法抛出 `TypeError`： 

```javascript
Symbol.keyFor(123); // TypeError: 123 is not a symbol 
```

## Symbol 作为属性

`Symbol` 可以作为属性来使用。如下所示：

```javascript
let s1 = Symbol("property one");

let o = {
    [s1]: 'symbol value one'
};
console.log(o);    // { [Symbol(property one)]: 'symbol value one' }
```

`Object.getOwnPropertySymbols()` 方法会让你在查找给定对象的 `Symbol` 属性时返回一个 `Symbol` 类型的数组。

```javascript
let s1 = Symbol("property one"),
    s2 = Symbol("property two");

let o = {
    [s1]: 'symbol value one',
    [s2]: 'symbol value two',
    name: 'symbol property sample',
    index: 12
};
console.log(Object.getOwnPropertySymbols(o));    // [ Symbol(property one), Symbol(property two) ]
```

使用 `Object.getOwnPropertyNames()` 正好相反。如下所示：

```javascript
console.log(Object.getOwnPropertyNames(o));    // [ 'name', 'index' ]
```

## Symbol 原型

所有 `Symbol` 都继承自 `Symbol.prototype`，而它也提供了 `constructor` 和 `description` 两个属性。`constructor` 属性会返回实例原型的函数，默认为 `Symbol` 函数；而 `description` 属性是一个只读字符串，返回 `Symbol` 对象的描述。

`Symbol.prototype` 也提供了 `toString()` 和 `valueOf()` 方法用于返回 `Symbol` 描述符的字符串方法和对象的原始值。

`Symbol.prototype[@@toPrimitive]` 会将 `Symbol` 对象转换为原始值。语法如下：

```javascript
Symbol()[Symbol.toPrimitive](hint)
```

`Symbol` 的 `[@@toPrimitive]()` 方法返回该 `Symbol` 对象原始值作为 `Symbol` 数据形式。`hint` 参数未被使用。

JavaScript 调用 `[@@toPrimitive]()` 方法将一个对象转换为原始值表示。你不需要自己调用 `[@@toPrimitive]()` 方法；当对象需要被转换为原始值时，JavaScript 会自动地调用该方法。

## 内置的 Symbol 属性

除了自己创建的 `Symbol`，JavaScript 还内建了一些在 ECMAScript 5 之前没有暴露给开发者的 `Symbol`，它们代表了内部语言行为。这些 `Symbol` 最重要的用途之一是重新定义它们，从而改变原生结构的行为。如重新定义 `Symbol.iterator` 属性的值，来改变 `for-of` 在迭代对象时的行为。

注意 ，在提到 ECMAScript 规范时，经常会引用符号在规范中的名称，前缀为 `@@`。比如，`@@iterator` 指的就是 `Symbol.iterator`。

### Symbol.iterator

该属性返回一个对象默认的迭代器，被 `for-of` 使用。下面自定义一个迭代器。

```javascript
class List {
    constructor() {
        this.index = 0;
        this.data = arguments;
    }
    *[Symbol.iterator]() {
        while(this.index < this.data.length) {
            yield this.data[this.index++];
        }
    }
}
function iter() {
    let list = new List("小玲", "小霞", "小星", "小民");
    for (const v of list) {
        console.log(v);
    }
}
iter();
// 小玲
// 小霞
// 小星
// 小民
```

如上所示，通过 `for-of` 循环对一个对象进行迭代时，`@@iterator` 方法在不传参的情况下被调用，返回的迭代器用于获取要迭代的值。

### Symbol.asyncIterator

一个返回对象默认的异步迭代器的方法。被 `for await of` 使用。

该方法返回一个对象默认的异步迭代器，由 `for-await-of` 语句使用。

```javascript
class List {
    constructor() {
        this.index = 0;
        this.data = arguments;
    }
    async *[Symbol.asyncIterator]() {

        while(this.index < this.data.length) {
            yield new Promise((resolve) => resolve(this.data[this.index++]));
        }
    }
}
async function asyIter() {
    let list = new List("小玲", "小霞", "小星", "小民");
    for await(const  v of list) {
        console.log(v);
    }
}
asyIter();
// 小玲
// 小霞
// 小星
// 小民
```

如上所示，`for-await-of` 循环会利用 `List` 类中的函数执行异步迭代操作。

注意，`Symbol.asyncIterator` 是 `ES2018` 规范定义的，只有新版本的浏览器支持它。

### Symbol.match 

该方法指定了用正则表达式去匹配字符串。`String.prototype.match()` 方法会调用此函数。

```javascript
console.log('It\'s a real horror story'.match(/horror/));
/*
[
  'horror',
  index: 12,
  input: "It's a real horror story",
  groups: undefined
]
*/
```



给这个方法传入非正则表达式值会导致该值被转换为RegExp对象。如果想改变这种行为，让方法直接使用参数，则重新定义 `Symbol.match` 函数以取代默认对正则表达式求值的行为，从而让match()方法使用非正则表达式实例。Symbol.match 函数接收一个参数，就是调用 match()方法的字符串实例。返回的值没有限制：

```javascript
class StringMatcher {
    static [Symbol.match](target) {
        return target.includes('horror');
    }
    constructor(str) {
        this.str = str;
    }
    [Symbol.match](target) {
        return target.includes(this.str);
    }
}
console.log('It\'s a real horror story'.match(StringMatcher)); // true
console.log('It\'s a real relaxing story'.match(StringMatcher)); // false
console.log('It\'s a real horror story'.match(new StringMatcher('horror'))); // true
console.log('It\'s a real horror story'.match(new StringMatcher('relaxing'))); // false
```

### Symbol.replace

该属性指定了当一个字符串替换所匹配字符串时所调用的方法。供 `String.prototype.replace()` 方法调用此方法，用于替换子字符串。

```javascript
console.log('It\'s a real horror story'.replace(/horror/, 'relaxing'));
// It's a real relaxing story
```

而正则表达式的原型上默认由 `Symbol.replace` 函数定义。给 `String.prototype.replace` 方法传入非正则表达式值会导致该值被转换为 `RegExp` 对象。

可以重新定义 `Symbol.replace` 函数，用来取代默认行为。两种定义 `Symbol.replace`  函数的方式如下所示：

```javascript
class StringReplacer {
    static [Symbol.replace](target, replacement) {
        return target.split('horror').join(replacement);
    }
    constructor(str) {
        this.str = str;
    }
    [Symbol.replace](target, replacement) {
        return target.split(this.str).join(replacement);
    }
}
console.log('It\'s a real horror story'.replace(StringReplacer, 'qux'));    // It's a real qux story

console.log('It\'s a real horror story'.replace(new StringReplacer('horror'), 'relaxing'));    // It's a real relaxing story
```

`Symbol.replace` 函数接收两个参数，即调用 `replace()` 方法的字符串实例和替换字符串。

### Symbol.search 

该方法会返回正则表达式在字符串中匹配的索引。`String.prototype.search()` 方法会调用此方法，用于查找索引。

```javascript
console.log('It\'s a real horror story'.search(/horror/));    // 12
```

可以重新定义 `Symbol.search` 函数，用来取代默认行为，从而让 `search()` 方法使用非正则表达式的实例。如下所示：

```javascript
class StringSearcher {
    static [Symbol.search](target) {
        return target.indexOf('qux');
    }
    constructor(str) {
        this.str = str;
    }
    [Symbol.search](target) {
        return target.indexOf(this.str);
    }
}

console.log('It\'s a real horror story'.search(StringSearcher)); // -1
console.log('It\'s a real qux story'.search(StringSearcher)); // 12
console.log('qux, It\'s a real horror story'.search(StringSearcher)); // 0

console.log('It\'s a real horror story'.search(new StringSearcher('qux'))); // -1
console.log('It\'s a real horror story'.search(new StringSearcher('horror'))); // 12
console.log('It\'s a real horror story'.search(new StringSearcher('s'))); // 3
```

`Symbol.search` 函数接收一个参数，就是调用 `search()` 方法的字符串实例。

### Symbol.split

该方法会通过一个正则表达式的索引，来分隔字符串。`String.prototype.split()` 方法会调用此方法。

```javascript
console.log('It\'s a real horror story'.split(/ /)); // 正则匹配的是空格
// [ "It's", 'a', 'real', 'horror', 'story' ]
```

可以重新定义 `Symbol.split` 函数，用来取代默认行为，从而让 `split()` 方法使用非正则表达式实例。

```javascript
class StringSplitter {
    static [Symbol.split](target) {
        return target.split('qux');
    }
    constructor(str) {
        this.str = str;
    }
    [Symbol.split](target) {
        return target.split(this.str);
    }
}
console.log('It\'s a real qux story'.split(StringSplitter));    // [ "It's a real ", ' story' ]
console.log('It\'s a real horror story'.split(new StringSplitter(' ')));    // [ "It's", 'a', 'real', 'horror', 'story' ]
```

`Symbol.split` 函数接收一个参数，就是调用 `split()` 方法的字符串实例。

### Symbol.hasInstance 

该属性用于判断某对象是否为某构造器对象的实例。可以用 `Symbol.hasInstance` 函数自定义 `instanceof` 在某个类上的行为。

`instanceof` 的场景如下：

```javascript
class Instance {}

let ist = new Instance();
console.log(ist instanceof Instance);    // true
```

而 `ES6` 中，`instanceof` 会使用 `Symbol.hasInstance` 属性来确定关系。

```javascript
console.log(Instance[Symbol.hasInstance](ist));    // true
```

`Symbol.hasInstance` 属性定义在 `Function` 的原型上，默认所有函数和类都可以调用。由于 `instanceof` 会在原型链上寻找这个属性定义，就跟在原型链上寻找其他属性一样，因此可以在继承的类上通过静态方法重新定义这个属性：

```javascript
class Instance {}
class SubInstance extends Instance {
    static [Symbol.hasInstance]() {
        return false;
    }
}
let sist = new SubInstance();
console.log(Instance[Symbol.hasInstance](sist)); // true
console.log(sist instanceof Instance);           // true
console.log(SubInstance[Symbol.hasInstance](sist)); // false
console.log(sist instanceof SubInstance);           // false
```

### Symbol.isConcatSpreadable

 `Symbol.isConcatSpreadable` 用于配置某对象作为 `Array.prototype.concat()` 方法的参数时是否展开其数组元素。

```javascript
const arr1 = ['a', 'b', 'c'];
const arr2 = [1, 2, 3];
let arr3 = arr1.concat(arr2);
console.log(arr3);    // [ 'a', 'b', 'c', 1, 2, 3 ]
```

这是在 `Symbol.isConcatSpreadable = true` 时的情形，如果设置为 `false`，就不会展开数组。

```javascript
arr2[Symbol.isConcatSpreadable] = false;
let arr4 = arr1.concat(arr2);
console.log(arr4);
/*
[
  'a',
  'b',
  'c',
  [ 1, 2, 3, [Symbol(Symbol.isConcatSpreadable)]: false ]
]

*/
```

由上可知，对于数组对象，使用 `concat` 在默认情况下会将数组中元素展开进行连接。重置 `Symbol.isConcatSpreadable` 可以改变默认行为。

### Symbol.unscopables 

`Symbol.unscopables` 指定对象值，其对象所有的以及继承属性，都会从关联对象的 `with` 环境绑定中排除。设置 `Symbol.unscopables` 并让其映射对应属性的键值为 `true`，就可以阻止该属性出现在 `with` 环境绑定中。如下所示：

```javascript
let o = {
    name: '小玲'
};
with (o) {
    console.log(name); // 小玲
}
o[Symbol.unscopables] = {
    name: true
};
with (o) {
    console.log(name); // ReferenceError: name is not defined
}
```

注意，不推荐使用 `with`，因此也不推荐使用 `Symbol.unscopables`。

### Symbol.species 

`Symbol.species` 作为创建派生对象的构造函数。用 `Symbol.species` 定义静态的 `getter` 方法，可以覆盖新创建实例的原型定义：

```javascript
class Array1 extends Array {}
class Array2 extends Array {
    static get [Symbol.species]() {
        return Array;
    }
}
let a1 = new Array1();
console.log(a1 instanceof Array); // true
console.log(a1 instanceof Array1);   // true
a1 = a1.concat('species');
console.log(a1 instanceof Array); // true
console.log(a1 instanceof Array1);   // true
let a2 = new Array2();
console.log(a2 instanceof Array); // true
console.log(a2 instanceof Array2);   // true
a2 = a2.concat('species');
console.log(a2 instanceof Array); // true
console.log(a2 instanceof Array2);   // false
```

### Symbol.toPrimitive 

`Symbol.toPrimitive` 是一个内置的 `Symbol` 值，它是作为对象的函数值属性存在的，当一个对象转换为对应的原始值时，会调用此函数。

`Symbol.toPrimitive` 属性可以将对象转换为相应的原始值。很多内置操作都会尝试将值将对象转换为原始值，包括字符串、数值和未指定的原始类型。如下所示：

```javascript
class Sample1 {}
let s1 = new Sample1();
console.log(+s1);    // NaN
console.log(`${s1}`);    // [object Object]
console.log(s1 + "");    // [object Object]
class Sample2 {
    constructor() {
        this[Symbol.toPrimitive] = function(hint) {
            switch (hint) {
                case 'number': return 77;
                case 'string': return 'hello world!';
                default: return true;
            }
        }
    }
}
let s2 = new Sample2();
console.log(+s2);    // 77
console.log(`${s2}`);    // hello world!
console.log(s2 + "");    // true
```

### Symbol.toStringTag 

`Symbol.toStringTag` 用于创建对象的默认字符串描述。由 `Object.prototype.toString()` 调用。许多内置类型已经指定了这个值，但自定义类实例可以明确定义：

```javascript
// 没有定义 `Symbol.toStringTag` 时
class StringTag {
    constructor() {
    }
}
let a = new StringTag();
console.log(a);   // StringTag {}
console.log(a.toString());    // [object Object]

// 定义 `Symbol.toStringTag` 时
class StringTag {
    constructor() {
        this[Symbol.toStringTag] = 'StringTag';
    }
}
let a = new StringTag();
console.log(a);    // StringTag { [Symbol(Symbol.toStringTag)]: 'StringTag' }
console.log(a.toString());    // [object StringTag]
```