JavaScript 一直以来被认为是最难调试的编程语言之一，因为它是动态的，且多年来没有适当的开
发工具。错误经常会以令人迷惑的浏览器消息形式抛出，比如"object expected"。这样的消息没有上下文，因此很难理解。ECMAScript 第3 版致力于改进这个方面，引入了try/catch 和throw 语句，以及一些错误类型，以帮助开发者在出错时正确地处理它们。几年后，JavaScript 调试器和排错工具开始在浏览器中出现。到了2008 年，大多数浏览器支持一些JavaScript 调试能力。

有了适当的语言和开发工具，Web 开发者如今已可以实现适当的错误处理并找到问题的原因。

## 浏览器错误报告

所有主流桌面浏览器，包括IE/Edge、Firefox、Safari、Chrome 和Opera，都提供了向用户报告错误的机制。默认情况下，所有浏览器都会隐藏错误信息。一个原因是除了开发者之外这些信息对别人没什么用，另一个原因是网页在正常操作中报错的固有特性。

### 桌面控制台

所有现代桌面浏览器都会通过控制台暴露错误。这些错误可以显示在开发者工具内嵌的控制台中。在前面提到的所有浏览器中，访问开发者工具的路径是相似的。可能最简单的查看错误的方式就是在页面上单击鼠标右键，然后在上下文菜单中选择Inspect（检查）或Inspect Element（检查元素），然后再单击Console（控制台）选项卡。

### 移动控制台

移动浏览器不会直接在设备上提供控制台界面。不过，还是有一些途径可以在移动设备中检查错误。

Chrome 移动版和Safari 的iOS 版内置了实用工具，支持将设备连接到宿主操作系统中相同的浏览器。然后，就可以在对应的桌面浏览器中查看错误了。这涉及设备之间的硬件连接，且要遵循不同的操作步骤，比如Chrome 的操作步骤参见Google Developers 网站的文章《Android 设备的远程调试入门》，Safari 的操作步骤参见Apple Developer 网站的文章“Safari Web Inspector Guide”。

此外也可以使用第三方工具直接在移动设备上调试。Firefox 常用的调试工具是Firebug Lite，这需要通过JavaScript 的书签小工具向当前页面中加入Firebug 脚本才可以。脚本运行后，就可以直接在移动浏览器上打开调试界面。Firebug Lite 也有面向其他浏览器（如Chrome）的版本。

## 错误处理

错误处理在编程中的重要性毋庸置疑。所有主流Web 应用程序都需要定义完善的错误处理协议，
大多数优秀的应用程序有自己的错误处理策略，尽管主要逻辑是放在服务器端的。事实上，服务器端团队通常会花很多精力根据错误类型、频率和其他重要指标来定义规范的错误日志机制。最终实现通过简单的数据库查询或报告生成脚本就可以了解应用程序的运行状态。

错误处理在应用程序的浏览器端进展较慢，尽管其重要性一点也不低。这里有一个重要的事实：大多数上网的人没有技术背景，甚至连什么是浏览器都不十分清楚，而且有的人不知道自己使用的是什么浏览器。如前所述，当网页中的JavaScript 脚本发生错误时，不同浏览器的处理方式不同。不过浏览器处理JavaScript 报告错误的默认方式对用户并不友好。最好的情况是用户自己不知道发生了什么，然后再重试；最坏的情况是用户感觉特别厌烦，于是永远不回来了。有一个良好的错误处理策略可以让用户知道到底发生了什么。为此，必须理解各种捕获和处理JavaScript 错误的方式。

### try-catch 语句

ECMA-262 第3 版新增了try/catch 语句，作为在JavaScript 中处理异常的一种方式。基本的语法
如下所示，跟Java 中的try/catch 语句一样：

```javascript
try {
// 可能出错的代码
} catch (error) {
// 出错时要做什么
}
```

任何可能出错的代码都应该放到try 块中，而处理错误的代码则放在catch 块中，如下所示：

```javascript
try {
window.someNonexistentFunction();
} catch (error){
console.log("An error happened!");
}
```
如果try 块中有代码发生错误，代码会立即退出执行，并跳到catch 块中。catch 块此时接收到
一个对象，该对象包含发生错误的相关信息。与其他语言不同，即使在catch 块中不使用错误对象，也必须为它定义名称。错误对象中暴露的实际信息因浏览器而异，但至少包含保存错误消息的message属性。ECMA-262 也指定了定义错误类型的name 属性，目前所有浏览器中都有这个属性。因此，可以像下面的代码这样在必要时显示错误消息：

```javascript
try {
window.someNonexistentFunction();
} catch (error){
console.log(error.message);
}
```

这个例子使用message 属性向用户显示错误消息。message 属性是唯一一个在IE、Firefox、Safari、Chrome 和Opera 中都有的属性，尽管每个浏览器添加了其他属性。IE 添加了description 属性（其值始终等于message）和number 属性（它包含内部错误号）。Firefox 添加了fileName、lineNumber和stack（包含栈跟踪信息）属性。Safari 添加了line（行号）、sourceId（内部错误号）和sourceURL属性。同样，为保证跨浏览器兼容，最好只依赖message 属性。

**1. finally 子句**

try/catch 语句中可选的finally 子句始终运行。如果try 块中的代码运行完，则接着执行
finally 块中的代码。如果出错并执行catch 块中的代码，则finally 块中的代码仍执行。try 或
catch 块无法阻止finally 块执行，包括return 语句。比如：

```javascript
function testFinally(){
try {
return 2;
} catch (error){
return 1;
} finally {
return 0;
}
}
```

这个函数在try/catch 语句的各个部分都只放了一个return 语句。看起来该函数应该返回2，因为它在try 块中，不会导致错误。但是，finally 块的存在导致try 块中的return 语句被忽略。因此，无论什么情况下调用该函数都会返回0。如果去掉finally 子句，该函数会返回2。如果写出 finally 子句，catch 块就成了可选的（它们两者中只有一个是必需的）。

注意：主要代码中包含了 `finally` 子句，`try` 块或 `catch` 块中的 `return` 语句就会被忽略，理解这一点很重要。在使用 `finally` 时一定要仔细确认代码的行为。

**2. 错误类型**

代码执行过程中会发生各种类型的错误。每种类型都会对应一个错误发生时抛出的错误对象。
ECMA-262 定义了以下8 种错误类型：

- Error
- InternalError
- EvalError
- RangeError
- ReferenceError
- SyntaxError
- TypeError
- URIError

Error 是基类型，其他错误类型继承该类型。因此，所有错误类型都共享相同的属性（所有错误对象上的方法都是这个默认类型定义的方法）。浏览器很少会抛出Error 类型的错误，该类型主要用于开发者抛出自定义错误。

InternalError 类型的错误会在底层JavaScript 引擎抛出异常时由浏览器抛出。例如，递归过多导致了栈溢出。这个类型并不是代码中通常要处理的错误，如果真发生了这种错误，很可能代码哪里弄错了或者有危险了。

EvalError 类型的错误会在使用eval()函数发生异常时抛出。ECMA-262 规定，“如果eval 属性没有被直接调用（ 即没有将其名称作为一个Identifier ， 也就是CallExpression 中的MemberExpression），或者如果eval 属性被赋值”，就会抛出该错误。基本上，只要不把eval()当成函数调用就会报告该错误：

```javascript
new eval(); // 抛出EvalError
eval = foo; // 抛出EvalError
```

实践中，浏览器不会总抛出EvalError。Firefox 和IE 在上面第一种情况下抛出TypeError，在
第二种情况下抛出EvalError。为此，再加上代码中不大可能这样使用eval()，因此几乎遇不到这种错误。

RangeError 错误会在数值越界时抛出。例如，定义数组时如果设置了并不支持的长度，如-20 或Number.MAX_VALUE，就会报告该错误：

```javascript
let items1 = new Array(-20); // 抛出RangeError
let items2 = new Array(Number.MAX_VALUE); // 抛出RangeError
```

RangeError 在JavaScript 中发生得不多。

ReferenceError 会在找不到对象时发生。（这就是著名的"object expected"浏览器错误的原
因。）这种错误经常是由访问不存在的变量而导致的，比如：

```javascript
let obj = x; // 在x 没有声明时会抛出ReferenceError
SyntaxError 经常在给eval()传入的字符串包含JavaScript 语法错误时发生，比如：
eval("a ++ b"); // 抛出SyntaxError
```

在eval()外部，很少会用到SyntaxError。这是因为JavaScript 代码中的语法错误会导致代码无法执行。

TypeError 在JavaScript 中很常见，主要发生在变量不是预期类型，或者访问不存在的方法时。很多原因可能导致这种错误，尤其是在使用类型特定的操作而变量类型不对时。下面是几个例子：

```javascript
let o = new 10; // 抛出TypeError
console.log("name" in true); // 抛出TypeError
Function.prototype.toString.call("name"); // 抛出TypeError
```

在给函数传参数之前没有验证其类型的情况下，类型错误频繁发生。

最后一种错误类型是URIError，只会在使用encodeURI()或decodeURI()但传入了格式错误的URI 时发生。这个错误恐怕是JavaScript 中难得一见的错误了，因为上面这两个函数非常稳健。

不同的错误类型可用于为异常提供更多信息，以便实现适当的错误处理逻辑。在try/catch 语句的catch 块中，可以使用instanceof 操作符确定错误的类型，比如：

```javascript
try {
someFunction();
} catch (error){
if (error instanceof TypeError){
// 处理类型错误
} else if (error instanceof ReferenceError){
// 处理引用错误
} else {
// 处理所有其他类型的错误
}
}
```

检查错误类型是以跨浏览器方式确定适当操作过程的最简单方法，因为message 属性中包含的错误消息因浏览器而异。

**3.  try/catch 的用法**

当try/catch 中发生错误时，浏览器会认为错误被处理了，因此就不会再使用本章前面提到的机
制报告错误。如果应用程序的用户不懂技术，那么他们即使看到错误也看不懂，这是一个理想的结果。使用try/catch 可以针对特定错误类型实现自定义的错误处理。

try/catch 语句最好用在自己无法控制的错误上。例如，假设你的代码中使用了一个大型JavaScript库的某个函数，而该函数可能会有意或由于出错而抛出错误。因为不能修改这个库的代码，所以为防止这个函数报告错误，就有必要通过try/catch 语句把该函数调用包装起来，对可能的错误进行处理。

如果你明确知道自己的代码会发生某种错误，那么就不适合使用try/catch 语句。例如，如果给
函数传入字符串而不是数值时就会失败，就应该检查该函数的参数类型并采取相应的操作。这种情况下，没有必要使用try/catch 语句。

### 抛出错误

与try/catch 语句对应的一个机制是throw 操作符，用于在任何时候抛出自定义错误。throw 操
作符必须有一个值，但值的类型不限。下面这些代码都是有效的：

```javascript
throw 12345;
throw "Hello world!";
throw true;
throw { name: "JavaScript" };
```

使用throw 操作符时，代码立即停止执行，除非try/catch 语句捕获了抛出的值。
可以通过内置的错误类型来模拟浏览器错误。每种错误类型的构造函数都只接收一个参数，就是错误消息。下面看一个例子：

```javascript
throw new Error("Something bad happened.");
```
以上代码使用一个自定义的错误消息生成了一个通用错误。浏览器会像处理自己生成的错误一样来处理这个自定义错误。换句话说，浏览器会像通常一样报告这个错误，最终显示这个自定义错误。当然，使用特定的错误类型也是一样的，如以下代码所示：

```javascript
throw new SyntaxError("I don't like your syntax.");
throw new InternalError("I can't do that, Dave.");
throw new TypeError("What type of variable do you take me for?");
throw new RangeError("Sorry, you just don't have the range.");
throw new EvalError("That doesn't evaluate.");
throw new URIError("Uri, is that you?");
throw new ReferenceError("You didn't cite your references properly.");
```

自定义错误常用的错误类型是Error、RangeError、ReferenceError 和TypeError。

此外，通过继承Error（第6 章介绍过继承）也可以创建自定义的错误类型。创建自定义错误类型时，需要提供name 属性和message 属性，比如：

```javascript
class CustomError extends Error {
constructor(message) {
super(message);
this.name = "CustomError";
this.message = message;
}
}
throw new CustomError("My message");
```


继承Error 的自定义错误类型会被浏览器当成其他内置错误类型。自定义错误类型有助于在捕获
错误时更准确地区分错误。

**1.  何时抛出错误** 

抛出自定义错误是解释函数为什么失败的有效方式。在出现已知函数无法正确执行的情况时就应该抛出错误。换句话说，浏览器会在给定条件下执行该函数时抛出错误。例如，下面的函数会在参数不是数组时抛出错误：

```javascript
function process(values){
values.sort();
for (let value of values){
if (value > 100){
return value;
}
}
return -1;
}
```

如果给这个函数传入字符串，调用sort()函数就会失败。每种浏览器对此都会给出一个模棱两可的错误消息，如下所示。

- IE：属性或方法不存在。
- Firefox：values.sort()不是函数。
- Safari：值undefined（对表达式values.sort 求值的结果）不是一个对象。
- Chrome：对象名没有方法'sort'。
- Opera：类型不匹配（通常是在需要对象时使用了非对象值）。

虽然Firefox、Chrome 和Safari 至少给出了导致错误的相关代码，但并没有哪个错误消息特别明确地指出发生了什么，或者怎么修复。对于上面的一个函数来说，通过这样的错误消息调试还是很容易的。但是，如果是一个复杂的Web 应用程序，有几千行JavaScript 代码，想要找到错误的原因就会很难。

这时候，使用适当的信息创建自定义错误可以有效提高代码的可维护性。比如下面的例子：

```javascript
function process(values){
if (!(values instanceof Array)){
throw new Error("process(): Argument must be an array.");
}
values.sort();
for (let value of values){
if (value > 100){
return value;
}
}
return -1;
}
```

在这个重写后的函数中，如果values 参数不是数组就会抛出错误。错误消息包含函数名以及对错误原因非常清晰的描述。即使在复杂的应用程序中出现这个错误，也可以很容易理解问题所在。

实际编写JavaScript 代码时，应该仔细评估每个函数，以及可能导致它们失败的情形。良好的错误处理协议可以保证只会发生你自己抛出的错误。

**2.  抛出错误与try/catch**

一个常见的问题是何时抛出错误，何时使用try/catch 捕获错误。一般来说，错误要在应用程序
架构的底层抛出，在这个层面上，人们对正在进行的流程知之甚少，因此无法真正地处理错误。如果你在编写一个可能用于很多应用程序的JavaScript 库，或者一个会在应用程序的很多地方用到的实用函数，那么应该认真考虑抛出带有详细信息的错误。然后捕获和处理错误交给应用程序就行了。

至于抛出错误与捕获错误的区别，可以这样想：应该只在确切知道接下来该做什么的时候捕获错误。捕获错误的目的是阻止浏览器以其默认方式响应；抛出错误的目的是为错误提供有关其发生原因的说明。

### error 事件

任何没有被try/catch 语句处理的错误都会在window 对象上触发error 事件。该事件是浏览器
早期支持的事件，为保持向后兼容，很多浏览器保持了其格式不变。在onerror 事件处理程序中，任何浏览器都不会传入event 对象。相反，会传入3 个参数：错误消息、发生错误的URL 和行号。大多数情况下，只有错误消息有用，因为URL 就是当前文档的地址，而行号可能指嵌入JavaScript 或外部文件中的代码。另外，onerror 事件处理程序需要使用DOM Level 0 技术来指定，因为它不遵循DOM Level 2 Events标准格式：

```javascript
window.onerror = (message, url, line) => {
console.log(message);
};
```

在任何错误发生时，无论是否是浏览器生成的，都会触发error 事件并执行这个事件处理程序。
然后，浏览器的默认行为就会生效，像往常一样显示这条错误消息。可以返回false 来阻止浏览器默认报告错误的行为，如下所示：

```javascript
window.onerror = (message, url, line) => {
console.log(message);
return false;
};
```

通过返回false，这个函数实际上就变成了整个文档的try/catch 语句，可以捕获所有未处理的运行时错误。这个事件处理程序应该是处理浏览器报告错误的最后一道防线。理想情况下，最好永远不要用到。适当使用try/catch 语句意味着不会有错误到达浏览器这个层次，因此也就不会触发error事件。

注意：浏览器在使用这个事件处理错误时存在明显差异。在 IE 中发生 error 事件时，正常代码会继续执行，所有变量和数据会保持，且可以在 onerror 事件处理程序中访问。然而在 Firefox 中，正常代码会执行会终止，错误发生之前的所有变量和数据会被销毁，导致很难真正分析处理错误。

图片也支持error 事件。任何时候，如果图片src 属性中的URL 没有返回可识别的图片格式，就会触发error 事件。这个事件遵循DOM格式，返回一个以图片为目标的event 对象。下面是个例子：

```javascript
const image = new Image();
image.addEventListener("load", (event) => {
console.log("Image loaded!");
});
image.addEventListener("error", (event) => {
console.log("Image not loaded!");
});
image.src = "doesnotexist.gif"; // 不存在，资源会加载失败
```

在这个例子中，图片加载失败后会显示一个alert 警告框。这里的关键在于，当error 事件发生时，图片下载过程已结束，不会再恢复。

### 错误处理策略

过去，Web 应用程序的错误处理策略基本上是在服务器上落地。错误处理策略涉及很多错误和错误处理考量，包括日志记录和监控系统。这些主要是为了分析模式，以期找到问题的根源并了解有多少用户会受错误影响。

在Web 应用程序的JavaScipt 层面落地错误处理策略同样重要。因为任何JavaScript 错误都可能导致网页无法使用，所以理解这些错误会在什么情况下发生以及为什么会发生非常重要。绝大多数Web 应用程序的用户不懂技术，在碰到页面出问题时通常会迷惑。为解决问题，他们可能会尝试刷新页面，也可能会直接放弃。作为开发者，应该非常清楚自己的代码在什么情况下会失败，以及失败会导致什么结果。另外，还要有一个系统跟踪这些问题。

### 识别错误

类型的，不会验证函数参数，所以很多错误只有在代码真正运行起来时才会出现。通常，需要注意3 类
错误：

- 类型转换错误
- 数据类型错误
- 通信错误

上面这几种错误会在特定情况下，在没有对值进行充分检测时发生。

**1.  静态代码分析器**

不得不说的是，通过在代码构建流程中添加静态代码分析或代码检查器（linter），可以预先发现非常多的错误。这样的代码分析工具有很多，详见GitHub Gist 网站All Gists 页面。常用的静态分析工具是JSHint、JSLint、Google Closure 和TypeScript。

静态代码分析器要求使用类型、函数签名及其他指令来注解JavaScript，以此描述程序如何在基本可执行代码之外运行。分析器会比较注解和JavaScript 代码的各个部分，对在实际运行时可能出现的潜在不兼容问题给出提醒。

### 区分重大与非重大错误

### 把错误记录到服务器中

## 调试技术

### 把消息记录到控制台

### 理解控制台运行时

### 使用JavaScript调试器

### 在页面中打印消息

### 补充控制台方法

### 抛出错误

## 旧版IE的常见错误

### 无效字符

### 未找到成员

### 未知运行时错误

### 语法错误

### 系统找不到指定资源

## 小结