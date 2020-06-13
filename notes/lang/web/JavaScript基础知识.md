# 基本语法

## 注释

被JavaScript引擎忽略的部分叫做注释，它的作用是对代码进行结束。JavaScript提供的注释的写法如下：

```javascript
// 单行注释
/*
 多行
 注释
*/
```

## 变量

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

## 区块

JavaScript使用大括号，将多个相关的语句组合在一起，称为 "区块"（block）。

对于 `var` 命令来说，JavaScript的区块不构成单独的作用域（scope）。

```javascript
{
	var i = 1;
}
a	// 1
```

上面代码在区块内部，使用 `var` 命令声明并赋值了变量 `a`然后在区块外部，变量 `a` 依然有效，区块对于 `var` 命令不构成单独的作用域，与不使用区块的情况没有任何区别。在 `JavaScript` 语言中，单独使用区块并不常见，区块往往用来构成其它更复杂的语法结构，比如 `for`、`if`、`while`、`function` 等。

## 条件语句

JavaScript提供 `if` 结构和 `switch` 结构，完成条件判断。

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

`switch` 结构可以比较方便的替代多个 `if...else` 语句

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

循环语句用于重复执行某个操作，它有三种形式：

- `while` 循环
- `do...while` 循环
- `for` 循环

**while循环**

```javascript
var i = 0;
while (i < 100) {
	i = i + 1;
}
```

**do...while循环**

```javascript
var x = 3, i = 0;
do {
	i++;
} while (i < x);
```

**for循环**  

`for`语句括号里面有三个表达式：

- 初始化表达式（initialize）：确定循环变量的初始值，只在循环开始时执行一次。
- 条件表达式（test）：每轮循环开始时，都要执行这个条件表达式，只有值为真，才继续进行循环。
- 递增表达式（increment）：每轮循环的最后一个操作，通常用来递增循环变量。

```javascript
for (var i = 0; i < 3; i++) {
	console.log(i);
}
```

**break与continue**

`break`语句和`continue`语句都具有跳转作用，可以让代码不按既有顺序执行。

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

**label**

JavaScript语言允许，语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置。

标签可以是任意的标识符，但不能是保留字；通常与 `break` 语句和 `continue` 语句配合使用，跳出特定的循环。

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

标签页可以用于跳出代码块。

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


# 数据类型

## 数值

## 字符串

## 对象

## 函数

## 数组
