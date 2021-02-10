ECMAScript 变量是松散类型的，意思是变量可以用于保存任何类型的数据。每个变量只不过是一个用于保存任意值的命令占位符。在 ECMAScript 6 之前，声明变量使用 `var` 关键字，ECMAScript 6 新增了 `const` 和 `let` 关键字用以声明变量。

## var

使用 `var` 关键字声明变量：

```javascript
var msg;    // 不初始化时，变量保存一个特殊值 undefined
var msg = "Hello";    // 初始化
msg = 3.14;    // 可以改变保存的值和值的类型，但不推荐
```

### 作用域

`var` 声明的变量的作用域是函数作用域，在函数退出时销毁变量：

```javascript
function test() {
    var msg = "Hello";
}
test();
console.log(msg); // ReferenceError: msg is not defined
```

但是省略 `var` 会创建全局变量：

```javascript
function test() {
    msg = "Hello";
}
test();
console.log(msg); // Hello
```

但这样操作会使得全局变量很难维护，不推荐这样做。

### var 声明提升

在 `var` 声明变量之前调用变量不会报错，因为 `var` 声明的变量会自动提升到函数作用域顶部：

```javascript
function test() {
    console.log(name);
    var name = "小明";
}
test(); // undefined
```

## let

`let` 跟 `var` 的作用差不多，但也有着非常重要的区别。最主要的区别是：`let` 声明变量的作用域是块作用域，`var` 声明变量的作用域是函数作用域。

```javascript
function varScope() {
    if (true) {
        var name = "var name";
        console.log(name);    // var name
    }
    console.log(name);    // var name
}
function letScope() {
    if (true) {
        let name = "let name";
        console.log(name);    // let name 
    }
    console.log(name);    // ReferenceError: name is not defined
}
```

从上面的例子中知道，`let` 关键字声明变量的作用域仅限于块内部。块作用域是函数作用域的子集，适用于 `var` 的作用域限制同样也适用于 `let`。

### 重复声明

`let` 不允许同一个块作用域中出现 **重复声明**。出现时会导致报错：

```javascript
function varScope() {
    var name = "var name";
    var name = "var name";
}
function letScope() {
    let name = "let name";
    let name = "let name";    // SyntaxError: Identifier 'name' has already been declared
}
```

然而，嵌套使用相同标识符相当于创建一个新的块作用域，因此，相同的标识符不会报错，这是因为同一个块中没有重复声明：

```javascript
function letScope() {
    let name = "let name 1";
    if (true) {
        let name = "let name 2";
        console.log(name);    // let name 2
    }
    console.log(name);    // let name 1
}
```

### 暂时性死区

`var` 声明的变量的初始值是 `undefined`。然而 `let` 与 `var` 不同，通过 `let` 声明的变量不会在作用域中被提升。直到声明被执行时才初始化，初始化前访问变量会导致 `ReferenceError`，`let` 声明变量之前的执行瞬间被称为 “暂时性死区”

let 与var 的另一个重要的区别，就是let 声明的变量不会在作用域中被提升。

```javascript
function deadZone() {
    console.log(varname);    // undefined
    console.log(letname);    // ReferenceError
    let letname = "let name";
    var varname = "var name";
}
```

### 全局声明

`var` 在 **全局作用域** 中声明的变量会成为 `window` 对象的属性；而 `let` 声明的变量不会称为 `window` 对象的属性。

```javascript
var varname = 'var name';
console.log(window.name); // 'var name'
let letname = "let name";
console.log(window.letname); // undefined
```

但 `let` 声明仍然是在全局作用域中发生的，相应变量会在页面的声明周期内存续。因此，为了避免 `SyntaxError`，必须确保页面不会重复声明同一个变量。

## const

`const` 声明的变量也是块作用域，与 `let` 基本相同，只是 `const` 声明变量时必须同时初始化变量。声明的变量的值是只读的，修改 `const` 声明的变量会导致运行时错误。

```javascript
// 声明常量要有一个初始值
const name;    // SyntaxError: Missing initializer in const declaration

// 块作用域
const name = "const name 1";
if (true) {
    const name = "const name 2";
    console.log(name);    // const name 2
}
console.log(name);    // const name 1

name = "const name 2";    // TypeError: Assignment to constant variable.

// 尝试重新声明
const name = "const name 3";    // SyntaxError: Identifier 'name' has already been declared
```

`const` 也可以声明对象和数组。当声明的变量是一个对象时，不修改对象引用，而是修改对象内部的属性不违反 `const` 的限制。

```javascript
const student = {};
student.name = "小明";
```

`const` 实质上保存的并不是变量的值不得改动，而是变量指向的内存地址不得改动。

在 `for` 循环中不能使用 `const` 声明变量，因为迭代时变量会自增就会抛出 `TypeError` 错误：

```java
for (const i = 0; i < 10; i++) {}
// TypeError: Assignment to constant variable.
```

然而，在 `for-in` 或 `for-of` 循环中使用不会报错。


```javascript
for (const key in {name: "小明", age: 24}) {
    console.log(key);    // name, age
}
for (const value of ["name", "age", "id", "sex"]) {
    console.log(value);    // name, age, id, sex
}
```

## 总结

`var` 声明变量带来了许多问题，ECMAScript 6 增加的 `let` 和 `const` 对语言更精确地声明作用域和语义提供了更好的支持，提升了代码质量。新增了两个关键字后，以不再需要 `var` 来声明变量，这样使得变量有明确的作用域、声明位置，以及不变的值。但声明变量时最好使用 `const`，这样使得变量在浏览器运行时强制保持不变。只在提前知道未来会有修改时，再使用 `let`。