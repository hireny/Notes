## 原型模式

创建的每个函数都有一个`prototype`属性，该属性指向一个对象，而对象是由特定类型的所有实例共享的属性和方法。这样可以将信息直接添加到原型对象中。

```javascript
function Student() {}
Student.prototype.name = "小圆";
Student.prototype.age = 24;
Student.prototype.sid = 2015;
Student.prototype.sayHi = function() {
	console.log("Hi, My name is " + this.name);
}
var s1 = new Student();
var s2 = new Student();
```

将所有属性和方法添加到原型对象上，对象上的属性和方法由所有实例共享。

### 理解原型对象

只要创建一个新函数，函数就会创建一个`prototype`属性并指向函数的原型对象。默认情况下的所有原型对象都会自动获得`constructor`属性，该属性包含指向`prototype`属性所在函数的指针。而其它方法从`Object`继承而来。当调用构造函数创建一个新实例后，该实例的内部将包含一个指针`[[Prototype]]`来指向原型对象。

可以通过`isPrototypeOf()`犯法确定对象之间是否存在这种关系。从本质上讲，如果`[[Prototype]]`指向调用`isPrototypeOf`方法的对象`Student.prototype`，那么这个方法就返回`true`。

```javascript
Student.prototype.isPrototypeOf(s1);	// true
```

`ES5`中增加了`Object.getPrototypeOf`方法，该方法返回对象的原型`[[Prototype]]`。这种方式可以获取原型对象中的属性。

```javascript
Object.getPrototypeOf(s1) == Student.prototype;	// true
Object.getPrototypeOf(s1).name;					// 小赵	本人代码输出的是undefined 存疑
```

代码在读取某个对象的属性时，都会从对象实例开始搜索，如果没有找到，则再次搜索指针指向的原型对象，这会执行两次搜索，是多个实例共享原型属性和方法的原理。

原型最初只包含`constructor`属性并且是共享的，可以通过对象实例访问，但不能通过对象实例重写原型中的值。在实例中添加属性时，会屏蔽原型对象中的同名属性，不修改原型对象中的属性值。

```javascript
function Student() {}
Student.prototype.name = "小圆";
Student.prototype.age = 24;
Student.prototype.sid = 2015;
Student.prototype.info = function() {
	console.log(this.name);
}
var s1 = new Student();
var s2 = new Student();
s1.name = "小诸";
console.log(s1.name);	// 小诸
console.log(s2.name);	// 小圆
```

可以使用`delete`操作符可以删除实例对象中的属性，从而重新冯文原型中的属性。

```javascript
delete s1.name;
console.log(s1.name);	// 小圆
```

使用`hasOwnProperty()`方法检测一个属性是存在于实例中还是存在于原型中。该方法从`Object`继承，只在给定属性存在于对象实例时，返回`true`。

```javascript
function Student() {}
Student.prototype.name = "小圆";
Student.prototype.age = 24;
Student.prototype.sid = 2015;
Student.prototype.info = function() {
	console.log(this.name);
}
var s1 = new Student();
var s2 = new Student();
s1.name = "小诸";
s1.hasOwnProperty("name");	// true		s1的name属性来自于实例
s2.hasOwnProperty("name");	// false	s2的name属性来自于原型
```

`in`操作符可以判定属性是否在给定实例中，不管该属性是存在于实例中，还是原型中。

```javascript
s1.hasOwnProperty("name");	// true		s1的name属性来自于实例
console.log("name" in s1);	// true
s2.hasOwnProperty("name");	// false	s2的name属性来自于原型
console.log("name" in s2);	// true
```

这样的话，可以结合`hasOwnProperty()`与`in`来判定属性是否存在于实例还是原型中。

```javascript
function hasPrototypeProperty(object, name) {
	return !object.hasOwnProperty(name) && (name in object);
}
```

使用`for-in`循环时，返回的是所有可通过对象访问、可枚举的属性。而屏蔽了原型中不可枚举属性的实例属性也会在`for-in`循环中（根据规定，开发人员定义的属性都是可枚举的）。

`ES5`还提供`Object.keys()`来获取对象中所有可枚举的实例对象。

```javascript
var keys = Object.keys(Student.prototype);
console.log(keys);	// [ 'name', 'age', 'sid', 'info' ]
var s1 = new Student4();
s1.name = "小玲";
s1.age = 18;
var s1keys = Object.keys(s1);
console.log(s1keys);	// [ 'name', 'age' ]
```

`Object.keys`传递原型，则获取是原型中所有属性，传递实例，则获取的是实例中所有属性。

还可以使用 `Object.getOwnPropertyNames()`方法获取带有`constructor`属性的实例的所有属性。

```javascript
var keys = Object.getOwnPropertyNames(Student.prototype);
console.log(keys);	// [ 'constructor', 'name', 'age', 'sid', 'sayHi' ]
```

`Object.keys()` 和 `Object.getOwnPropertyNames()` 都可替代 `for-in` 循环。

### 更简单的原型语法

以对象字面量形式创建对象重写原型对象的创建，减少不必要的语法。

```javascript
function Student() {
}
Student.prototype = {
    name: "小刘",
    age: 36,
    sid: 2016,
    sayHi: function () {
        console.log("Hi, My name is " + this.name);
    }
};
```

这种以对象字面量形式创建新对象并指向`Student.prototype`会导致`constructor`属性不再指向`Student`。因为这种语法在本质上完全重新了默认的`prototype`对象，因此`constructor`属性会指向默认的`Object`构造函数。

```javascript
var s1 = new Student();
console.log(s1 instanceof Object);        	// true
console.log(s1 instanceof Student);			// true
console.log(s1.constructor == Student);  	// false
console.log(s1.constructor == Object);    	// true
```

如果需要`constructor`属性，可以手动设置该属性的值。

```javascript
function Student() {
}
Student.prototype = {
	constructor: Student,
    name: "小刘",
    age: 36,
    sid: 2016,
    sayHi: function () {
        console.log("Hi, My name is " + this.name);
    }
};
```

但这种重设`constructor`属性的方式会导致它变为可枚举的。默认是不可枚举的。可以通过`ES5`的`Object.defineProperty()`设置。

```javascript
// 重设构造函数，只适用于ECMAScript5兼容的浏览器
Object.defineProperty(Student.prototype, "constructor", {
    enumerable: false,
    value: Student
});
```

### 原型的动态性

在原型中查找值的过程是一次搜索，即便我们先创建实例，在修改原型，都会反映到实例上。

```javascript
var s1 = new Student();
Student.prototype.sayHi = function() {
	console.log("Hi");
}
s1.sayHi();	// Hi
```

调用构造函数时会为实例添加一个指向最初原型的`[[Prototype]]`指针，重写整个原型对象的话，会切断它们之间的联系（实例中的指针仅指向原型，不指向构造函数）。

```javascript
function Student() {
}
var s1 = new Student();
Student.prototype = {
	constructor: Student,
    name: "小刘",
    age: 36,
    sid: 2016,
    sayHi: function () {
        console.log("HI, My name is " + this.name);
    }
};
s1.sayHi();	// TypeError: s1.info is not a function
```

重写原型对象会切断现有原型与任何之前存在的对象实例之间的联系；它们引用的仍然是最初的原型。

### 原生对象的原型

原生的引用类型都是采用原型模式创建的。通过原生对象的原型，不仅可以取得所有默认方法的引用，而且还可以定义新方法。下面给`String`类型添加`startsWith()` 方法。

```javascript
String.prototype.startsWith = function (text) {
    return this.indexOf(text) == 0;
};
var msg = "Hello world!";
console.log(msg.startsWith("Hello"));	// true
console.log(msg.startsWith("world"));	// false
```

但不推荐通过修改原生对象的原型来添加方法，这可能会导致命名冲突，而且会意外地重写原生方法。

### 原型对象的问题

原型对象的属性被实例共享，基本类型的值可以在实例上添加同名属性，隐藏原型中对应属性来解决，但对引用类型值，就会出现问题。

```javascript
function Student() {
}
Student.prototype = {
    constructor: Student,
    name: "小刘",
    age: 36,
    sid: 2016,
    friends: ["小燕", "小柒"],
    info: function () {
        for (var val in this) {
            if ('info' == val) {
                continue;
            }
            console.log(this[val]);
        }
    }
};
var s1 = new Student();
var s2 = new Student();
s1.friends.push("小历");
console.log(s1.friends);	// [ '小燕', '小柒', '小历' ]
console.log(s2.friends);	// [ '小燕', '小柒', '小历' ]
console.log(s1.friends == s2.friends);	// true
```

`s1`与`s2`同时指向原型，在`s1`中添加的元素也会反映到`s2`中。