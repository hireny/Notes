# 前言

在 HTML 中使用 `<form>` 表单元素在 JavaScript 中对应的是 `HTMLFormElement` 类型，而`HTMLFormElement`继承了`HTMLElement`接口：

```javascript
interface HTMLFormElement extends HTMLElement {
    ...
}
```

因此，`HTMLFormElement` 除了与 `HTMLElement`有一样的属性和方法外，还有自己独有的属性和方法：

- `acceptCharset`：服务器可以接收的字符集，等价于 HTML 的 accept-charset 属性。
- `action`：请求的 URL，等价于 HTML 的 `action` 属性。
- `elements`：表单中所有控件的 `HTMLCollection`。
- `enctype`：请求的编码类型，等价于 HTML 的 enctype 属性。
- `length`：表单中控件的数量。
- `method`：HTTP 请求的方法类型，通常是 `get` 或 `post`，等价于 HTML 的 `method` 属性。
- `name`：表单的名字，等价于 HTML 的 `name` 属性。
- `target`：用于发送请求和接收响应的窗口的名字，等价于 HTML 的 `target` 属性。
- `reset()`：把表单字段重置为各自的默认值，如果没有默认值，则为空。
- `submit()`：提交表单。


如果想要获取 `<form>` 表单元素的引用，需要使用 `DOM` 选择器进行查找，如 `getElementById/getElementsByName/getElementsByClassName/getElementsByTagName/querySelector/querySelectorAll` 这些方法，下面使用 `getElementById()` 方法获取表单，只要给表单设置一个 `id` 属性即可：

```javascript
let form = document.getElementById("form-id");
```

`Document`中的`<form>`是`document.forms`的成员，因此可以通过索引或表单的 `name` 来访问特定的表单。如下所示：

```javascript
// 文档中第一个元素
let firstForm = document.forms[0];
// 取得名字为"form-name"的表单
let formName = document.forms["form-name"];
```

<small>注意：表单可以同时拥有`id`和`name`，且两者可以不相同。</small>

# 提交表单

一般情况下，`<form>`表单元素是通过客户端用户点击的方式发送表单数据。而定义提交按钮主要有：

- 定义使用 `type=“submit”` 属性值的 `<input>` 元素，如 `<input type="submit" value="提交">`。
- 定义使用 `type="submit"` 属性值的 `<button>` 元素，如 `<button type="submit">提交</button>`。
- 定义使用 `type="image"` 属性值的 `<input>` 元素，如 `<input type="image" src="submit.gif">`。

这种方式提交表单会在请求之前触发 `submit` 事件。这就提供了一个验证表单数据的机会，可以根据验证结果来决定表单是否要提交。如下所示：

```javascript
let form = document.getElementById("form-id");
form.addEventListener("submit", (event) => {
    // 验证表单
    if (...) {
        // 验证失败后，就要阻止表单提交
        event.preventDefault();
        return;
    } 
    // 这里用于验证成功后将表单提交
});
```

调用 `preventDefault()` 方法可以阻止表单提交。通常，在表单数据无效以及不应该发送到服务器可以这样处理。

也可以调用`submit()` 方法提交表单，表单中不存在提交按钮也不影响提交。如下所示：

```javascript
let form = document.querySelector("#form-id");
// 提交表单
form.submit();
```

通过 `submit()` 方法提交的表单，`submit` 事件不会触发。因此在调用这个方法前要先做数据验证。

表单提交的最大一个问题是可能会提交两次。如果提交表单后没有反应，那么没有耐心的用户可能会多次点击提交按钮。因此，解决这种方法主要有两种：表单提交后禁用按钮，或者通过 `onsubmit` 事件取消之后的表单提交。

# 重置表单

一般用户填写完表单信息后，想要重新填写时，可以使用重置按钮来重置表单。而定义重置按钮主要有：

- 定义使用 `type="reset"` 属性值的 `<input>`元素，如 `<input type="reset" value="重置">`。
- 定义使用 `type="reset"` 属性值的 `<button>` 元素，如 `<button type="reset">重置</button>`

重置成功后，表单字段的值会重置为默认值；如果没有默认值，则会为空。

重置按钮可以触发 `reset` 事件。这个事件为取消重置提供了机会。如下所示：

```javascript
let form = document.getElementById("form-id");
form.addEventListener("reset", (event) => {
    event.preventDefault();
});
```

重置按钮也可以调用 `reset()` 方法完成重置操作：

```javascript
let form = document.getElementById("form-id");
// 重置表单
form.reset();
```

但 `reset()`方法会触发 `reset` 事件 。

<small>注意：表单设计中通常不提倡重置表单。</small>

# 表单字段

表单元素可以像页面中的其它元素一样使用原生 `DOM` 方法来访问。此外，调用 `elements` 属性为表单的有序列表，包含表单中所有标签元素的引用，包括`<input>`、`<textarea>`、`<button>`、`<select>`和`<fieldset>`元素，并按照在 HTML 中出现的次序保存，通过索引和`name`属性进行访问。如下所示：

```javascript
let form = document.getElementById("form-id");
// 取得表单中的第一个字段
let field1 = form.elements[0];
// 取得表单中名为"textbox-name"的字段
let field2 = form.elements["textbox-name"];
// 取得字段的数量
let fieldCount = form.elements.length;
```

如果多个表单控件使用同一个 `name` 值，比如 `<input>`元素设置`type="radio"`属性为单选，就会返回包含所有同名元素的 `HTMLCollection`。如下所示：

```html
<form method="post" id="form-id">
    <ul>
        <li><input type="radio" name="sex" value="男">男</li>
        <li><input type="radio" name="sex" value="女">女</li>
        <li><input type="radio" name="sex" value="未知">未知</li>
    </ul>
</form>
```

因此，访问 `elements["sex"]` 返回的 `NodeList` 就包含着 3 个元素；而使用索引访问时，就会返回当前位置的元素。如下所示：

```javascript
let form = document.getElementById("form-id");
let sexes = form.elements["sex"];
console.log(sexes.length); // 3
let index_one = sexes[1];
let form_one = form.elements[1];
console.log(index_one === form_one); // true
```

### 公共属性

除了 `<fieldset>` 元素以外，所有表单字段都有一组同样的属性。由于 `<input>` 类型可以表示多种表单字段，因此某些属性只适用于特定类型的字段。除此之外的属性可以在任何表单字段上使用。以下列出了这些表单字段的公共属性和方法。

- `disabled`：布尔值，表示表单字段是否禁用。
- `form`：指针，指向表单字段所属的表单。这个属性是只读的。
- `name`：字符串，这个字段的名字。
- `readOnly`：布尔值，表示这个字段是否只读。
- `tabIndex`：数值，表示这个字段在按Tab 键时的切换顺序。
- `type`：字符串，表示字段类型，如"checkbox"、"radio"等。
- `value`：要提交给服务器的字段值。对文件输入字段来说，这个属性是只读的，仅包含计算机上某个文件的路径。


JavaScript 可以动态修改任何属性，当然，`form`属性除外。如下所示：

```javascript
let form = document.getElementById("form-id");
let field = form.elements[0];
// 修改字段的值
field.value = "修改后的值";
// 检查字段所属的表单
console.log(field.form === form); // true
// 给字段设置焦点
field.focus();
// 禁用字段
field.disabled = true;
// 改变字段的类型（不推荐，但对<input>来说是可能的）
field.type = "checkbox";
```

动态修改表单字段属性的能力为修改表单提供了方便。如当点击两次提交按钮时，会在第一次提交后，通过监听`submit`事件来实现禁用提交按钮。如下所示：

```javascript
// 避免多次提交表单的代码
let form = document.getElementById("form-id");
form.addEventListener("submit", (event) => {
let target = event.target;
// 取得提交按钮
let btn = target.elements["submit-btn"];
// 禁用提交按钮
btn.disabled = true;
});
```

以上代码，通过监听 `submit` 事件，并在函数中获取按钮并设置`disabled=true`，来达到提交按钮禁用。注意：这个功能不能通过直接给提交按钮添加 `onclick` 事件进行实现，原因是不同浏览器触发事件的时机不一样。有些浏览器会在触发表单的 `submit` 事件前先触发 `onclick` 事件，有些浏览器会后触发 `onclick` 事件。对于先触发 `onclick` 事件的浏览器，这个按钮会在表单提交前被禁用，这意味着表单不会被提交。因此，最好使用表单的 `submit` 事件进行禁用提交按钮。但这种方式不适用于没有提交按钮的表单提交，因为，只有提交按钮才能触发 `submit` 事件。

`type` 属性可以用于除`<fieldset>`之外的任何表单字段。对于`<input>`元素，这个值等于 HTML 的 `type` 属性值。对于其他元素，这个 `type` 属性的值按照下表设置。

|描述|示例HTML|类型的值|
|:---:|:---:|:---:|
|单选列表|`<select>...</select>`|"select-one"|
|多项列表|`<select multiple>...</select>`|"select-multiple"|
|自定义按钮|`<button>...</button>`|"submit"|
|自定义非提交按钮|`<button type="button">...</button>`|"button"|
|自定义重置按钮|`<button type="reset">...</button>`|"reset"|
|自定义提交按钮|`<button type="submit">...</button>`|"submit"|

对于`<input>`和`<button>`元素，可以动态修改其 `type` 属性。但`<select>`元素的 `type` 属性是只读的。

### 公共方法

每个表单字段都有两个公共方法：

- `focus()`：把浏览器焦点设置到表单字段，该字段会变成活动字段并可以响应键盘事件。
- `blur()`：从元素上移出焦点，焦点不会转移到任何特定元素，仅仅是从调用这个方法的元素上移除。


给一个 `focus()` 方法的例子：

```javascript
window.addEventListener("load", (event) => {
    document.forms[0].elements[0].focus();
});
```

上述情况就是在页面加载后，把焦点定位到表单中的第一个字段。注意：如果表单中第一个字段是`type="hidden"`属性的`<input>`元素，或者该字段被 CSS 属性 `display` 或 `visibility` 隐藏，以上代码就会出错。

在 HTML5 中，表单字段增加了 `autofocus` 属性，支持的浏览器会自动为带有该属性的元素设置焦点，无须使用 JavaScript。如下所示：

```html
<input type="text" autofocus>
```

为了让之前的代码在使用`autofocus`时也能正常工作，必须先检测元素上是否设置了该属性。如果设置了 `autofocus`，就不再调用 `focus()`：

```javascript
window.addEventListener("load", (event) => {
    let element = document.forms[0].elements[0];
    if (element.autofocus !== true) {
        element.focus();
    }
});
```

<small>注意：默认情况下只能给表单元素设置焦点。不过，通过将 `tabIndex` 属性设置为`–1` 再调用`focus()`，也可以给任意元素设置焦点。</small>

再给一个 `blur()` 的例子：

```javascript
document.forms[0].elements[0].blur();
```

在浏览器支持 `readOnly` 属性之前，Web 开发者通常会使用这个方法创建只读字段。现在很少有需要调用`blur()`的了。

### 公共事件

除了鼠标、键盘、变化和HTML 事件外，所有字段还支持以下 3 个事件：

- `blur`：字段失去焦点时，会触发该事件。
- `change`：`<input>`和`<textarea>`元素的 `value` 发生变化且失去焦点时触发，或者在`<select>`元素中选中项发生变化时触发。
- `focus`：在字段获得焦点时触发。

`blur` 和 `focus` 事件是用户手动改变焦点或调用`blur/focus`方法时触发。`change`事件是因控件不同而改变触发时机。

- `<input>` 和 `<textarea>`：`change`事件会在字段失去焦点，同时 `value` 自控件获得焦点后发生改变时触发。
- `<select>`：`change` 事件会在用户改变选中项时触发，不需要控件失去焦点。

`focus` 和 `blur` 事件通常用于改变用户界面，提供可见的提示或额外功能；`change`事件通常用于验证用户在字段中输入的内容。比如，有的文本框可能只限于接收数值，`focus` 事件用来改变控件的背景颜色以便更清楚地表明当前字段获得了焦点，`blur` 事件用于去掉这个背景颜色，`change` 事件用于在用户输入了非数值时把背景颜色改为红色。如下所示：

```javascript
let textbox = document.forms[0].elements[0];
textbox.addEventListener("focus", (event) => {
    let target = event.target;
    if (target.style.backgroundColor != "red") {
        target.style.backgroundColor = "yellow";
    }
});
textbox.addEventListener("blur", (event) => {
    let target = event.target;
    target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
});
textbox.addEventListener("change", (event) => {
    let target = event.target;
    target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
});
```

上述代码中，`onfocus` 事件会把文本框的背景改为黄色，表明是当前活动字段。`onblur` 和 `onchange` 事件会在发现非数值字符时把背景改为红色。为测试非数值字符，这里使用了简单的正则表达式来检测文本框的`value`。这个功能必须同时在 `onblur` 和 `onchange` 事件上实现，确保无论文本框是否改变都能执行验证。

<small>注意：`blur` 和 `change` 事件的关系并没有明确定义。在某些浏览器中，`blur`事件会先于 `change` 事件触发；在其它浏览器中，触发顺序会相反。因此不能依赖这两个事件触发的顺序，必须区分时要多加注意。</small>


# 总结

HTML 和 ECMAScript 都更新了好几个版本，但是表单几乎从来没有改变。但是，通过 JavaScript 可以增加现有表单字段以提供新功能或增强易用性。也因此，表单字段也暴露了属性、方法和事件供 JavaScript 调用使用。