在 ECMASCript 6 之前，使用构造函数模式与原型模式以及它们的组合来模拟类的行为 。但是这几种策略都有自己的问题，也有相应的妥协。而实现继承也会显得非常冗长和混乱。因此，ECMASCript5 新引入了 `class` 关键字来定义类，但实际上背后使用的仍然是原型和构造函数的概念。

## 类定义

类是 “特殊的函数”，因此定义类也有两种方式。第一种定义类的方式是声明类。

```javascript
class Person {}
```

另一种定义类的方式是类表达式。

```javascript
const Person = class {};
```

类表达式的名称是可选的。在把类表达式赋值给变量后，可以通过 `name` 属性取得类表达式的名称字符串。但不能在类表达式作用域外部访问这个标识符。

```javascript
let Person = class PersonName {
    identify() {
        console.log(Person.name, PersonName.name);
    }
}
let p = new Person();
p.identify(); // PersonName PersonName
console.log(Person.name); // PersonName
console.log(PersonName); // ReferenceError: PersonName is not defined
```

二者之间有重要区别。函数声明可以提升，类声明不会提升。

```javascript
let p = new Person();    // ReferenceError: Cannot access 'Person' before initialization
class Person {}
let s = new Student();    // ReferenceError: Cannot access 'Student' before initialization
let Student = class {};
```

类可以包含构造函数方法、实例方法、获取函数、设置函数和静态类方法，但这些都不是必需的。空的类定义照样有效。默认情况下，类定义中的代码都在严格模式下执行。

```javascript
class Person {
    // 构造函数
    constructor() {}
    // 获取函数
    get name() {}
    // 静态方法
    static of() {}
}
```

### 构造方法

`constructor` 方法是类定义中的构造方法。当类创建实例时，会调用这个方法。构造方法的定义不是必需的，不定义构造方法相当于将构造函方法定义为空函数。

当在类中定义了 `constructor` 方法时，使用 `new` 实例对象时，会调用 `constructor` 方法进行实例化，并且执行如下操作。

1. 在内存中创建一个新对象。
2. 这个新对象内部的 `[[Prototype]]` 指针被赋值为构造函数的 `prototype` 属性。
3. 构造函数内部的 `this` 指向新对象。
4. 执行构造函数内部的代码。
5. 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

```javascript
class Person {
    constructor(name) {
        console.log(arguments.length);
        this.name = name || null;
    }
}
class Student {
    constructor() {
        this.name = "default name";
    }
}
```

类实例化时传入的参数会用作构造函数的参数。如果不需要参数，则类名后面的括号也是可选的。

```javascript
let p1 = new Person;    // 0
let p2 = new Person();   // 0
let p3 = new Person("小刚");    // 1
console.log(p1.name);    // null
console.log(p2.name);    // null
console.log(p3.name);    // 小刚
let s2 = new Student();
console.log(s2.name);    // default name
```

默认情况下，类构造函数会在执行之后返回 `this` 对象。构造函数返回的对象会被用作实例化的对象，如果没有什么引用新创建的 `this` 对象，那么这个对象会被销毁。不过，如果返回的不是`this` 对象，而是其他对象，那么这个对象不会通过 `instanceof` 操作符检测出跟类有关联，因为这个对象的原型指针并没有被修改。

```javascript
class Person {
    constructor(override) {
        this.name = '小齐';
        if (override) {
            return {
                nickname: "昵称"
            };
        }
    }
}
let p1 = new Person(),
    p2 = new Person(true);
console.log(p1); // Person { name: '小齐' }
console.log(p1 instanceof Person); // true
console.log(p2); // { nickname: '昵称' }
console.log(p2 instanceof Person); // false
```

类构造函数与构造函数的主要区别是，调用类构造函数必须使用 `new` 操作符。而普通构造函数如果不使用 `new` 调用，那么就会以全局的 `this`（通常是 `window`）作为内部对象。调用类构造函数时如果忘了使用 `new` 则会抛出错误：

```javascript
function Person() {}
class Student {}
// 把window 作为this 来构建实例
let p = Person();
let a = Student();    // TypeError: Class constructor Student cannot be invoked without 'new'
```

类构造函数没有什么特殊之处，实例化之后，它会成为普通的实例方法（但作为类构造函数，仍然要使用 `new` 调用）。因此，实例化之后可以在实例上引用它：

```javascript
class Person {}
// 使用类创建一个新实例
let p1 = new Person();
p1.constructor();    // TypeError: Class constructor Person cannot be invoked without 'new'
// 使用对类构造函数的引用创建一个新实例
let p2 = new p1.constructor();    // 这里可以通过
```

在 ECMAScript 中，使用 `class` 定义的类，通过 `typeof` 来检测，其实质是一个函数：

```javascript
class Person {}
console.log(Person);    // [class Person]
console.log(typeof Person);    // function
```

类标识符有 `prototype` 属性，而这个原型也有一个 `constructor` 属性指向类自身：

```javascript
class Person{}
console.log(Person.prototype); // Person {}
console.log(Person === Person.prototype.constructor); // true
```

与普通构造函数一样，可以使用 `instanceof` 操作符检查构造函数原型是否存在于实例的原型链中：

```javascript
class Person {}
let p = new Person();
console.log(p instanceof Person); // true
```

类是 JavaScript 的一等公民，因此可以像其他对象或函数引用一样把类作为参数传递：

```javascript
// 类可以像函数一样在任何地方定义，比如在数组中
let classes = [
    class {
        constructor(id) {
            this.id = id;
            console.log(`instance ${this.id}`);
        }
    }
];
function tryInstance(classDefinition, id) {
    return new classDefinition(id);
}
let instance = tryInstance(classes[0], 3.14); // instance 3.14
```

与立即调用函数表达式相似，类也可以立即实例化：

```javascript
// 因为是一个类表达式，所以类名是可选的
let p = new class Person {
    constructor(x) {
        console.log(x);
    }
}('小强');    // 小强 
console.log(p); // Person {}
```

### 实例方法

每次创建实例时，都会执行类构造方法。而在类的构造方法中，通过 `this` 可以为该类添加 实例属性。每个实例都对应一个唯一的成员对象，这意味着所有成员都不会在原型上共享：

```javascript
class Person {
    constructor() {
        // 这个例子先使用对象包装类型定义一个字符串
        // 为的是在下面测试两个对象的相等性
        this.name = new String("小丽");
        this.sayName = () => console.log(this.name);
        this.nicknames = ['小节', '小点']
    }
}
let p1 = new Person(),
    p2 = new Person();
p1.sayName(); // 小丽
p2.sayName(); // 小丽
console.log(p1.name === p2.name); // false
console.log(p1.sayName === p2.sayName); // false
console.log(p1.nicknames === p2.nicknames); // false
p1.name = p1.nicknames[0];
p2.name = p2.nicknames[1];
p1.sayName(); // 小节
p2.sayName(); // 小点
```

静态属性或原型的数据属性必须定义在类定义的外面。

```javascript
class Person {
    sayName() {
        console.log(`${Person.greeting} ${this.name}`);
    }
}
// 在类上定义数据成员
Person.greeting = 'My name is';
// 在原型上定义数据成员
Person.prototype.name = '柯林';
let p = new Person();
p.sayName();    // My name is 柯林
```

<small>注意：类定义中之所以没有显示支持添加数据成员，是因为在共享目标上添加可变数据成员是一种反模式。一般来说，对象实例应该独自拥有通过 `this` 引用的数据。</small>

### 原型方法

为了在实例间共享方法，类定义语法把在类块中定义的方法作为原型方法。

```javascript
class Person {
    constructor() {
        // 添加到this 的所有内容都会存在于不同的实例上
        this.locate = () => console.log('instance');
    }
    // 在类块中定义的所有内容都会定义在类的原型上
    locate() {
        console.log('prototype');
    }
}
let p = new Person();
p.locate(); // instance
Person.prototype.locate(); // prototype
```

### 静态方法

可以在类上可以使用 `staitc` 关键字定义静态方法。调用静态方法不需要实例化该类，但不能通过一个类实例调用静态方法。在静态方法中，`this` 引用类自身。

```javascript
class Person {
    constructor() {
        // 添加到this 的所有内容都会存在于不同的实例上
        this.locate = () => console.log('instance', this);
    }
    // 定义在类的原型对象上
    locate() {
        console.log('prototype', this);
    }
    // 定义在类本身上
    static locate() {
        console.log('class', this);
    }
}
let p = new Person();
p.locate(); // instance Person { locate: [Function] }
Person.prototype.locate(); // prototype Person {}
Person.locate(); // class [class Person]
```

### 迭代器与生成器

类定义语法支持在原型和类本身上定义生成器方法：

```javascript
class Person {
    // 在原型上定义生成器方法
    * createNicknameIterator() {
        yield '杰克狗';
        yield '杰克鼠';
        yield '杰克猫';
    }
    // 在类上定义生成器方法
    static* createJobIterator() {
        yield '邦德一';
        yield '邦德二';
        yield '邦德三';
    }
}
let jobIter = Person.createJobIterator();
console.log(jobIter.next().value); // 邦德一
console.log(jobIter.next().value); // 邦德二
console.log(jobIter.next().value); // 邦德三
let p = new Person();
let nicknameIter = p.createNicknameIterator();
console.log(nicknameIter.next().value); // 杰克狗
console.log(nicknameIter.next().value); // 杰克鼠
console.log(nicknameIter.next().value); // 杰克猫
```

因为支持生成器方法，所以可以通过添加一个默认的迭代器，把类实例变成可迭代对象：

```javascript
class Person {
    constructor() {
        this.nicknames = ['杰克狗', '杰克鼠', '杰克猫'];
    }
    *[Symbol.iterator]() {
        yield *this.nicknames.entries();
    }
}
let p = new Person();
for (let [idx, nickname] of p) {
    console.log(nickname);
}
// 杰克狗
// 杰克鼠
// 杰克猫
```

也可以只返回迭代器实例：

```javascript
class Person {
    constructor() {
        this.nicknames = ['杰克狗', '杰克鼠', '杰克猫'];
    }
    [Symbol.iterator]() {
        return this.nicknames.entries();
    }
}
let p = new Person();
for (let [idx, nickname] of p) {
    console.log(nickname);
}
// 杰克狗
// 杰克鼠
// 杰克猫
```

## 继承

ECMAScript 6 通过 `extends` 关键字提供的语法糖来与任何拥有 `[[Construct]]` 和原型的对象实现的类继承机制。

```javascript
class Person {}
class Student extends Person{}
let s = new Student();
console.log(s instanceof Student);    // true
console.log(s instanceof Person);    // true
```

但是，这种继承方式也可以继承普通的构造函数。

```javascript
function Person() {}
class Student extends Person{}
let s = new Student();
console.log(s instanceof Student);    // true
console.log(s instanceof Person);    // true
```

这种继承方式可以用在类表达式上。

```javascript
let Student = class extends Person {}
```

子类还可以通过 `super` 关键字引用它们的原型，而且只能在子类中使用。子类中定义了构造方法，必须调用 `super()` 之后，才能使用 `this`。这里调用 `super()` 会调用父类的构造方法，并将返回的实例赋值给 `this`。

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
}
class Student extends Person{
    constructor(name) {
        super(name);
        console.log(this);
    }
}
let s = new Student("小王");    // Student { name: '小王' }
console.log(s.name);    // 小王
```

在静态方法中，可以通过 `super` 调用父类上定义的静态方法：

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
    static of(name) {
        return new Person(name);
    }
}

class Student extends Person{
    constructor(name) {
        super(name);
        console.log(this);
    }
    static of(name) {
        return  super.of(name);
    }
}
let s = Student.of("小军");
console.log(s);    // Person { name: '小军' }
console.log(s instanceof Student);    // false
console.log(s instanceof Person);    // true
```

ECMAScript 可以通过 `new.target` 实现一个供其他类继承，但本身不会被实例化的抽象基类。`new.target` 保存通过 `new` 关键字调用的类或函数。通过在实例化时检测`new.target` 是不是抽象基类，可以阻止对抽象基类的实例化：

```javascript
class Person {
    constructor(name) {
        if (new.target === Person) {
            throw new Error("Person不能直接被实例化");
        }
        this.name = name;
    }
}
class Student extends Person {
    constructor(name) {
        super(name);
    }
}

let s = new Student("小萍");
let p = new Person("小玲");    // Error: Person不能直接被实例化
```

也可以在抽象基类的构造方法中检查子类是否定义了某个方法。因为原型方法在调用类构造方法之前就已经存在了，所以可以通过 `this` 来检查相应的方法：

```javascript
class Person {
    constructor(name) {
        if (new.target === Person) {
            throw new Error("Person不能直接被实例化");
        }
        if (!this.action) {
            throw Error("继承的类必须定义action()方法");
        }
        this.name = name;
    }
}
class Student extends Person {
    constructor(name) {
        super(name);
    }
    action() {
        console.log("学生行为");
    }
}
class Employee extends Person {
    constructor(name) {
        super(name);
    }
}

let s = new Student("小萍");
let e = new Employee("小洪");    // Error: 继承的类必须定义action()方法
```

ES6 新增的 `class` 和 `extends` 可以很顺畅的为内置引用类型扩展功能。

```javascript
class MoreArray extends Array {
    first() {
        return this[0];
    }
    last() {
        return this[this.length - 1];
    }
}
let arr = new MoreArray(20, 92, 15, 40);
console.log(arr.first());    // 20
console.log(arr.last());    // 40
```

## 类混入

ECMAScript 6 支持单继承，但是可以通过现有特性模拟多重继承。

`extends` 关键字后面可以跟一个 JavaScript 表达式。任何可以解析为一个类或一个构造函数的表达式都是有效的。

```javascript
class Person {}
function getPerson() {
    console.log("对象操作");
    return Person;
}
class Student extends getPerson() {}    // 对象操作
```

混入模式可以通过在一个表达式中连缀多个混入元素来实现，这个表达式最终会解析为一个可以被继承的类。如果 `Person` 类需要组合A、B、C，则需要某种机制实现 B 继承 A，C 继承 B，而 `Person` 再继承 C，从而把 A、B、C 组合到 `Person` 中。实现这种模式有不同的策略。

一个策略是定义一组 “可嵌套” 的函数，每个函数分别接收一个父类作为参数，而将混入类定义为这个参数的子类，并返回这个类。这些组合函数可以连缀调用，最终组合成超类表达式：

```javascript
class Person {}
let Action = (Superclass) => class extends Superclass {
    action() {
        console.log('动作');
    }
};
let Face = (Superclass) => class extends Superclass {
    face() {
        console.log('表情');
    }
};
let Sex = (Superclass) => class extends Superclass {
    sex() {
        console.log('性别');
    }
};
class Student extends  Sex(Face(Action(Person))) {}
let s = new Student();
s.action();    // 动作
s.face();    // 表情
s.sex();    // 性别
```

也可以通过写一个辅助函数，可以把嵌套调用展开：

通过写一个辅助函数，可以把嵌套调用展开：

```javascript
class Person {}
let Action = (Superclass) => class extends Superclass {
    action() {
        console.log('动作');
    }
};
let Face = (Superclass) => class extends Superclass {
    face() {
        console.log('表情');
    }
};
let Sex = (Superclass) => class extends Superclass {
    sex() {
        console.log('性别');
    }
};
function mix(BaseClass, ...Mixins) {
    return Mixins.reduce((accumulator, current) => current(accumulator), BaseClass);
}
class Student extends mix(Person, Action, Face, Sex) {}
let s = new Student();
s.action();    // 动作
s.face();    // 表情
s.sex();    // 性别
```

<small>注意：很多JavaScript框架已经抛弃混入模式，转向组合模式。这反映了 “组合胜过继承” 的软件设计原则，且提供了很大的灵活性。<small>

## 总结

ECMAScript 6 新增的类语法很大程度上是基于语言既有的原型机制来实现的语法糖。但这种语法可以优雅地定义向后兼容的类，既可以继承内置类型，也可以继承自定义类型。类有效地跨越了对象实例、原型和类之间的鸿沟。