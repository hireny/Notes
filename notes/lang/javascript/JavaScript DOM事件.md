# 概述

JavaScript 与 HTML 之间的交互是通过事件实现的。事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间。可以使用侦听器（或处理程序）来预定事件，以便事件发生时执行相应的代码。这种在传统软件工程中被称为观察员模式的模型，支持页面的行为（JavaScript 代码）与页面的外观（HTML 和 CSS 代码）之间的松耦合。

# 事件流

**事件流** 描述的是从页面中接收事件的顺序。

## DOM事件流

DOM2 Events 规范规定事件流分为 3 个阶段：事件捕获、到达目标和事件冒泡。事件捕获最先发生，为提前拦截事件提供了可能。然后，实际的目标元素接收到事件。最后一个阶段是冒泡，最迟要在这个阶段响应事件。

# 事件处理程序

事件意味着用户或浏览器执行的某种动作。比如，单击（click）、加载（load）、鼠标悬停（mouseover）。为响应事件而调用的函数被称为事件处理程序（或事件监听器）。

## HTML事件处理程序

特定元素支持的每个事件都可以使用事件处理程序的名字以 HTML 属性的形式来指定。此时属性的值必须是能够执行的 JavaScript 代码。

在 HTML 中定义的事件处理程序可以包含精确的动作指令，也可以调用在页面其他地方定义的脚本：

## DOM0事件处理程序

在JavaScript 中指定事件处理程序的传统方式是把一个函数赋值给（DOM 元素的）一个事件处理程序属性。这也是在第四代Web 浏览器中开始支持的事件处理程序赋值方法，直到现在所有现代浏览器仍然都支持此方法，主要原因是简单。要使用JavaScript 指定事件处理程序，必须先取得要操作对象的引用。

每个元素（包括 window 和 document）都有通常小写的事件处理程序属性，比如 onclick。只要把这个属性赋值为一个函数即可：

```javascript
let btn = document.getElementById("myBtn");
btn.onclick = function () {
    console.log("Clicked");
}
```

像这个使用 DOM0 方式为事件处理程序赋值时，所赋函数被视为元素的方法。因此，事件处理程序会在元素的作用域中运行，即 this 等于元素。

通过将事件处理程序属性的值设置为 null，可以移除通过 DOM0 方式添加的事件处理程序，如下面的例子所示：

```javascript
btn.onclick = null;	// 移除事件处理程序
```

## DOM2事件处理程序

DOM2 Events 为事件处理程序的赋值和移除定义了两个方法：`addEventListener()` 和 `removeEventListener()`。这两个方法暴露在所有 DOM 节点上，它们接收 3 个参数：事件名、事件处理函数和一个布尔值，true表示在捕获阶段调用事件处理程序，false（默认值）表示在冒泡阶段调用事件处理程序。

仍以给按钮添加 click 事件处理程序为例，可以这样写：

```javascript
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
    console.log(this.id);
}, false);
```

与 DOM0 方法类似，这个时间处理程序同样在被附加的元素的作用域中运行。使用 DOM2 方式的主要优势是可以为同一个事件添加多个事件处理程序。来看下面的例子：

```javascript
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
    console.log(this.id);
}, false);
btn.addEventListener("click", () => {
    console.log("Hello world!");
}, false);
```

通过 `addEventListener()` 添加的事件处理程序只能使用 `removeEventListener()` 并传入与添加时同样的参数来移除。这意味着使用 `addEventListener()` 添加的匿名函数无法移除，如下面的例子所示：

```javascript
let btn = document.getElementById("myBtn");
btn.addEventListener("click", () => {
    console.log(this.id);
}, false);
// 其他代码
btn.removeEventListener("click", function() { // 没有效果！
    console.log(this.id);
}, false);
```

这个例子通过 `addEventListener()` 添加了一个匿名函数作为事件处理程序。然后，又以看起来相同的参数调用了 `removeEventListener()`。但实际上，第二个参数与传给 `addEventListener()` 的完全不是一回事。传给 `removeEventListener()` 的事件处理函数必须与传给 `addEventListener()` 的是同一个，如下面的例子所示：

```javascript
let btn = document.getElementById("myBtn");
let handler = function() {
    console.log(this.id);
};
btn.addEventListener("click", handler, false);
// 其他代码
btn.removeEventListener("click", handler, false); // 有效果！
```

这个例子有效，因为调用 `addEventListener()` 和 `removeEventListener()` 时传入的是同一个函数。

大多数情况下，事件处理程序会被添加到事件流的冒泡阶段，主要原因是跨浏览器兼容性好。把事件处理程序注册到捕获阶段通常用于在事件到达其指定目标之前拦截事件。如果不需要拦截，则不要使用事件捕获。

## IE事件处理程序

IE 实现了与DOM 类似的方法，即attachEvent()和detachEvent()。这两个方法接收两个同样的参数：事件处理程序的名字和事件处理函数。因为IE8 及更早版本只支持事件冒泡，所以使用attachEvent()添加的事件处理程序会添加到冒泡阶段。

要使用attachEvent()给按钮添加click 事件处理程序，可以使用以下代码：

```javascript
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function() {
    console.log("Clicked");
});
```

注意， attachEvent() 的第一个参数是 `"onclick"`，而不是 DOM 的 `addEventListener()` 方法的 `"click"`。

在IE 中使用attachEvent()与使用DOM0 方式的主要区别是事件处理程序的作用域。使用DOM0方式时，事件处理程序中的this 值等于目标元素。而使用attachEvent()时，事件处理程序是在全局作用域中运行的，因此this 等于window。来看下面使用attachEvent()的例子：

## 跨浏览器事件处理程序

为了以跨浏览器兼容的方式处理事件，很多开发者会选择使用一个JavaScript 库，其中抽象了不同浏览器的差异。有些开发者也可能会自己编写代码，以便使用最合适的事件处理手段。自己编写跨浏览器事件处理代码也很简单，主要依赖能力检测。要确保事件处理代码具有最大兼容性，只需要让代码在冒泡阶段运行即可。

为此，需要先创建一个addHandler()方法。这个方法的任务是根据需要分别使用DOM0 方式、DOM2 方式或IE 方式来添加事件处理程序。这个方法会在EventUtil 对象（本章示例使用的对象）上添加一个方法，以实现跨浏览器事件处理。添加的这个addHandler()方法接收3 个参数：目标元素、事件名和事件处理函数。

有了addHandler()，还要写一个也接收同样的3 个参数的removeHandler()。这个方法的任务是移除之前添加的事件处理程序，不管是通过何种方式添加的，默认为DOM0 方式。

以下就是包含这两个方法的EventUtil 对象：

```javascript

```

# 事件对象

在 DOM 中发生事件时，所有相关信息都会被手机并存储在一个名为 `event` 的对象中。这个对象包含了一些基本信息，比如导致事件的元素、发生的事件类型，以及可能与特定事件相关的任何其它数据。例如，鼠标操作导致的事件会生成鼠标位置信息，而键盘操作导致的事件会生成与被按下的键有关的信息。所有浏览器都支持这个 `event` 对象，尽管支持方式不同。

## DOM 事件对象

在 DOM 合规的浏览器中，`event` 对象是传给事件处理程序的唯一参数。不管以哪种方式（DOM0 或 DOM2）指定事件处理程序，都会传入这个 `event` 对象。下面的例子展示了在两种方式下都可以使用事件对象：

```javascript
let btn = document.getElementById("myBtn");
btn.onclick = function(event) {
    console.log(event.type); // "click"
};
btn.addEventListener("click", (event) => {
    console.log(event.type); // "click"
}, false);
```

> 注意：event 对象只在事件处理程序执行期间存在，一旦执行完毕，就会被销毁。

## IE 事件对象

与DOM 事件对象不同， IE 事件对象可以基于事件处理程序被指定的方式以不同方式来访问。如果事件处理程序是使用DOM0 方式指定的，则event 对象只是window 对象的一个属性，如下所示：

## 跨浏览器事件对象

虽然 DOM 和 IE 的事件对象并不相同，但它们有足够的相似性可以实现跨浏览器方案。DOM 事件对象中包含 IE 事件对象的所有信息和能力，只是形式不同。这些共性可让两种事件模型之间的映射成为可能。

# 事件类型

Web 浏览器中可以发生很多事件。如前所示，所发生事件的类型决定了事件对象中会保存什么信息。DOM3 Events 定义了如下事件类型。

- 用户界面事件（UIEvent）：涉及与 BOM 交互的通用浏览器事件。
- 焦点事件（FocusEvent）：在元素获得和失去焦点时触发。
- 鼠标事件（MouseEvent）：使用鼠标在页面上执行某些操作时触发。
- 滚轮事件（WheelEvent）：使用鼠标滚轮（或类似设备）时触发。
- 输入事件（InputEvent）：向文档中输入文本时触发。
- 键盘事件（KeyboardEvent）：使用键盘在页面上执行某些操作时触发。
- 合成事件（CompositionEvent）：在使用某种 IME（Input Method Editor，输入法编辑器）输入字符时触发。

除了这些事件类型之外，HTML5 还定义了另一组事件，而浏览器通常在 DOM 和 BOM 上实现专有事件。这些专有事件基本上都是根据开发者需求而不是按照规范增加的，因此不同浏览器的实现可能不同。

DOM3 Events 在 DOM2 Events 基础上重新定义了事件，并增加了新的事件类型。所有主流浏览器都支持 DOM2 Events 和 DOM3 Events。

### 用户界面事件

用户界面事件或 UI 事件不一定跟用户操作有关。这类事件在 DOM 规范出现之前就已经以某种形式存在了，保留它们是为了向后兼容。UI 事件主要有以下几种。



# 内存与性能

# 模拟事件

# 小结
