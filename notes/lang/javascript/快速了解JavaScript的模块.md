# 概述

现在 JavaScript 开发毋庸置疑会遇到代码量大和广泛使用第三方库的问题。解决这个问题的方案通常需要把代码拆分成很多部分，然后再某种方式将它们连接起来。

在ECMAScript6模块规范出现之前，虽然浏览器原生不支持模块的行为，但迫切需要这样的行为。ECMAScript 同样不支持模块，因此希望使用模块模式的库或代码库必须基于JavaScript的语法和词法特性“伪造”出类似模块的行为。

因为 JavaScript 是异步加载的解释型语言，所以得到广泛引用的各种模块实现也表现出不同的形态。这些不同的形态决定了不同的结果，但最终它们都实现了经典的模块模式。

# ES6 模块

ES6 最大的一个改进就是引入了模块规范。这个规范全方位简化了之前出现的模块加载器、原生浏览器支持意味着加载器以及其他预处理都不再必要。从很多方面看，ES6 模块系统是集 AMD 和 CommonJS 之大成者。

## 模块标签及定义

ES6 模块是作为一整块 JavaScript 代码而存在的。带有 `type="module"` 属性的 `<script>` 标签会告诉浏览器相关代码应该作为模块执行，而不是作为传统的脚本执行。模块可以嵌入在网页中，也可以作为外部文件引入：

```html
<script type="module">
// 模块代码
</script>
<script type="module" src="path/to/myModule.js"></script>
```

即使与常规 JavaScript 文件处理方式不同，JavaScript 模块文件也没有专门的内容类型。

与传统脚本不同，所有模块都会像 `<script defer>`加载的脚本一样按顺序执行。解析到`<script type="module">`标签后会立即下载模块文件，但执行会延迟到文档解析完成。无论对嵌入的模块代码，还是引入的外部模块文件，都是这样。`<script type="module">`在页面中出现的顺序就是它们执行的顺序。与`<script defer>`一样，修改模块标签的位置，无论是在`<head>`还是在`<body>`中，只会影响文件什么时候加载，而不会影响模块什么时候加载。

下面演示了嵌入模块代码的执行顺序：

```html
<!-- 第二个执行 -->
<script type="module"></script>
<!-- 第三个执行 -->
<script type="module"></script>
<!-- 第一个执行 -->
<script></script>
```

另外，还可以加在外部 JS 模块定义：

```html
<!-- 第二个执行 -->
<script type="module" src="module.js"></script>
<!-- 第三个执行 -->
<script type="module" src="module.js"></script>
<!-- 第一个执行 -->
<script><script>
```

也可以给模块标签添加 `async` 属性。这样影响是双重的，不仅模块执行顺序不再与 `<script>` 标签在页面中的顺序绑定，模块也不会等待文档完成解析才执行。不过，入口模块必须等待其依赖加载完成。

与 `<script type="module">` 标签关联的 ES6 模块被认为是模块图中的入口模块。一个页面上有多少入口没有限制，重复加载同一个模块也没有限制。同一个模块无论在一个页面中被加载多少次，也不管它是如何加载的，实际上都只会加载一次，如下面的代码所示：

```html
<!-- moduleA 在这个页面上只会被加载一次 -->
<script type="module">
import './moduleA.js'
<script>
<script type="module">
import './moduleA.js'
<script>
<script type="module" src="./moduleA.js"></script>
<script type="module" src="./moduleA.js"></script>
```

嵌入的模块定义代码不能使用 `import` 加载到其他模块。只有通过外部文件加载的模块才可以使用 `import` 加载。因此，嵌入模块只适合作为入口模块。

## 模块加载

ECMAScript 6 模块的独特之处在于，既可以通过浏览器原生加载，也可以与第三方加载器和构建工具一起加载。有些浏览器还没有原生支持ES6 模块，因此可能还需要第三方工具。事实上，很多时候使用第三方工具可能会更方便。

完全支持ECMAScript 6 模块的浏览器可以从顶级模块加载整个依赖图，且是异步完成的。浏览器会解析入口模块，确定依赖，并发送对依赖模块的请求。这些文件通过网络返回后，浏览器就会解析它们的内容，确定它们的依赖，如果这些二级依赖还没有加载，则会发送更多请求。这个异步递归加载过程会持续到整个应用程序的依赖图都解析完成。解析完依赖图，应用程序就可以正式加载模块了。

这个过程与AMD 风格的模块加载非常相似。模块文件按需加载，且后续模块的请求会因为每个依赖模块的网络延迟而同步延迟。即，如果moduleA 依赖moduleB，moduleB 依赖moduleC。浏览器在对moduleB 的请求完成之前并不知道要请求moduleC。这种加载方式效率很高，也不需要外部工具，但加载大型应用程序的深度依赖图可能要花费很长时间。

## 模块行为

ECMAScript 6 模块借用了CommonJS 和AMD 的很多优秀特性。下面简单列举一些。

- 模块代码只在加载后执行。
- 模块只能加载一次。
- 模块是单例。
- 模块可以定义公共接口，其他模块可以基于这个公共接口观察和交互。
- 模块可以请求加载其他模块。
- 支持循环依赖。

ES6 模块系统也增加了一些新行为。

- ES6 模块默认在严格模式下执行。
- ES6 模块不共享全局命名空间。
- 模块顶级this 的值是undefined（常规脚本中是window）。
- 模块中的var 声明不会添加到window 对象。
- ES6 模块是异步加载和执行的。

浏览器运行时在知道应该把某个文件当成模块时，会有条件地按照上述 ECMAScript 6 模块行为来施加限制。与 `<script type="module">` 关联或者通过 `import` 语句加载的 JavaScript 文件会被认定为模块。

## 模块导出

ES6 模块的公共导出系统与CommonJS 非常相似。控制模块的哪些部分对外部可见的是export 关键字。ES6 模块支持两种导出：命名导出和默认导出。不同的导出方式对应不同的导入方式，下一节会介绍导入。

export 关键字用于声明一个值为命名导出。导出语句必须在模块顶级，不能嵌套在某个块中：

```javascript
// 允许
export ...
// 不允许
if (condition) {
export ...
}
```

导出值对模块内部JavaScript 的执行没有直接影响，因此export 语句与导出值的相对位置或者
export 关键字在模块中出现的顺序没有限制。export 语句甚至可以出现在它要导出的值之前：

```javascript
// 允许
const foo = 'foo';
export { foo };
// 允许
export const foo = 'foo';
// 允许，但应该避免
export { foo };
const foo = 'foo';
```
命名导出（named export）就好像模块是被导出值的容器。行内命名导出，顾名思义，可以在同一行执行变量声明。下面展示了一个声明变量同时又导出变量的例子。外部模块可以导入这个模块，而 foo 将成为这个导入模块的一个属性：

```javascript
export const foo = 'foo';
```

变量声明跟导出可以不在一行。可以在export 子句中执行声明并将标识符导出到模块的其他地方：

```javascript
const foo = 'foo';
export { foo };
```

导出时也可以提供别名，别名必须在export 子句的大括号语法中指定。因此，声明值、导出值和为导出值提供别名不能在一行完成。在下面的例子中，导入这个模块的外部模块可以使用myFoo 访问导出的值：

```javascript
const foo = 'foo';
export { foo as myFoo };
```

因为ES6 命名导出可以将模块作为容器，所以可以在一个模块中声明多个命名导出。导出的值可以在导出语句中声明，也可以在导出之前声明：

```javascript
export const foo = 'foo';
export const bar = 'bar';
export const baz = 'baz';
```

考虑到导出多个值是常见的操作，ES6 模块也支持对导出声明分组，可以同时为部分或全部导出值指定别名：

```javascript
const foo = 'foo';
const bar = 'bar';
const baz = 'baz';
export { foo, bar as myBar, baz };
```

默认导出（default export）就好像模块与被导出的值是一回事。默认导出使用default 关键字将一个值声明为默认导出，每个模块只能有一个默认导出。重复的默认导出会导致SyntaxError。

下面的例子定义了一个默认导出，外部模块可以导入这个模块，而这个模块本身就是foo 的值：

```javascript
const foo = 'foo';
export default foo;
```

另外，ES6 模块系统会识别作为别名提供的default 关键字。此时，虽然对应的值是使用命名语法导出的，实际上则会成为默认导出：

```javascript
const foo = 'foo';
// 等同于export default foo;
export { foo as default };
```

因为命名导出和默认导出不会冲突，所以ES6 支持在一个模块中同时定义这两种导出：

```javascript
const foo = 'foo';
const bar = 'bar';
export { bar };
export default foo;
```

这两个export 语句可以组合为一行：

```javascript
const foo = 'foo';
const bar = 'bar';
export { foo as default, bar };
```

ES6 规范对不同形式的export 语句中可以使用什么不可以使用什么规定了限制。某些形式允许声明和赋值，某些形式只允许表达式，而某些形式则只允许简单标识符。注意，有的形式使用了分号，有的则没有：

```javascript
// 命名行内导出
export const baz = 'baz';
export const foo = 'foo', bar = 'bar';
export function foo() {}
export function* foo() {}
export class Foo {}
// 命名子句导出
export { foo };
export { foo, bar };
export { foo as myFoo, bar };
// 默认导出
export default 'foo';
export default 123;
export default /[a-z]*/;
export default { foo: 'foo' };

export { foo, bar as default };
export default foo
export default function() {}
export default function foo() {}
export default function*() {}
export default class {}
// 会导致错误的不同形式：
// 行内默认导出中不能出现变量声明
export default const foo = 'bar';
// 只有标识符可以出现在export 子句中
export { 123 as foo }
// 别名只能在export 子句中出现
export const foo = 'foo' as myFoo;
```

注意：什么可以获不可以与 export 关键字出现在同一行可能很难记住。一般来说，声明、赋值和导出标识符最好分开。这样就不容易搞错了，同时也可以让 export 语句集中在一块。

## 模块导入

模块可以通过使用import 关键字使用其他模块导出的值。与export 类似，import 必须出现在模块的顶级：

```javascript
// 允许
import ...
// 不允许
if (condition) {
import ...
}
```

import 语句被提升到模块顶部。因此，与export 关键字类似，import 语句与使用导入值的语句的相对位置并不重要。不过，还是推荐把导入语句放在模块顶部。

```javascript
// 允许
import { foo } from './fooModule.js';
console.log(foo); // 'foo'
// 允许，但应该避免
console.log(foo); // 'foo'
import { foo } from './fooModule.js';
```

模块标识符可以是相对于当前模块的相对路径，也可以是指向模块文件的绝对路径。它必须是纯字符串，不能是动态计算的结果。例如，不能是拼接的字符串。

如果在浏览器中通过标识符原生加载模块，则文件必须带有.js 扩展名，不然可能无法正确解析。不过，如果是通过构建工具或第三方模块加载器打包或解析的ES6 模块，则可能不需要包含文件扩展名。

```javascript
// 解析为/components/bar.js
import ... from './bar.js';
// 解析为/bar.js
import ... from '../bar.js';
// 解析为/bar.js
import ... from '/bar.js';
```

不是必须通过导出的成员才能导入模块。如果不需要模块的特定导出，但仍想加载和执行模块以利用其副作用，可以只通过路径加载它：

```javascript
import './foo.js';
```

导入对模块而言是只读的，实际上相当于const 声明的变量。在使用`*`执行批量导入时，赋值给别名的命名导出就好像使用Object.freeze()冻结过一样。直接修改导出的值是不可能的，但可以修改导出对象的属性。同样，也不能给导出的集合添加或删除导出的属性。要修改导出的值，必须使用有内部变量和属性访问权限的导出方法。

```javascript
import foo, * as Foo './foo.js';
foo = 'foo'; // 错误
Foo.foo = 'foo'; // 错误
foo.bar = 'bar'; // 允许
```
命名导出和默认导出的区别也反映在它们的导入上。命名导出可以使用`*`批量获取并赋值给保存导出集合的别名，而无须列出每个标识符：

```javascript
const foo = 'foo', bar = 'bar', baz = 'baz';
export { foo, bar, baz }
import * as Foo from './foo.js';
console.log(Foo.foo); // foo
console.log(Foo.bar); // bar
console.log(Foo.baz); // baz
```

要指名导入，需要把标识符放在import 子句中。使用import 子句可以为导入的值指定别名：

```javascript
import { foo, bar, baz as myBaz } from './foo.js';
console.log(foo); // foo
console.log(bar); // bar
console.log(myBaz); // baz
```

默认导出就好像整个模块就是导出的值一样。可以使用default 关键字并提供别名来导入。也可
以不使用大括号，此时指定的标识符就是默认导出的别名：

```javascript
// 等效
import { default as foo } from './foo.js';
import foo from './foo.js';
```

如果模块同时导出了命名导出和默认导出，则可以在import 语句中同时取得它们。可以依次列出特定导出的标识符来取得，也可以使用`*`来取得：

```javascript
import foo, { bar, baz } from './foo.js';
import { default as foo, bar, baz } from './foo.js';
import foo, * as Foo from './foo.js';
```

注意：本书写作时，有一个动态导入模块的提案处在第三阶段，参见GitHub 上的tc39/proposals 页面。

## 模块转移导出

模块导入的值可以直接通过管道转移到导出。此时，也可以将默认导出转换为命名导出，或者相反。如果想把一个模块的所有命名导出集中在一块，可以像下面这样在bar.js 中使用`*`导出：

```javascript
export * from './foo.js';
```

这样，foo.js 中的所有命名导出都会出现在导入bar.js 的模块中。如果foo.js 有默认导出，则该语法会忽略它。使用此语法也要注意导出名称是否冲突。如果foo.js 导出baz，bar.js 也导出baz，则最终导出的是bar.js 中的值。这个“重写”是静默发生的：
foo.js

```javascript
export const baz = 'origin:foo';
```

bar.js

```javascript
export * from './foo.js';
export const baz = 'origin:bar';
```

main.js

```javascript
import { baz } from './bar.js';
console.log(baz); // origin:bar
```

此外也可以明确列出要从外部模块转移本地导出的值。该语法支持使用别名：

```javascript
export { foo, bar as myBar } from './foo.js';
```

类似地，外部模块的默认导出可以重用为当前模块的默认导出：

```javascript
export { default } from './foo.js';
```

这样不会复制导出的值，只是把导入的引用传给了原始模块。在原始模块中，导入的值仍然是可用的，与修改导入相关的限制也适用于再次导出的导入。

在重新导出时，还可以在导入模块修改命名或默认导出的角色。比如，可以像下面这样将命名导出指定为默认导出：

```javascript
export { foo as default } from './foo.js';
```

## 工作者模块

ECMAScript 6 模块与Worker 实例完全兼容。在实例化时，可以给工作者传入一个指向模块文件的路径，与传入常规脚本文件一样。Worker 构造函数接收第二个参数，用于说明传入的是模块文件。

下面是两种类型的Worker 的实例化行为：

```javascript
// 第二个参数默认为{ type: 'classic' }
const scriptWorker = new Worker('scriptWorker.js');
const moduleWorker = new Worker('moduleWorker.js', { type: 'module' });
```
在基于模块的工作者内部，self.importScripts()方法通常用于在基于脚本的工作者中加载外部脚本，调用它会抛出错误。这是因为模块的import 行为包含了importScripts()。

## 向后兼容

ECMAScript 模块的兼容是个渐进的过程，能够同时兼容支持和不支持的浏览器对早期采用者是有价值的。对于想要尽可能在浏览器中原生使用ECMAScript 6 模块的用户，可以提供两个版本的代码：基于模块的版本与基于脚本的版本。如果嫌麻烦，可以使用第三方模块系统（如SystemJS）或在构建时将ES6 模块进行转译，这都是不错的方案。

第一种方案涉及在服务器上检查浏览器的用户代理，与支持模块的浏览器名单进行匹配，然后基于匹配结果决定提供哪个版本的JavaScript 文件。这个方法不太可靠，而且比较麻烦，不推荐。更好、更优雅的方案是利用脚本的type 属性和nomodule 属性。

浏览器在遇到`<script>`标签上无法识别的type 属性时会拒绝执行其内容。对于不支持模块的浏览器，这意味着`<script type="module">`不会被执行。因此，可以在`<script type="module">`标签旁边添加一个回退`<script>`标签：

```javascript
// 不支持模块的浏览器不会执行这里的代码
<script type="module" src="module.js"></script>
// 不支持模块的浏览器会执行这里的代码
<script src="script.js"></script>
```

当然，这样一来支持模块的浏览器就有麻烦了。此时，前面的代码会执行两次，显然这不是我们想要的结果。为了避免这种情况，原生支持ECMAScript 6 模块的浏览器也会识别nomodule 属性。此属性通知支持ES6 模块的浏览器不执行脚本。不支持模块的浏览器无法识别该属性，从而忽略这个属性的存在。

因此，下面代码会生成一个设置，在这个设置中，支持模块和不支持模块的浏览器都只会执行一段脚本：

```javascript
// 支持模块的浏览器会执行这段脚本
// 不支持模块的浏览器不会执行这段脚本
<script type="module" src="module.js"></script>
// 支持模块的浏览器不会执行这段脚本
// 不支持模块的浏览器会执行这段脚本
<script nomodule src="script.js"></script>
```

# 总结

模块系统是管理复杂性的永恒工具。开发者可以通过它创建逻辑彼此独立的代码段，在这些代码段之间声明依赖，并将它们连接在一起。此外，这种模式也是经证明能够优雅扩展到任意复杂度且跨平台的方案。

多年依赖，CommonJS和AMD这两个分别针对服务器端环境和首延迟限制的客户端环境的模块系统长期分裂。两个系统都获得了爆炸性增强，但为它们编写的代码则在很多方面不一致，经常也会带有冗余的样板代码。而且，这两个系统都没有在浏览器中实现。缺乏兼容导致出现了相关工具，从而让在浏览器中实现模块成为可能。

ECMAScript6规范重新定义了浏览器模块，集之前两个系统之长于一身，并通过更简单的声明性语法暴露出来。浏览器对原生模块的支持越来越好，但也提供了稳健的工具以实现从不支持到支持 ES6 模块的过渡。