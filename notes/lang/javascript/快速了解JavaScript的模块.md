# 概述

随着现代 JavaScript 开发 Web 应用变得复杂，命名冲突和依赖关系也变得难以处理，因此需要模块化。而引入模块化，可以避免命名冲突、方便依赖关系管理、提高了代码的复用性和和维护性，因此，在 JavaScript 没有模块功能的前提下，只能通过第三方规范实现模块化：

- `CommonJS`：同步模块定义，用于服务器端。
- `AMD`：异步模块定义， 用于浏览器端。
- `CMD`：异步模块定义，用于浏览器端。
- `UMD`：统一 `COmmonJS` 和 `AMD` 模块化方案的定义。 

它们都是基于 JavaScript 的语法和词法特性 “伪造” 出类似模块的行为。而 `TC-39` 在 ECMAScript 2015 中加入了模块规范，简化了上面介绍的模块加载器，原生意味着可以取代上述的规范，成为浏览器和服务器通用的模块解决方案，比使用库更有效率。而 ES6 的模块化的设计目标：

- 像 `CommonJS` 一样简单的语法。
- 模块必须是静态的结构
- 支持模块的 `异步加载` 和 `同步加载`，能同时用在 `server` 和 `client` 端
- 支持模块加载的 ‘灵活配置’
- 更好地支持模块之间的循环引用
- 拥有语言层面的支持，超越 `CommonJS` 和 `AMD`

ECMAScript 在 2015 年开始支持模块标准，此后逐渐发展，现已经得到了所有主流浏览器的支持。ECMAScript 2015 版本也被称为 ECMAScript 6。

# 模块

ES6 模块借用了 `CommonJS` 和 `AMD` 的很多优秀特性，如下所示：

- 模块代码只在加载后执行。
- 模块只能加载一次。
- 模块是单例。
- 模块可以定义公共接口，其他模块可以基于这个公共接口观察和交互。
- 模块可以请求加载其他模块。
- 支持循环依赖。

ES6 模块系统也增加了一些新行为。

- ES6 模块默认在严格模式下执行。
- ES6 模块不共享全局命名空间。
- 模块顶级 `this` 的值是 `undefined`；常规脚本中是 `window`。
- 模块中的 `var` 声明不会添加到 `window` 对象。
- ES6 模块是异步加载和执行的。

浏览器运行时在知道应该把某个文件当成模块时，会有条件地按照上述 ES6 模块行为来施加限制。与 `<script type="module">` 关联或者通过 `import` 语句加载的 JavaScript 文件会被认定为模块。

# 导出

ES6 模块内部的所有变量，外部无法获取，因此提供了 `export` 关键字从模块中导出实时绑定的函数、对象或原始值，这样其他程序可以通过 `import` 关键字使用它们。`export` 支持两种导出方式：命名导出和默认导出。不同的导出方式对应不同的导入方式。

在 ES6 模块中，无论是否声明 `"use strict;"` 语句，默认情况下模块都是在严格模式下运行。`export` 语句不能用在嵌入式脚本中。

## 命名导出

通过在声明的前面加上 `export` 关键字，一个模块可以导出多个内容。这些导出的内容通过名字区分，被称为命名导出。

```javascript
// 导出单个特性（可以导出 var，let，const）
export let name = "小明";
export function sayHi(name) {
    console.log(`Hello, ${name}!`);
}
export class Sample {
    ...
}
```

或者导出事先定义的特性

```javascript
let name = "小明";
const age = 18;
function sayHi(name) {
    console.log(`Hello, ${name}!`);
}
export {name, age, sayHi}
```

导出时也可以指定别名，别名必须在 `export` 子句的大括号语法中指定。因此，声明值、导出值和未导出值提供别名不能在一行完成。

```javascript
export {name as username, age, sayHi}
```

但导出语句必须在模块顶级，不能嵌套在某个块中：


```javascript
// 允许
export ...
// 不允许
if (condition) {
    export ...
}
```

## 默认导出

默认导出就好像模块与被导出的值是一回事。默认导出使用 `default` 关键字将一个值声明为默认导出，**每个模块只能有一个默认导出**。重复的默认导出会导致 `SyntaxError`。如下所示：

```javascript
// 导出事先定义的特性作为默认值
export default {
    name: "Xiao Ming",
    age: 18,
    sex: "boy"
};
export {sayHi as default}    // ES 6 模块会识别作为别名提供的 default 关键字。此时，虽然对应的值是使用命名语法导出的，实际上则会称为默认导出 等同于 export default function sayHi() {}
// 导出单个特性作为默认值
export default function () {...}
export default class {...}
```

ES6 规范对不同形式的 `export` 语句中可以使用什么不可以使用什么规定了限制。某些形式允许声明和赋值，某些形式只允许表达式，而某些形式则只允许简单标识符。注意，有的形式使用了分号，有的则没有。

下面列出几种会导致错误的 `export` 形式：

```javascript
// 会导致错误的不同形式：
// 行内默认导出中不能出现变量声明
export default const name = '小刘';
// 只有标识符可以出现在export 子句中
export { 123 as name }
// 别名只能在export 子句中出现
export const name = '小红' as uname;
```

<small>注意：声明、赋值和导出标识符最好分开。这样不容易搞错了，同时也可以让 `export` 语句集中在一块。而且，没有被 `export` 关键字导出的变量、函数或类会在模块内保持私有。<small>

## 模块重定向

模块导入的值还可以再次导出，这样的话，可以在父模块集中多个模块的多个导出。可以使用 `export from` 语法实现：

```javascript
export {default as m1, name} from './module1.js'
// 等效于
import {default as m1, name} from "./module1.js"
export {m1, name}
```

外部模块的默认导出也可以重用为当前模块的默认导出：

```javascript
export { default } from './module1.js';
```

也可以在重新导出时，将导入模块修改为默认导出，如下所示：

```javascript
export { name as default } from './module1.js';
```

而想要将所有命名导出可以使用如下语法：

```javascript
export * from './module1.js';
```

该语法会忽略默认导出。但这种语法也要注意导出名称是否冲突。如下所示：

```javascript
// module1.js
export const name = "module1:name";
// module2.js
export * from './mudule1.js'
export const name = "module2:name";
// index.js
import { name } from './module2.js';
console.log(name); // module2:name
```

最终输出的是 `module2.js` 中的值，这个 “重写” 是静默发生的。

# 导入

使用 `export` 关键字定义了模块的对外接口以后，其它模块就能通过 `import` 关键字加载这个模块了。但与 `export` 类似，`import` 也必须出现在模块的顶级：

```javascript
// 允许
import ...
// 不允许
if (condition) {
    import ...
}
```

模块标识符可以是相对于当前模块的相对路径，也可以是指向模块文件的绝对路径。它必须是纯字符串，不能是动态计算的结果。例如，不能是拼接的字符串。

当使用 `export` 命名导出时，可以使用 `*` 批量获取并赋值给保存导出集合的别名，而无须列出每个标识符：

```javascript
const name = "Xiao Ming", age = 18, sex = "boy";
export {name, age, sex}

// 上面的命名导出可以使用如下形式导入(上面的代码是在 module1.js 模块中)
import * as Sample from "./module1.js"
console.log(`My name is ${Sample.name}, A ${Sample.sex}，${Sample.age} years old.`);
```

也可以指名导入，只需要把名字放在 `{}` 中即可：

```javascript
import {name, sex as s, age} from "./module1.js";
console.log(`My name is ${name}, A ${s}，${age} years old.`);
```

`import` 引入是采用的 `Singleton` 模式，多次使用 `import` 引入同一个模块时，只会引入一次该模块的实例：

```javascript
import {name, age} from "./module1.js";
import {sex as s} from "./module1.js";
// 等同于，并且只会引入一个 module1.js 实例
import {name, sex as s, age} from "./module1.js";
```

而使用默认导出的话，可以使用 `default` 关键字并提供别名来导入，也可以直接使用标识符就是默认导出的别名导入：

```javascript
import {default as Sample} from "./module1.js"
// 与下面的方式等效
import Sample from "./module1.js"
```

而模块中同时有命名导出和默认导出，可以在 `import` 语句中同时导入。下面三种方式都等效。

```javascript
import Sample, {sayHi} from "./module1.js"
import {default as Sample, sayHi} from "./module1.js"
import Sample, * as M1 from "./module1.js"
```

当然，也可以将整个模块作为副作用而导入，而不导入模块中的特定内容。这将运行模块中的全局代码，但实际上不导入任何值。

```javascript
import './module1.js'
```

`import` 导入的值与 `export` 导出的值是绑定关系，绑定是不可变的。因此，`import` 对所导入的模块是只读的。但是可以通过调用被导入模块的函数来达到目的。

```javascript
import Sample, * as M1 from "./module1.js"
Sample = "Modify Sample";    // 错误
M1.module1 = "Module 1";    // 错误
Sample.name = "小亮";       // 允许
```

这样做的好处是能够支持循环依赖，并且一个大的模块可以拆成若干个小模块时也可以运行，只要不尝试修改导入的值。

<small>注意：如果要在浏览器中原生加载模块，则文件必须带有 `.js` 扩展名，不然可能无法解析。而使用构建工具或第三方模块加载器打包或解析 `ES6` 模块，可能不需要包含扩展名。<small>

## import()

标准的 `import` 关键字导入模块是静态的，会使所有被导入的模块，在加载时就被编译。而最新的 ES11 标准中引入了动态导入函数 `import()`，不必预先加载所有模块。该函数会将模块的路径作为参数，并返回一个 `Promise`，在它的 `then` 回调里使用加载后的模块：

```javascript
import ('./module1.mjs')
    .then((module) => {
        // Do something with the module.
    });
```

这种使用方式也支持 `await` 关键字。

```javascript
let module = await import('./module1.js');
```

`import()` 的使用场景如下：

- 按需加载。
- 动态构建模块路径。
- 条件加载。

# 加载

ES6 模块既可以通过浏览器原生加载，也可以与第三方加载器和构建工具一起加载。

完全支持 ES6 模块的浏览器可以从顶级模块异步加载整个依赖图。浏览器会解析入口模块，确定依赖，并发送对依赖模块的请求。这些文件通过网络返回后，浏览器会解析它们的内容，确认依赖，如果二级依赖还没有加载，则会发送更多请求。这个异步递归加载过程会持续到整个依赖图都解析完成。解析完依赖，应用就可以正式加载模块了。

模块文件按需加载，且后续模块的请求会因为每个依赖模块的网络延迟而同步延迟。即，`module1` 依赖 `module2`，`module2` 依赖 `module3`。浏览器在对 `module2` 的请求完成之前并不知道要请求 `module3`。这种架子啊方式效率高，也不需要外部工具，但加载大型应用的深度依赖图可能要花费很长时间。

## HTML

想要在 HTML 页面中使用 ES6 模块，需要将 `type="module"` 属性放在 `<script>` 标签中，来声明该 `<script>` 所包含的代码在浏览器中作为模块执行。它可以嵌入在网页中，也可以作为外部文件引入：

```html
<script type="module">
    // 模块代码
</script>
<script type="module" src="./module1.js"></script>
```

`<script type="module">`模块加载的顺序与 `<script defer>` 加载的脚本一样按顺序执行。但执行会延迟到文档解析完成，但执行顺序就是`<script type="module">`在页面中出现的顺序。

也可以给模块标签添加 `async` 属性。这样影响是双重的，不仅模块执行顺序不再与 `<script>` 标签在页面中的顺序绑定，模块也不会等待文档完成解析才执行。不过，入口模块必须等待其依赖加载完成。

## Worker

`Worker` 为了支持 ES6 模块，在 `Worker` 构造函数中可以接收第二个参数，其 `type` 属性的默认值是 `classic`，可以将 `type` 设置为 `module` 来加载模块文件。如下所示：

```javascript
// 第二个参数默认为{ type: 'classic' }
const scriptWorker = new Worker('scriptWorker.js');
const moduleWorker = new Worker('moduleWorker.js', { type: 'module' });
```

在基于模块的工作者内部，`self.importScripts()` 方法通常用于在基于脚本的工作者中加载外部脚本，调用它会抛出错误。这是因为模块的 `import` 行为包含了 `importScripts()`。

## 向后兼容

如果浏览器原生支持 ES6 模块，可以直接使用，而不支持的浏览器可以使用第三方模块系统（`System.js`）或在构建时将 ES6 模块进行转译。

脚本模块可以使用 `type="module"` 属性设定，而对于不支持模块的浏览器，可以使用 `nomodule` 属性。此属性会通知支持 ES6 模块的浏览器不执行脚本。不支持模块的浏览器无法识别该属性，从而忽略该属性。如下所示：

```html
// 支持模块的浏览器会执行这段脚本
// 不支持模块的浏览器不会执行这段脚本
<script type="module" src="module.js"></script>
// 支持模块的浏览器不会执行这段脚本
// 不支持模块的浏览器会执行这段脚本
<script nomodule src="script.js"></script>
```

# 总结

ES6 在语言层面上支持了模块，结束了 `CommonJS` 和 `AMD` 这两个模块加载器的长期分裂状况，重新定义了模块功能，集两个规范于一身，并通过简单的语法声明来暴露。

模块的使用不同方式加载 `.js` 文件，它与脚本有很大的不同：

1. 模块始终使用 `use strict` 执行严格模式。
2. 在模块的顶级作用域创建的变量，不会被自动添加到共享的全局作用域，它们只会在模块顶级作用域的内部存在。
3. 模块顶级作用域的 `this` 值为 `undefined`。
4. 模块不允许在代码中使用 HTML 风格的注释。
5. 对于需要让模块外部代码访问的内容，模块必须导出它们。
6. 允许模块从其他模块导入绑定。
7. 模块代码执行一次。导出仅创建一次，然后会在导入之间共享。

浏览器对原生模块的支持越来越好，但也提供了稳健的工具以实现从不支持到支持 ES6 模块的过渡。