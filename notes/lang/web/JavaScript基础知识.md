# 注释

单行注释：

```javascript
// 单行注释
```

多行注释：

```javascript
/*
 多行
 注释
*/
```

历史上 JavaScript 可以兼容 HTML 注释，因此 `<!--` 和 `-->` 也可以是单行注释。

```javascript
x = 1; <!-- x = 2;
--> x = 3;
```

只有 `x = 1` 会执行，其它部分都被注释掉了。

但是 `-->` 只有在行首才会被当成注释，否则会当作正常的运算。

```javascript
function countdown(n) {
	while (n --> 0) console.log(n);
}
countdown(3)
```

上面代码中，`n --> 0` 实际上会当作 `n-- > 0`，因此输出 2、1、0。

# 语句

JavaScript程序的执行单位为行（line），也就是一行一行地执行。一般情况下，每一行就是一个语句。

语句（statement）是为了完成某种任务而进行的操作，比如下面就是一行赋值语句。

```javascript
// 修饰符修饰变量a 赋值 表达式，以分号结束，结合起来为一个语句
var a = 1 + 3;
// 多个语句可以写在一行内
var a = 1 + 3; var b = 'abc';
// 三个空语句
;;;
```

# 变量 & 常量

JavaScript声明变量需使用关键字 `var` 来声明：

```javascript
var i;
var sum;
// 或者
var i, sum;
// 声明变量时赋值
var message = "Hello";
var i = 0, j = 0, k = 0;
// 变量赋值不写 var 也是可以的
a = 1;	// 等同于 var a = 1; 但这样写法不利于表达，且容易不知不觉地创建全局变量
```

如果未在 `var` 声明语句时指定初始值，初始值会默认为 `undefined`。

`ES6`新增 `let` 来声明变量，是现代的变量声明方式，与 `var` 大体相同。但 `let` 声明的变量只能在 `let` 命令所在的代码块内有效，无变量提升（变量提升就是被提升至所在作用域的顶部）。

`ES6` 新增 `const` 声明常量，不可被修改，无变量提升。

```javascript
const pi = 3.14;
pi = 3.15;	// 这里会报错，常量不能被修改。
```

# 标识符

标识符就是用来标记各种值的合法名称。大小写敏感。

命名规则：

- 首字符为字母下划线(`_`)与美元符号(`$`)。
- 剩下的字符除了上述字符还可以有数字 `0-9`

但 JavaScript 中的保留字不可以用作标识符。

# 现代模式

`ES5`规范新增了新的语言特性，但默认不生效。要使用特殊指令`use strict` 进行激活。将 `use strict` 放在脚本文件的顶部，整个脚本文件都将以 "现代" 模式工作。

```javascript
"use strict";
// 代码以现代模式工作
...
```

现代JavaScript支持 "classes" 和 "modules" ，它们会自动启动 `use strict`。

# 区块

JavaScript使用大括号，将多个相关的语句组合在一起，称为 "区块"（block）。

对于 `var` 命令来说，JavaScript的区块不构成单独的作用域（scope）。

```javascript
{
	var a = 1;
}
a	// 1
```

上面代码在区块内部，使用 `var` 命令声明并赋值了变量 `a`然后在区块外部，变量 `a` 依然有效，区块对于 `var` 命令不构成单独的作用域，与不使用区块的情况没有任何区别。在 `JavaScript` 语言中，单独使用区块并不常见，区块往往用来构成其它更复杂的语法结构，比如 `for`、`if`、`while`、`function` 等。

# 流程控制

## 条件语句

JavaScript提供 `if` 结构和 `switch` 结构，完成条件判断。

### if语句

`if` 结构的条件语句有几种情况：

```javascript
// 第一种
if (布尔值) {
	语句;
}
// 第二种
if (布尔值) {
	语句;
} else {
	语句;
}
// 多次判断
if (布尔值) {
	语句;
} else if (布尔值) {
	语句;
} else if (布尔值) {
	语句;
} else {
	语句;
}
```

### switch 语句

`switch` 结构可以替代多个 `if`语句结构：

```javascript
switch (fruit) {
	case "banana":
		// ...
		break;
	case "apple":
		// ...
		break;
	...
	default:
		// ...
}
```

如果没有 `break` 语句，代码会一直执行下去。

`switch` 语句后面的表达式，与 `case` 语句后面的表达式比较运算结果时，采用的是严格相等运算符（`===`），而不是相等运算符（`==`），这意味着比较时不会发生类型转换。

JavaScript还有一个三元运算符`?:`可以用于逻辑判断：

```javascript
var even = (n % 2 === 0) ? true : false;
```

三元运算符可以被视作 `if...else...` 的简写。

## 循环语句

循环语句用于重复执行某个操作。JavaScript支持三种形式：`while` 循环、`do...while` 循环、`for` 循环。

### while循环

```javascript
var i = 0;
while (i < 100) {
	i = i + 1;
}
```

### do...while循环

```javascript
var x = 3, i = 0;
do {
	i++;
} while (i < x);
```

### for循环

`for` 语句里有三个表达式：初始化、条件表达式、递增语句。

```javascript
for (var i = 0; i < 3; i++) {
	console.log(i);
}
```

### break & continue

`break` 与 `continue` 可以用来结束循环语句，但作用不相同。

`break`语句用于跳出代码块或循环。

```javascript
var i = 0;
while (i < 100) {
	i++;
	if (i === 10) break;	// 当i=10，跳出循环
}
```

`continue` 语句用于立即终止本轮循环，返回循环结构的头部，开始下一轮循环。

```javascript
var i = 0;
while (i++) {
	i++;
	if (i % 2 === 0) continue;	// 当i为偶数时终止本轮循环
}
```

### label

JavaScript 可以在循环语句前添加 `label` ，用于跳转到程序的任意位置。相当于定位。

`label` 设置的标识符不能是保留字。通常与 `break` 和 `continue` 进行配合，跳出循环语句。

```javascript
top:
	for (var i = 0; i < 3; i++) {
		for (var j = 0; j < 3; j++) {
			if (i === 1 && j === 1) break top;
			console.log('i=' + i + ', j=' + j);
		}
	}
// i = 0, j = 0
// i = 0, j = 1
// i = 0, j = 2
// i = 1, j = 0
```

标签也可以用于跳出代码块。

```javascript
foo: {
	console.log(1);
	break foo;
	console.log('本行不会输出');
}
console.log(2);
// 1
// 2
```

# 运算符

运算符是处理数据的基本方法，用来从现有的值得到新的值。运算符提供算术运算符、关系运算符、布尔运算符、位运算符与其它运算符来进行运算。下面我们依次来介绍它们。

## 算术运算符

算术运算符提供 `加法(+)`、`减法(-)`、`乘法(*)`、`除法(/)`、`指数(**)`、`余数(%)`、`自增(++)`、`自减(--)`、`正数值(+X)`、`负数值(-X)` 运算符。

### 加法运算

使用 `+` 运算符来实现两数相加。JavaScript允许非数值相加；而如果与字符串相加，则将字符串相连并返回新的字符串。

```javascript
1 + 1	// 2
true + true		// 2
1 + true		// 2
'hello,' + ' world!'	// hello, world!
1 + ', 数字转成字符串'		// 1, 数字转成字符串
true + ', 布尔值转成字符串'	// true, 布尔值转成字符串
```

### 对象相加

对象相加，必须先转成原始类型的值。

```javascript
var obj = { p:1 };
obj + 2		// [object Object]2
```

上述代码中，对象 `obj` 调用 `valueOf` 方法返回对象自身：

```java
obj.valueOf()	// { p: 1 }
```

在调用 `toString` 方法，将其转为字符串。而上述对象默认返回 `[object Object]`。

因此，我们自己定义 `valueOf` 方法或 `toString` 方法，得到想要的结果。

```javascript
var obj = {
    valueOf: function () {
        return 1;
    }
};
obj + 2		// 3
```

### 余数运算

余数的运算结果的正负号由第一个除数的正负号决定。

```javascript
-1 % 2		// -1
1 % -2		// 1
```

### 自增与自减

自增和自减运算符，是一元运算符，只需要一个运算子。它们的作用是将运算子首先转为数值，然后加上1或者减去1。它们会修改原始变量。

### 数值运算符，负数值运算符

数值运算符（`+`）同样使用加号，但它是一元运算符，而加法运算符是二元运算符。

数值运算符的作用在于可以将任何值转为数值（与`Number` 函数的作用相同）。

```javascript
+true	// 1
+[]		// 0
+{}		// NaN
```

负数值运算符（`-`），也同样具有将一个值转为数值的功能，只不过得到的值正负相反。连用两个负数值运算符，等同于数值运算符。

```java
var x = 1;
-x	// -1
-(-x)	// 1
```

数值运算符号和赋值运算符，都会返回一个新的值，而不会改变原始变量的值。

### 指数运算符

指数运算符（`**`）完成指数运算，前一个运算子是底数，后一个运算子是指数。

```javascript
2 *** 4	//16
```

注意，指数运算符是右结合，而不是左结合。即多个指数运算符连用时，先进行最右边的计算。

```javascript
// 相当于 2 ** (3 ** 2)
2 *** 3 *** 2 	// 512
```

上面代码中，由于指数运算符是右结合，所以先计算第二个指数运算符，而不是第一个。

### 赋值运算符

赋值运算符（Assignment Operators）用于给变量赋值。可以与其它运算符结合。

```javascript
var x = 1;
var y = x;
x += y;		// 与加号运算符结合
x >>= y		// 与位运算符结合
```

## 比较运算符

比较运算符用于比较两个值之间的大小，然后返回一个布尔值，表示是否满足指定的条件。可用于各种类型的值，不限于数值。

提供 `大于(>)`、`小于(<)`、`小于等于(<=)`、`大于等于(>=)`、`相等(==)`、`严格相等(===)`、`不相等(!=)`、`严格不相等(!==)` 八比较运算符。

### 相等与不相等

`==` 和 `===` 同是用于比较两个值是否相等，但是语义的定义不尽相同。`==` 运算符称作相同运算符，用于检测两个操作数是否相同，允许类型转换。`===` 运算符称作严格相等运算符，用于检测两个值的类型和值是否都相等，比较过程不会进行类型转换。而 `!=` 与 `!==` 也与上面一样。

### 对象比较

如果比较的是对象，会转成原始类型的值，在进行比较。先调用 `valueOf` 方法，如果返回的还是对象，在调用 `toString` 方法。

```javascript
var x = [2];
x >= '11'	// true
// 等同于 [2].valueOf
```

## 逻辑运算符

逻辑运算符是将表达式转为布尔值，共四个运算符：`逻辑非(!)`、`与(&&)`、`或(||)`、`三元(?:)`。

### 逻辑非

取反操作就是将布尔值变成相反值。`!true == false`。对于非布尔值，取反会将其转为布尔值。

```javascript
!undefined	// true
!null		// true
!0			// true
!NaN		// true
!""			// true
```

上述值进行取反操作为 `true`，其它值就都会成为 `false`。

### 逻辑与

逻辑与运算符（`&&`）对多个值进行`AND`运算。该操作只有多个值皆为真，结果才为真。两个值执行`&&`操作，只有在第一个值和第二个值都为`true`，它才返回`true`。如果其中一个为值为`false`，它会返回`false`。而第一个值为`false`，它会提前结束`AND`操作，并返回结果`false`，这种行为称为"短路"。

```javascript
3-1 == 2 && 3+2 == 5	// true
3-1 == 2 && 3+2 != 5	// false
```

### 逻辑或

逻辑或运算符（`||`）对多个值进行`OR`运算，该操作是有一个值为真，结果就为真。而值都为假时，才为假。

```javascript
3-1 == 2 || 3+2 != 5	// true
3-1 != 2 || 3+2 != 5	// false
```

例如上面的逻辑与操作换成逻辑或操作后，结果为`true`。

### 三元运算符

三元运算符由 `问号(?)` 与 `冒号(:)`  组成。如果表达式为 `true`，则返回第二个表达式的值，否则返回第三个表达式的值。

```javascript
var x = 2;
var result = x > 1 ? 'true' : 'false';
```

三元表达式与 `if...else` 语句的效果差不多。


## 位运算符

对数字表示的二进制数据进行更低层级的按位运算，一共有7个：`或(|)`、`与(&)`、`否(~)`、`异或(^)`、`左移(<<)`、`右移(>>)` 和 `头部补零的右移(>>>)`。

位运算符直接对每一个比特位（bit）直接处理，所以是非常底层的运算，速度快但不直观。

位运算符只对32位整数起作用，必要时，位运算会将操作数转为32位整数再执行。

### 按位或

或运算符（`|`）对两个整数的二进制逐位进行`OR`操作。其中一个二进制操作数相应位为1，或者两个整数比较的二进制位都为1，就返回1，否则返回0。

```javascript
5 | 6	// 7
```

数字`5`转为二进制为 `101`，数字`6`转为二进制为 `110`，所以进行或运算得到 `111`即（`7`）

### 按位与

与运算符（`&`）对两个整数的二进制逐位进行`AND`操作。只有两个整数的二进制为皆为1，才能返回1，否则返回0。

```javascript
5 & 6	// 4
```

数字`5`转为二进制为 `101`，数字`6`转为二进制为 `110`，所以进行或运算得到 `100`即（`4`）

### 按位否

否运算符（`~`）是一元运算符，位于一个整型参数之前，它将操作数的二进制位取反，即`0`转为`1`，`1`转为`0`。

```javascript
~3	// -4
```

数字 `3` 转为二进制为32整数形式的 `0..011`，对它进行按位取反则为32整数形式的`1..100`，转为十进制为`-4`。

### 异或

异或运算（`^`）对两个操作数的二进制执行`XOR`操作。只有二进制位不同时返回`1`，相同是则返回`0`。

```javascript
3 ^ 4	// 7
```

数字 `3` 转为二进制为32形式的`0..011`，数字`4`转为二进制为32形式的`0..100`，所以进行异或运算得到`0..111`，转为十进制为`7`。

### 左移

左移运算符（`<<`）表示将一个数的二进制值向左移动指定的位数，尾部补`0`，即乘以`2`的指定次方。要移动二进制的数有第一个操作数决定，而移动的位数有第二个操作数决定。

```javascript
3 << 4	// 48
```

数字`3`向左移动`4`位，从二进制中看`0..011`，得到`0..11000`，转为十进制为`48`。

### 右移

右移运算符（`>>`）表示将一个数的二进制值向右移动指定的位数，即除以`2`的指定次方，为正数，最高位补`0`；为负数，最高位补`1`。要移动二进制的数有第一个操作数决定，而移动的位数有第二个操作数决定。

```javascript
-48>>4	// -3
```

数字`-48`向右移动`4`位，从二进制中看`10..011000`，得到`10..011`，转为十进制为`-3`。

### 头部补零的右移

头部补零的右移（`>>>`）与右移（`>>`）只有一个差别，就是`>>>`操作的头部一律补零，与原先符号无关。该运算得到的值皆为正数。

```javascript
-48>>>4	// 268435453
```

# 数据类型

数据类型分为两类：原始类型（primitive type）和对象类型（object type）。

原始类型分为 `数字(Number)`、`字符串(String)`、`布尔值(Boolean)`。还有两个特殊的原始值为`undefined`和`null`。

`ES6`新增了`Symbol` 类型。`BigInt` 是在`Symbol`之后新添加的数字类型。这两个也是原始类型。

对象类型是 `Object`类型，它可以存储数据集合和更复杂的实体。	

## Number

`Number` 类型代表整数和浮点数，因为JavaScript内部都是以64位浮点数形式存储数字类型。

```javascript
let pi = 3.14159;
0.1+0.2 === 0.3	// false
```

浮点数不是精确的值，因此小数的运算要小心。例如上面的例子 `0.1+0.2` 等于 `0.30000000000000004` 与 `0.3` 不相等。

`Number` 类型可以做四则运算等等。除了常规的数字，还包括"特殊数值"：`无穷大(Infinity)`、`负无穷大(-Infinity)` 和 `非数字(NaN)`。

```javascript
1 / 0	// Infinity
1 / -0	// -Infinity
1 - 'A'	// NaN	主要出现在将字符串解析成数字出错的场合
```

## BigInt

JavaScript中 `Number` 无法表示 $2^{53}-1$ 或小于 $-(2^{53}-1)$ 的整数。

有时需要加密或微妙读取的时间戳时，这个范围就不够了。

`BigInt` 类型是最近被添加到JavaScript中的，用于表示任意长度的整数。可以通过 `n` 附加到整数字段的末端来创建 `BigInt` 值。

```javascript
const bigInt = 98765432100123456789n;
```

## String

`String` 类型代表字符串，必须使用引号括起来，单引号或双引号皆可，可以使用 `length` 计算字符串长度。

```javascript
let name = "Jam";
name.length		// 3
```

JavaScript使用 `Unicode` 字符集存储字符，还可以直接使用 `Unicode` 表示字符。例如 `\172`，输出时是 `z`。

## Boolean

`boolean` 类型仅包含 `true` 和 `false`。用于表示正确与否。

```javascript
let isReal = 2 != 3;	// true
```

会被转为 `false` 的值有 `undefined`、`null`、`false`、`0`、`NaN` 和 空字符串。其它值会被转为 `true`。

## null & undefined

`null` 属于原始类型中的特殊值，代表"无"、"空"等未知值。不是其它编程语言中的 "对不存在的`object`的引用" 或者 "`null`指针"。

`undefined` 也属于原始类型中的特殊值，代表"未定义"。与 `null`的用法差不多，都代表值不存在，带语义不一样。对于没有初始化的变量、函数调用时候提供的函数参数、缺失的对象属性，它们的默认值就是 `undefined`。

```javascript
let name;	// undefined
name = "Any";
name = undefined;	// 不建议
name = null;	// 建议
```

变量被申明，但未被赋值，那它的值就是 `undefined`。而在有值后要修改为无值，不建议使用 `undefined`。可以使用 `null`。`null`将一个"空"或者"未知"的值写入变量。而`undefined`保留作为未进行初始化的变量的默认初始值。

## Symbol

`Symbol` 类型的实例是唯一且不可变的；一种唯一标识符，可用作对象的唯一属性名。

```javascript
let id1 = Symbol('id');	// id 是描述为 "id" 的 Symbol
let id2 = Symbol('id');
id1 == id2;	// false
```

可以调用 `Symbol.description` 属性来显示描述信息。

### "隐藏"属性

`Symbol` 允许创建对象的 "隐藏" 属性，这些属性都不能访问或重写。

```javascript
let student = {
	name: 'XiaoMing',
	age: 23
};
let sid = Symbol('sid');	// 学号
student[sid] = 2020;
student[sid];	// 2020 可以使用 Symbol 作为键访问数据
```

`Symbol` 在 `for...in` 循环中会被跳过，不会显示。

```javascript
for (var val in student) {
    console.log(val);
}
/*
name
age
*/
```

### 全局Symbol

通常所有的 `Symbol` 都不同，即使它们由相同的名字。但为了实现相同的 `Symbol` 具有相同的实体。有一个全局 `Symbol` 注册表。我们可以创建并访问，确保每次访问相同名字的 `Symbol`时，返回的都是相同的 `Symbol`。

```javascript
let id = Symbol.for('id');	// 从全局注册表中读取，如果不存在，则创建
let idAgain = Symbol.for('id');	// 再次读取
id == idAgain;	// true,相同的Symbol
```

还可以使用 `Symbol.keyFor(sym)` 通过 `Symbol` 返回该 `Symbol` 的名称。

```javascript
let name = Symbol.for('name');
Symbol.keyFor(name);	// name
```

## Object

`Object` 类型是重要的数据类型，它可以存储 "键值对"（key-value），是无序的符合数据集合。

```javascript
var o1 = {
	val1: 123,
	val2: function() {...}
}
// 动态创建
var o2 = {}
o2.val1 = 123;
o2.val2 = function() {...}
```

我们可以使用点运算符读取对象的属性`o.val1`或者使用方括号`o['val1']`。

## 类型转换

`typeof` 运算符可以返回参数的类型。对处理不同类型或类型检验时有用。支持两种语法： 运算符形式`typeof x` 或 函数形式`typeof(x)`。

```javascript
typeof undefined	// underfined
typeof 3			// number
typeof 3n			// bigint
typeof true			// boolean
typeof "string"		// string
typeof null			// object
typeof Math			// object
```

使用 `String(value)` 将值转换为字符串类型。使用 `Number(value)` 将值转换为 `Number` 类型。使用 `Boolean(value)` 将值转换为布尔类型。对 `undefined` 进行数字转换时，输出为 `NaN`，而非 `0`。而 `"0"` 和只有空格的字符串进行布尔转换时，输出为 `true`。

## Array

`Array` 数组类型是值的有序集合。数组中的元素都是按照插入顺序排序的。

### 数组声明

创建数组的方法总共有如下几种：

```javascript
let arr = new Array();
let arr = [];
// 也可以直接添加元素
let arr = ['Jan', 'Aug', 'Mar'];
```

任何类型的数据都可以放入数组。通过下标获取数组的元素 `arr[0]`。

数组是一种特殊的对象。使用 `typeof` 效验 `arr` 属于的类型。

```javascript
typeof arr	// object
```

### 遍历

数组可以使用 `for...in` 遍历。

```javascript
arr.test = 23;
for (var i in arr) {
	console.log(i);
}
/*
0
1
2
test
*/
```

但是`for...in`还会遍历非数字键。推荐使用 `while` 或者 `for` 循环遍历。

### 存取方法

`Array` 数组支持`push/pop` 和 `shift/unshift` 两种存取方式。

`push` 方法会在末端添加一个元素；`pop` 方法会在末端取出一个元素；`shift` 会在首端取出一个元素；`unshift` 方法会在首端添加一个元素。

队列是一种先进先出的线性结构，我们可以使用 `push/shift` 两种方法来进行实现。

栈是一种后进先出的数据结构，我们可以使用 `push/pop` 来实现。

这就是数组的两种应用。

## 函数

我们会在许多地方执行许多相似的操作。而这些操作可以使用函数来进行构建。而在JavaScript中函数实际上是对象。每个函数都是`Function`类型的实例。

### 函数声明

使用 `function` 关键字来声明代码块。

```javascript
function add(x, y) {	// add 为函数名
	return x + y;
}
```

函数还可以通过一个表达式声明。

```javascript
var add = function(x, y) {
	return x + y;
};
// 调用方式
add(10, 20);	// 30
```

函数表达式实际上就是一个匿名函数，存储在变量中，不需要函数名，通过变量名调用。

我们还可以使用 "箭头函数" 来替代函数表达式：

```javascript
var add = (x, y) => {
	return x + y;
};
// 或者
var add = (x,y) => x + y;
add(10, 20);	// 30
```

上面的函数是两种写法：`(...args) => expression` 和 `(...args) => { statements }`。

我们也可以使用 `Function` 构造函数来声明。

```javascript
var sub = new Function(
	'x',
	'y',
	'return x - y'
);
// 调用方式
sub(20, 10);	// 10
```

### 重复声明

同一函数被多次声明，前面的函数会被后面的函数覆盖。

```javascript
function add(x, y) {
    return x - y;
}
add(10, 20);	// 30
function add(x, y) {
    return x + y;
}
add(10, 20);	// 30
```

### 函数作为值

JavaScript中函数名本身就是变量，所以函数可以作为值来使用。凡是使用值的地方，都可以使用函数。

```javascript
function sub(func, x) {
    return func - x;
}
sub(add(10, 20), 25);	// 5
```

上面的函数将`add`函数作为参数传递给了它。

### 函数提升

函数会像声明变量一样，被提升到代码头部。

```javascript
add(10, 20);	// 30
function add(x, y) {
    return x + y;
}
```

因此，如上的`add`调用不会报错。

### 函数内部属性

函数内部有两个属性： `arguments` 和 `this`。

`arguments` 是一个类数组对象，包含传入的所有参数。

```javascript
function min(x, y) {
    console.log(arguments);
    return Math.min(x, y);
}
min(10, 20);
/*
[Arguments] { '0': 10, '1': 20 }
10
*/
```

`arguments` 带有一个 `callee` 属性，返回它所对应的原函数。

```javascript
function factorial(num) {
    if (num <= 1) {
        return 1;
    }
    return num * arguments.callee(num - 1);
}
factorial(3);	// 6
```

我们通过 `arguments.callee`，达到调用函数自身的目的。

`this` 就是函数运行时所在的环境对象。

在全局执行环境中使用`this`，浏览器中指`window`对象。

在没有对象的情况下调用 `this` 的值为 `undefined`。

例如，这里相同的函数被分配给两个不同的对象，在调用中有着不同的 "this" 值：

```javascript
let user = {name: 'John'};
let admin = {name: 'Admin'};

function sayHi() {
    return this.name;
}
// 两个对象中使用相同的函数
user.func = sayHi;
admin.func = sayHi;

// 调用不同的 this 值
user.func();	// John
admin.func();	// Admin
```

### 函数属性和方法

#### name属性

`name` 用于返回声明函数的名称。

```javascript
sub.name;	// sub
```

#### length属性

`length` 用于返回函数的形参个数。

```javascript
sub.length;	// 2
```

#### prototype属性

每个函数都有一个`prototype` 属性，这个属性指向一个对象，这个对象就是原型对象。原型对象的作用就是定义所有实例对象共享的属性和方法。实例对象可以看作从原型对象衍生出来的子对象，原型对象上的所有属性和方法都被实例对象共享。

#### toString()方法

`toString` 用于返回关于函数源码的字符串。

```javascript
sub.toString()	
/* function sub(func, x) {
    return func - x;
   }
*/
```

### 闭包

闭包（closure）是一个函数读取其它函数内部变量的函数。变量的作用域分为全局变量和局部变量，局部变量是只有被函数内部读取，外部读取不了，因此就会用到闭包。

```javascript
function clos1() {
    var x = 10;
    function clos2() {
        return x++;
    }
    return clos2;
}
var result = clos1();
result();	// 10
```

上述代码中，函数`clos1` 的返回值就是`clos2`函数，`clos2`读取了`clos1`中的内部变量`x`，在调用函数时，会取得`clos1`中的内部变量。函数`clos2`就是闭包。

闭包最大的两个用处就是读取函数内部的变量和让这些变量的值始终保持在内存中。

```javascript
result();	// 11
```

`result` 实际上就是闭包 `clos2` 函数，它一共运行两次，第一次返回 `10` 之后，做了`++`操作的`x`的值为`11`；下一次调用的值就会返回 `11`。

原因是 `clos2` 被赋给了一个全局变量，导致 `clos2` 一直在内存中，而 `clos2` 依赖于 `clos1`，因此 `clos1` 也一直在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

### 立即调用的函数表达式（IIFE）

根据JavaScript语法，在上面的例子中 `funcname()` 函数名+`()` 就可以调用函数。

```javascript
function iife() {
    console.log("立即调用");
}();	// 报错 SyntaxError: Unexpected token ')' 
```

而想要解决该问题就是让 `function` 不要出现的行首，让引擎将其理解成一个表达式。

```javascript
(function iife() {
    console.log("立即调用");
}());
// 或者
(function iife() {
    console.log("立即调用");
})();
// 理解调用
```

上述两种都以`(` 圆括号开头，引擎会认为后面跟的是表达式，而不是函数定义语句，所以会避免错误。这就叫 "立即调用的函数表达式"（Immediately-Invoked Function Expression）。