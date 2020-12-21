面向对象（Object Oriented，缩写OO）有类的概念，通过类创建任意多个具有相同属性和方法的对象。典型的面向对象语言（C++/Java），都有 "类"（class）这个概念。但JavaScript语言没有类的概念，而ECMAScript（简称ES）是基于原型实现的面向对象编程。

# 理解对象

对象是一组无序属性的集合，一个属性就是一个键值对{"key:value"}。现在，我们来简单的创建一个 `Object` 的实例。

```javascript
var s1 = new Object();
s1.name = "小赵";
s1.age = 20;
s1.sid = 2002;
s1.sayHi = function() {
    console.log("Hi, My name is " + this.name);
}
```

还可以使用对象字面量的语法创建对象。

```javascript
var s1 = {
    name: "小赵",
    age: 20,
    sid: 2002,
    sayHi: function () {
        console.log("Hi, My name is " + this.name);
    }
};
```

这样创建的实例对象与前面的一样。

## 属性类型

`ES`中有数据属性和访问器属性。

数据属性包含一个数据值的位置。在这个位置可以读取和写入值。

访问器属性不包含数据值，而是使用`getter`函数读取值，`setter`函数写入值。

这两种属性都有4个描述其行为的特性。

### 属性特性

以下特性是属于数据属性：

- `[[Writable]]`：表示属性值能否被修改。默认为`true`。
- `[[Value]]`：表示属性的数据值。从这个位置可以读取属性值；也可以保存新的属性值。默认为`undefined`。

以下特性是属于访问器属性：

- `[[Get]]`：调用函数获取属性值。默认为`undefined`。
- `[[Set]]`：调用函数写入属性值。默认为`undefined`。

而这两种属性类型都具有以下特性：

- `[[Configurable]]`：表示属性是否可配置。如通过`delete`删除并重新定义属性，能否修改属性特性，能否将属性修改为访问器属性（或者将属性修改为数据属性）。默认为`true`。
- `[[Enumerable]]`：表示能否通过`for-in`循环遍历。默认为`true`。设置为`false`时，会使`for-in`循环、`Object.keys()`操作等跳过该属性。

### Object.defineProperty

无论是数据属性修改属性默认的特性，还是访问器属性的定义，都要使用`ES5`的`Object.defineProperty()`方法。该方法接收三个参数：要定义属性的对象，对象中要定义或修改的属性名和属性描述符。下面对数据属性进行定义。

```javascript
var student ={};
Object.defineProperty(student, "name", {
    writable: false,
    value: "小赵"
});
student.name;	// 小赵
student.name = "小钱";
student.name;	// 小赵
```

创建一个只读的 `name` 属性，它的值不可修改，非严格模式下修改值的操作会被忽略；严格模式下会抛出错误 。

类似的规则也适用于不可配置的属性。例如：

```javascript
var student ={};
Object.defineProperty(student, "name", {
    configurable: false,
    value: "小赵"
});
student.name;	// 小赵
delete student.name;
student.name;	// 小赵
```

`configurable`的设置使得`name`属性不能删除。在非严格模式下调用`delete`会忽略该操作，而严格模式下会报错。

而一旦设置为不可配置，就不能改回可配置了。此时，调用 `Object.defineProperty()`方法修改除`writable`之外的特性，都会导致错误：

```javascript
var student ={};
Object.defineProperty(student, "name", {
    configurable: false,
    value: "小赵"
});
// TypeError: Cannot redefine property: name
Object.defineProperty(student, "name", {
    configurable: true,
    value: "小赵"
});
```

可以多次调用 `Object.defineProperty()` 方法修改同一个属性，但把 `configurable` 设置为 `false`后，就会有限制。

在调用 `Object.defineProperty()` 方法时，如果不指定，`configurable`、`enumerable` 和 `writable` 特性的默认值都是 `false`，多数情况下，可能都没有必要利用 `Object.defineProperty()` 方法提供的这些高级功能。

而访问器属性不能直接定义`getter`与`setter`，必须使用`Object.defineProperty()`来定义。

```javascript
var book = {
    _year: 2004,
    edition: 1
};
Object.defineProperty(book, "year", {
    get: function () {
        return this._year;
    },
    set: function (newValue) {
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
book.year = 2006;
console.log(book.edition);	// 3
```

不一定同时指定`getter`和`setter`。只指定`getter`意味着可读不可写，写入属性时会忽略，严格模式下会抛出错误。类似的，只指定`setter`意味着可写不可读，读取时返回`undefined`，严格模式下会抛出错误。

## 定义多个属性

当为对象定义多个属性时，可以使用`ES5`定义的`Object.defineProperties()`方法。这个方法接收两个对象参数：第一个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。例如：

```javascript
let book = {
};
Object.defineProperties(book, {
    _year: {
        value: 2004,
        writable: true
    },
    edition: {
        value: 1,
        writable: true
    },
    year: {
        get: function () {
            return this._year;
        },
        set: function (newValue) {
            if (newValue > 2004) {
                this._year = newValue;
                this.edition += newValue - 2004;
            }
        }
    }
});
```

定义了两个数据属性和一个访问器属性。最终对象与上面定义的相同。唯一区别是在这里的属性是同一时间创建的。

## 读取属性的特性

`ES5`中的`Object.getOwnPropertyDescriptor()`方法可以取得给定属性的描述符。这个方法接收两个参数：属性所在的对象和读取其描述符的属性名称。返回值是一个对象，如果是访问器属性，这个对象的属性有`configurable`、`enumerable`、`get`和`set`；如果是数据属性，这个对象的属性有`configurable`、`enumerable`、`writable`和`value`。例如：

```javascript
var book = {};
Object.defineProperties(book, {
    _year: {
        value: 2004,
        writable: true
    },
    edition: {
        value: 1,
        writable: true
    },
    year: {
        get: function () {
            return this._year;
        },
        set: function (newValue) {
            if (newValue > 2004) {
                this._year = newValue;
                this.edition += newValue - 2004;
            }
        }
    }
});
var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
console.log(descriptor.value);			// 2004
console.log(descriptor.configurable);	// false
console.log(typeof descriptor.get);		// undefined

var descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value);			// undefined
console.log(descriptor.enumerable);		// false
console.log(descriptor.get);			// [Function: get]
```

在JavaScript中，可以针对任何对象——包括`DOM`和`BOM`对象，使用`Object.getOwnPropertyDescriptor()`方法。

# 创建对象

为了解决`Object`构造函数或对象字面量创建对象产生的大量重复代码，开始使用工厂模式实现对象创建。

## 工厂模式

工厂模式抽象创建具体对象的过程，因为`ES6`之前无法创建类，因此使用函数封装特定接口创建对象的细节。

```javascript
function createStudent(name, age, sid) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.sid = sid;
    o.sayHi = function () {
        console.log("Hi, My name is " + o.name);
    };
    return o;
}
var s1 = createStudent("小周", 30, 2005);
var s2 = createStudent("小吴", 18, 2006);
```

函数`createStudent()`根据接收的参数构建`Student`对象。该函数可以无数次调用，但没有解决对象识别问题。

## 构造函数模式

`ES`中构造函数用来创建特定类型的对象，自定义对象类型的属性和方法。

```javascript
function Student(name, age, sid) {
    this.name = name;
    this.age = age;
    this.sid = sid;
    this.sayHi = function () {
        console.log("Hi, My name is " + this.name);
    };
}
var s1 = new Student("小郑", 45, 2008);
var s2 = new Student("小王", 32, 2009);
```

在构造函数模式中，没有显示地创建对象，直接将属性和方法赋给`this`对象，没有使用`return`语句返回对象，可以使用`new`调用这个构造函数。

此外，构造函数本身是函数，因此开头大写与其它函数区分。

实例对象中`constructor`属性指向当前对象类型。

```javascript
s1.constructor == Student;		// true
s2.constructor == Student;		// true
```

也可以使用`instanceof`检查对象类型。

```javascript
s1 instanceof Object	// true
s1 instanceof Student	// true
```

构造函数也是函数，可以不通过`new`操作符调用，这样和其它函数就没什么区别。

```javascript
// 当作构造函数使用
var s1 = new Student("小冯", 37, 2010);
s1.sayHi();
// 作为普通函数调用
Student("小新", 52, 2012);
window.sayHi();
// 在另一个对象的作用域中调用
var o = new Object();
Student.call(o, "小明", 66, 2020);
o.sayHi();
```

构造方法中的每个方法都要在每个实例上创建一遍。就像上面例子中`sayHi`方法的`function() {}`创建与`new Function()`是等价的。因此，不同实力上的同名函数是不相等的。

```javascript
console.log(s1.sayHi == s2.sayHi);	// false
```

因此，我们可以将函数定义转移到构造函数的外面来解决。

```javascript
function Student(name, age, sid) {
    this.name = name;
    this.age = age;
    this.sid = sid;
    this.sayHi = sayHi;
}
function sayHi() {
	console.log("HI, My name is " + this.name);
}
var s1 = new Student("小龙", 29, 2011);
var s2 = new Student("小凤", 41, 2012);
```

但这样定义的函数要有很多时，我们就需要定义多个全局函数，没有封装性可言。下面介绍一下原型模式，通过该模式来解决这种缺陷。

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

可以通过`isPrototypeOf()`方法确定对象之间是否存在这种关系。从本质上讲，如果`[[Prototype]]`指向调用`isPrototypeOf`方法的对象`Student.prototype`，那么这个方法就返回`true`。

```javascript
Student.prototype.isPrototypeOf(s1);	// true
```

`ES5`中增加了`Object.getPrototypeOf()`方法，该方法返回对象的原型`[[Prototype]]`。这种方式可以获取原型对象中的属性。

```javascript
Object.getPrototypeOf(s1) == Student.prototype;	// true
Object.getPrototypeOf(s1).name;					// 小赵
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

可以使用`delete`操作符可以删除实例对象中的属性，从而重新访问原型中的属性。

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

`Object.keys()` 和 `Object.getOwnPropertyNames()` 都可以替代 `for-in` 循环。

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

在原型中查找值的过程是一次搜索，即便我们先创建实例，再修改原型，都会反映到实例上。

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
    sayHi: function () {
        console.log("Hi, My name is " + this.name);
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

## 组合使用构造函数模式和原型模式

组合使用构造函数模式与原型模式是创建自定义类型最常见的方式。构造函数模式用于定义实例属性，原型模式定义方法和共享的属性。这种模式还支持构造函数的参数传递。

```javascript
function Student(name, age, sid) {
    this.name = name;
    this.age = age;
    this.sid = sid;
    this.friends = ["小小", "小冷"];
}
Student.prototype = {
    constructor: Student,
    sayHi: function(){
          console.log("Hi, My name is " + this.name);
     }
}

var s1 = new Student("小新", 29, 2020);
var s2 = new Student("小霞", 19, 2012);
s1.friends.push("小军");
console.log(s1.friends);	// ["小小", "小冷", "小军"]
console.log(s2.friends);	// ["小小", "小冷"]
console.log(s1.friends === s2.friends);	// false
console.log(s1.sayHi === s2.sayHi);		// true
```

这种混合的模式是目前`ES`中使用最广泛、认同度最高的一种定义引用类型的模式。

## 动态原型模式

动态原型模式把所有信息都封装在构造函数中，在构造函数初始化时，通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

```javascript
function Student(name, age, sid) {
    // 属性
    this.name = name;
    this.age =age;
    this.sid = sid;
    // 方法
    if (typeof this.sayHi != "function") {
          Student.prototype.sayHi = function() {
              console.log("Hi" + this.name);
         }
     }
}
```

这种方式只有在`sayHi`方法不存在的情况下，才会将它添加到原型中。只会在初次调用构造函数时才会执行。完成初始化后就不再修改。这里对原型做出的修改，会立即在所有实例中反映；其次，`if`语句检查的可以是初始化之后应该存在的任何属性或方法，不必挨个检查，只要检查其中一个即可。这种模式创建的对象可以使用`instanceof`确定它的类型。

## 寄生构造函数模式

寄生（parasitic）构造函数模式的基本思想是创建一个函数，该函数仅仅用于封装创建对象的代码，然后返回新创建的对象。

```javascript
function Student(name, age, sid) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.sid = sid;
    o.sayHi = function() {
          console.log("Hi" + this.name);
     }
    return o;
}

var friend = new Student("小青", 22, 2018);
friend.sayHi();
```

该模式与工厂模式几乎一样，只是使用`new`进行创建并将函数称为构造函数。构造函数不返回对象，默认也会返回一个新的对象，可以通过`return`语句重写构造函数返回的值。

这种模式可以为不能直接修改的构造函数添加额外的属性和方法。

```javascript
function SpecialArray() {
    // 创建数组
    var values = new Array();
    // 添加值
    values.push.apply(values, arguments);
    // 添加方法
    values.toPipedString = function() {
          return this.join("|");
     }
    // 返回数组
    return values;
}
var colors = new SpecialArray("red", "blue", "green");
console.log(colors.toPipedString());	// red|blue|green
```

我们为`Array`数组的实例添加了`toPipedString()`方法。

## 稳妥构造函数模式

稳妥对象（durable objects）指的是没有公共属性，而且其方法也不引用`this`的对象。稳妥对象适合在一些安全的环境中（禁止使用`this`和`new`），或者在放置数据被其它应用程序改动时使用。稳妥构造函数与寄生构造函数类似，但有两点不同：一是新创建对象的实例方法不引用`this`；而是不使用`new`操作符调用构造函数。

```javascript
function Student(name, age, sid) {
    // 创建要返回的对象
    var o = new Object();
    // 定义私有变量和函数
    var name = name, age = age, sid = sid;
    // 添加方法
    o.getName = function() {
          return name;
     };
    return o;
}
var s1 = Student("小路", 52, 2017);
console.log(s1.getName());		// 小路
friends.name;	// undefined
```

如上面的代码所示，想要获取对象的`name`信息，只有调用`getName`方法，没有别的方式访问其数据成员。

与寄生构造函数模式类似，使用稳妥构造函数模式创建的对象与构造函数之间也没有什么关系，因此`instanceof`操作符对这种对象也没有意义。

## 类构造函数模式

`ES6`引入了`Class`的概念。通过`class`关键字可以定义类。它可以看作一个语法糖，让对象原型的写法更加清晰、更接近面向对象的语法。

```javascript
class Student {
    constructor(name, age, sid) {
        this.name = name;
        this.age = age;
        this.sid = sid;
        this.friends = ["小蓝", "小白", "小黑"];
    }
    sayHi() {
        console.log("Hi, My name is " + this.name);
    }
}

var s1 = new Student("小雷", 42, 2021);
s1.friends.push("小绿");
var s2 = new Student("小云", 28, 2022);
console.log(s1.friends);	// [ '小蓝', '小白', '小黑', '小绿' ]
console.log(s2.friends);	// [ '小蓝', '小白', '小黑' ]
```

`class` 中的`constructor`对应`ES5`中的构造函数；创建类的方法不需要使用`function`关键字，而没有显示定义`constructor`，默认会添加一个空的`constructor`方法。

```javascript
class Student {
}
// 等同
class Student {
    constructor() {}
}
```

`ES6`的类可以看做是构造函数的另一种写法，类是`function`类型，且类的`prototype`对象的`constructor`属性直接指向类本身。

```javascript
typeof  Student	// function
Student === Student.prototype.constructor	// true
```

虽然类是函数，但和`ES5`不同，不通过`new`而直接调用类会导致类型错误。

```javascript
var s1 = Student("小冰", 22, 2022);	// TypeError: Class constructor Student cannot be invoked without 'new'
```

另外，类内部所有定义的方法，都是不可枚举的。

# 继承

继承在OO语言中很常见，许多OO语言都支持接口继承和实现继承。ECMAScript函数没有签名，因此无法实现接口继承，它只支持实现继承，但ECMAScript不通过`class`继承，而是使用原型链来实现。

`ES6`引入了`class`语法，可以实现基于`class`的继承。

## 原型链

原型链的基本思想是引用类型的所有属性和方法通过原型被另一个引用类型继承，这样层层递进，构成链条。

实现原型链的代码如下：

```javascript
function SuperType() {
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {
    return this.property;
}
function SubType() {
    this.subproperty = false;
}
// 将SuperType的实例赋值给SubType.prototype
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function() {
    return this.subprototype;
}
var instance = new SubType();
console.log(instance.getSuperValue());     // true
```

上面的代码主要是通过`SuperType`的实例赋值给`SubType.prototype`来实现继承。而所有的类型都是继承自`Object`，而该继承也是通过原型链实现的。

我们可以通过 `instanceof` 操作符合`isPrototypeOf()`方法来确定原型和实例之间的关系。

```javascript
console.log(Object.prototype.isPrototypeOf(instance));          //true
console.log(SuperType.prototype.isPrototypeOf(instance));          //true
console.log(SubType.prototype.isPrototypeOf(instance));          //true
```

原型链还存在问题，例如引用类型的原型会被所有实例共享，而实例对引用类型中的属性的操作会反映到另一个实例上。

```javascript
function SuperType(){
  this.colors = ["red", "blue", "green"];
}
function SubType(){}

SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); //"red,blue,green,black"

var instance2 = new SubType(); 
console.log(instance2.colors); //"red,blue,green,black"
```

## 借用构造函数

借用构造函数（constructor stealing）是在子类型构造函数的内部调用超类型构造函数。

```javascript
function SuperType() {
    this.colors = ["red", "blue", "green"];
}
function SubType() {
    // 继承了SuperType
    SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors);  // [ 'red', 'blue', 'green', 'black' ]

var instance2 = new SubType();
console.log(instance2.colors);  // [ 'red', 'blue', 'green' ]
```

通过使用 `call()` 方法（或者`apply()`）"借用"了超类型的构造函数。在创建新的`SubType`实例环境下调用`SuperType`的构造函数，因此，`SubType`的每个实例都会具有`SuperType`属性的副本。

但使用借用构造函数，会无法实现函数复用；并且不能继承在超类型的原型中定义的属性和方法。

## 组合继承

组合继承（combination inheritance）是使用原型链实现对原型模式和方法的继承，通过借用构造函数来实现对实例属性的继承。通过在原型上定义方法实现函数复用，还能保证每个实例都有它自己的属性。

```javascript
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayHi = function () {
    console.log("Hi, My name is " + this.name);
};
function SubType(name, age) {
    // 继承属性
    SuperType.call(this, name);
    this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
// 重写SubType.prototype的constructor属性，指向自己的构造函数SubType
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    console.log("My age is " + this.age);
};
var instance1 = new SubType("小华", 29);
instance1.colors.push("black");
console.log(instance1.colors); // [ 'red', 'blue', 'green', 'black' ]
instance1.sayHi(); // Hi, My name is 小华
instance1.sayAge(); // My age is 29
var instance2 = new SubType("小夏", 27);
console.log(instance2.colors); // [ 'red', 'blue', 'green' ]
instance2.sayHi(); // Hi, My name is 小夏
instance2.sayAge(); // My age is 27
```

在这里，通过将`SuperType`的实例赋值给`SubType`的原型，让`SubType`的实例分别有用各自的属性。

组合继承融合了原型链与借用构造函数的优点，避免了它们的缺点，是最常用的继承模式。

## 原型式继承

原型式继承就是创建通过原型指向已有对象的新对象。

```javascript
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}
```

本质上，`object()`对传入其中的对象执行一次浅复制。

```javascript
var student = {
    name: "小李",
    friends: ["小明", "小清", "小民"]
};
var anotherStudent = object(student);
anotherStudent.name = "小汉";
anotherStudent.friends.push("小唐");

var yetAnotherStudent = object(student);
yetAnotherStudent.name = "小晋";
yetAnotherStudent.friends.push("小隋");

console.log(student.friends);	// [ '小明', '小清', '小民', '小唐', '小隋' ]
```

这种方式的继承会使以对象`student`作为原型的原型链共享着引用类型值属性；我们在其它的实例上修改了`friends`数组的值，在原型上也会反映。

`ES5`中新增`Object.create()`方法规范了原型式继承。`Object.create()`一共接收两个参数：一个用作新对象原型的对象；另一个为新对象定义额外属性的对象（可选）。

```javascript
var anotherStudent = Object.create(student);
anotherStudent.name = "小汉";
anotherStudent.friends.push("小唐");

var yetAnotherStudent = Object.create(student);
yetAnotherStudent.name = "小晋";
yetAnotherStudent.friends.push("小隋");

console.log(student.friends);	// [ '小明', '小清', '小民', '小唐', '小隋' ]
```

第二个参数的格式与`Object.defineProperties()`方法的第二个参数格式相同。

```javascript
var anotherStudent = Object.create(student, {
    name: {
        value: "小宋"
    }
});
console.log(anotherStudent.name);	// 小宋
```

## 寄生式继承

寄生式（parasitic）继承与原型式继承同一种思路，但可以在函数内部增强对象。

```javascript
function createAnother(original) {
    var clone = object(original);   // 通过调用函数创建一个新对象
    clone.sayHi = function () {     // 以某种方式来增强这个对象
        console.log("Hi, My name is " + clone.name);
    }
    return clone;                   // 返回这个对象
}
```

在上面的函数中我们为对象新增了方法，来增强函数。

```javascript
var student = {
    name: "小李",
    friends: ["小明", "小清", "小民"]
};
var anotherStudent = createAnother(student);
anotherStudent.sayHi();	// Hi, My name is 小李
```

寄生式继承的方式并没有解决原型式继承的缺点，也不能做到函数复用，从而降低效率。

## 寄生组合式继承

寄生组合式继承，通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。

```javascript
// 该函数实现了寄生组合式继承的最简单形式。
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype);    // 创建对象
    prototype.constructor = subType;                // 增强对象
    subType.prototype = prototype;                  // 指定对象
}
// 父类初始化实例属性和原型属性
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayHi = function () {
    console.log("Hi, My name is " + this.name);
}
// 借用构造函数传递增强子类实例的属性（支持传参和避免篡改）
function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
}
// 将父类原型指向子类
inheritPrototype(SubType, SuperType);
// 新增子类原型属性
SubType.prototype.seyAge = function () {
    console.log("My age is " + this.age);
}
```

这个例子的高效率体现在它只调用了一次`SuperType`构造函数，并且因此避免了在`SubType.prototype`上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用`instanceof` 和`isPrototypeOf()`。

## 类继承

`ES6`通过`extends`关键字实现继承。子类必须在`constructor`方法中调用`super`方法，对父类的`this`对象进行赋值。否则子类得不到`this`对象，会报错。

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
        this.friends = ["小蓝", "小白", "小黑"];
    }
    sayHi() {
        console.log("Hi, My name is " + this.name);
    }
}
class Student extends Person{
    constructor(name, age, sid) {
        super(name, age);
        this.sid = sid;
    }
}
```

上面代码中，`constructor`方法出现了`super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象。


# 总结

ECMAScript支持面向对象，但不使用类或者接口。对象在代码执行过程中创建和增强。在没有类的情况下，采用的是工厂模式、构造函数模式、原型模式等创建对象。而从`ES6`开始可以使用`class`创建对象。

JavaScript主要通过原型链实现继承。而原型链的构建是通过将一个类型的实例赋值给另一个构造函数的原型来实现。提供的是原型式继承、寄生式继承和两者组合继承。而从`ES6`开始使用`class`与`extends`关键字来实现类的继承方式。