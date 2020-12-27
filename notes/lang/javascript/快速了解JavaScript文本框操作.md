HTML 中使用 `<input>` 元素表示单行输入框和 `<textarea>` 元素表示多行文本框。

HTML中使用的 `<input>` 元素在 JavaScript 中对应的是 `HTMLInputElement` 类型。`HTMLInputElement` 继承自 `HTMLElement` 接口：

```javascript
interface HTMLInputElement extends HTMLElement {
    ...
}
```

`HTMLInputElement` 类型有一些独有的属性和方法：

- `name`：表示 `<input>` 元素的名称。
- `disabled`：表示 `<input>` 元素是否禁止使用。在表单提交时不会包含被禁止的 `<input>` 元素。
- `autofocus`：表示页面加载时，该元素是否会自动获得焦点。
- `required`：表示表单提交时，该 `<input>`元素是否为必填。
- `value`：表示该 `<input>` 元素的值。
- `validity`：返回一个 `ValidityState` 对象，表示 `<input>` 元素的校验状态。是只读属性。
- `validationMessage`：表示 `<input>` 元素校验失败时，用户看到的报错信息。如果该元素不需要校验，或者通过校验，该属性为空字符串。是只读属性。
- `willValidate`：表示表单提交时，`<input>` 元素是否会被校验。是只读属性。
- `select()`：选中 `<input>` 元素内部的所有文本。
- `click()`：模拟鼠标点击当前的 `<input>` 元素。

而在上述介绍 `HTMLInputElement` 类型中的属性时，`type` 属性要特别关注一下，因为根据 `type` 属性的改变，可以改变`<input>`的属性。

|类型|描述|
|:---:|:---:|
|text|文本输入|
|password|密码输入|
|submit|表单数据提交|
|button|按钮|
|radio|单选框|
|checkbox|复选框|
|file|文件|
|hidden|隐藏的字段|
|image|定义图像作为提交按钮|
|reset|重置按钮|

省略 `type` 属性与 `type="text"`效果一样， `<input>` 元素显示为文本框。

当 `type` 的值为`text/password/number/`时，会有以下属性对 `<input>` 元素有效。

|属性|类型|描述|
|:---:|:---:|:---:|
|autocomplete|string|字符串on或off，表示`<input>`元素的输入内容可以被浏览器自动补全。|
|maxLength|long|指定`<input>`元素允许的最多字符数。|
|size|unsigned long|表示`<input>`元素的宽度，这个宽度是以字符数来计量的。|
|pattern|string|表示`<input>`元素的值应该满足的正则表达式|
|placeholder|string|表示`<input>`元素的占位符，作为对元素的提示。|
|readOnly|boolean|表示用户是否可以修改`<input>`的值。|
|min|string|表示`<input>`元素的最小数值或日期。|
|max|string|表示`<input>`元素的最大数值或日期。|
|selectionStart|unsigned long|表示选中文本的起始位置。如果没有选中文本，返回光标在`<input>`元素内部的位置。|
|selectionEnd|unsigned long|表示选中文本的结束位置。如果没有选中文本，返回光标在`<input>`元素内部的位置。|
|selectionDirection|string|表示选中文本的方向。可能的值包括`forward`、`backward`、`none`。|

下面创建一个 `type="text"` ，一次显示 25 个字符，但最多允许显示 50 个字符的文本框：

```html
<input type="text" size="25" maxlength="50" value="initial value">
```

HTML 使用的 `<textarea>` 元素在 JavaScript 中对应的是 `HTMLTextAreaElement` 类型。`HTMLTextAreaElement`类型继承自 `HTMLElement` 接口：

```javascript
interface HTMLTextAreaElement extends HTMLElement {
    ...
}
```

`HTMLTextAreaElement` 类型有一些独有的属性和方法：

- `form`：只读属性，返回对父表单元素的引用。如果此元素未包含在表单元素中，则它可以是同一文档中 `id` 属性同 `form` 值的 `<form>` 元素，如果没有，该值为 `null`。
- `type`：只读属性，返回字符串`textarea`。
- `value`：`string`类型，返回/设置控件中包含的原始值。
- `textLength`：只读属性，`long`类型，返回`<textarea>`元素的`value`值的长度。与 `value.length` 相同。
- `defaultValue`：`string`类型，返回/设置`textarea`元素的默认值。
- `placeholder`：`string`类型，返回/设置 `textarea` 元素的占位符，作为对输入内容的提示。
- `rows`：`unsigned long`类型，返回/设置`textarea`元素的可见文本行数。
- `cols`：`unsigned long`类型，返回/设置 `textarea` 元素的可见宽度。
- `autofocus`：`boolean`类型，返回/设置`textarea`元素在页面加载时自动获取焦点。
- `name`：返回/设置 `textarea` 元素的名称。
- `disabled`：`boolean`类型，返回/设置 `textarea` 元素是否应当被禁用。
- `HTMLTextAreaElement.labels`：`NodeList`类型，返回与此`select`元素关联的标签元素列表。
- `maxLength`：`long`类型，返回/设置元素可以输入的最大字符数。仅在值更改时才评估此约束。
- `minLength`：`long`类型，返回/设置元素可以输入的最小字符数。仅在值更改时才评估此约束。
- `accessKey`：`boolean`类型，返回/设置访问 `textarea` 的键盘快捷键。
- `readOnly`：`boolean`类型，返回/设置 `textarea` 元素是否为只读。
- `required`：`boolean` 类型，返回/设置 `textarea` 元素是否为必填项。
- `tabIndex`：`long`类型，返回/设置 `textarea` 元素的 `tab` 键导航顺序中元素的位置。

下面创建一个高度为 25，宽度为 5 的 `<textarea>` 多行文本框。它与 `<input>` 不同的是，初始值显示在 `<textarea>...</textarea>` 之间：

```html
<textarea rows="25" cols="5">initial value</textarea>
```

注意：处理文本框值的时候最好不要使用 `DOM` 方法，而应该使用 `value` 属性。

# 选择文本

`<input>` 与 `<textarea>` 都支持 `select()` 方法，该方法用于选中文本框中的所有内容。该方法的语法为：

```javascript
select(): void
```

下面看一个示例：

```javascript
let textbox = document.forms[0].elements["input-box"];
textbox.select();
```

也可以在文本框获得焦点时，选中文本框的内容：

```javascript
textbox.addEventListener("focus", (event) => {
    event.target.select();
});
```

### select 事件

当选中文本框中的文本或使用 `select()` 方法时，会触发 `select` 事件。

```javascript
let textbox = document.forms[0].elements["textbox1"];
textbox.addEventListener("select", (event) => {
    console.log(`Text selected: ${textbox.value}`);
});
```

### 取得选中的文本

HTML5 对 `select` 事件进行了扩展，通过 `selectionStart` 和 `selectionEnd` 属性获取文本选区的起点偏移量和终点偏移量。如下所示：

```javascript
function getSelectedText(textbox){
    return textbox.value.substring(textbox.selectionStart,
textbox.selectionEnd);
}
```

注意：在 IE8 及更早版本不支持这两个属性。

### 选中部分文本

HTML5 提供了 `setSelectionRange()` 方法用于选中部分文本：

```javascript
setSelectionRange(start, end, direction): void;
```

- `start`：被选中的第一个字符的位置索引，从 0 开始。如果这个值比元素的 `value` 长度还大，则会被看做 `value` 最后一个位置的索引。
- `end`：被选中的最后一个字符的下一个位置索引。如果这个值比元素的 `value` 长度还大，则会被看做 `value` 最后一个位置的索引。
- `direction`：该参数是可选的字符串类型，表示选择方向，可以传入 `“forward”/“backward”/“none”` 三个值。

下面看一个例子：

```html
<input type="text" id="text-sample" size="20" value="Hello World!">
<button onclick="selectText()">选中部分文本</button>
<script>
    function selectText() {
        let input = document.getElementById("text-sample");
        input.focus();
        input.setSelectionRange(4, 8);    // o Wo
    }
</script>
```

如果想要看到选中效果，必须让文本框获得焦点。

# 输入过滤 

不同文本框经常需要保证输入特定类型或格式的数据，或许数据需要包含特定字符或必须匹配某个特定模式。而文本框并未提供验证功能，因此要配合 JavaScript 脚本实现输入过滤功能。

### 屏蔽字符

有些输入框需要出现或不出现特定字符。如果想要将输入框变成只读的，只需要使用 `preventDefault()`方法将按键都屏蔽：

```javascript
input.addEventListener("keypress", (event) => {
    event.preventDefault();
});
```

而要屏蔽特定字符，就需要检查事件的 `charCode` 属性。如下所示，使用正则表达式实现只允许输入数字的输入框：

```javascript
input.addEventListener("keypress", (event) => {
    if (!/\d/.test(event.key)) {
        event.preventDefault();
    }
});
```

还有一个问题需要处理：复制、粘贴及涉及Ctrl 键的其他功能。在除IE 外的所有浏览器中，前面代码会屏蔽快捷键Ctrl+C、Ctrl+V 及其他使用Ctrl 的组合键。因此，最后一项检测是确保没有按下Ctrl键，如下面的例子所示：

```javascript
textbox.addEventListener("keypress", (event) => {
if (!/\d/.test(String.fromCharCode(event.charCode)) &&
event.charCode > 9 &&
!event.ctrlKey){
event.preventDefault();
}
});
```

最后这个改动可以确保所有默认的文本框行为不受影响。这个技术可以用来自定义是否允许在文本框中输入某些字符。

### 处理剪贴板

IE 是第一个实现了剪切板相关的事件以及通过JavaScript访问剪切板数据的浏览器，其它浏览器在后来也都支持了相同的事件和剪切板的访问，后来 HTML5 将其纳入了规范。以下是与剪切板相关的 6 个事件：

- `beforecopy`：发生复制操作前触发。
- `copy`：发生复制操作时触发。
- `beforecut`：发生剪切操作前触发。
- `cut`：发生剪切操作时触发。
- `beforepaste`：发生粘贴操作前触发。
- `paste`：发生粘贴操作时触发。

剪切板事件的行为及相关对象会因浏览器而异。在 Safari、Chrome 和 Firefox 中，`beforecopy`、`beforecut` 和 `beforepaste` 事件只会在显示文本框的上下文菜单时触发，但 IE 不仅在这种情况下触发，也会在 `copy`、`cut` 和 `paste` 事件在所有浏览器中都会按预期触发。

在实际的事件发生之前，通过`beforecopy`、`beforecut` 和 `beforepaste` 事件可以在向剪贴板发送或从中检索数据前修改数据。不过，取消这些事件并不会取消剪贴板操作。要阻止实际的剪贴板操作，必须取消 `copy`、`cut`和 `paste` 事件。

剪贴板的数据通过 `clipboardData` 对象来获取，且`clipboardData` 对象提供 3 个操作数据的方法：

- `getData(format)`：从剪贴板获取指定格式的数据，接收的参数为指定获取的数据格式，如果没有指定数据格式或指定数据格式不存在，则此方法返回一个空字符串。
- `setData(format, data)`：给剪贴板赋予指定格式的数据，第一个参数指定数据格式，第二个参数为第一个参数赋予数据。如果指定数据格式不存在，则将其添加到数据存储的末尾；如果指定数据格式存在，则将数据替换在指定数据格式的位置。
- `clearData(format)`：删除指定格式的数据，接收的参数为指定要删除的数据格式，如果指定格式不存在，则此方法不执行任何操作；如果此参数为空字符串或未提供，则将删除所有格式的数据。

而 `clipboardData` 对象在 IE 中使用 `window` 获取，在 Firefox、Safari 和 Chrome 中使用 `event` 获取。为防止未经授权访问剪贴板，只能在剪贴板事件期间访问 `clipboardData` 对象；IE 会在任何时候都暴露 `clipboardData` 对象。因此，要兼容两者，最好在剪贴板事件期间使用该对象。

```javascript
function getClipboardText(event){
    var clipboardData = (event.clipboardData || window.clipboardData);
    return clipboardData.getData("text");
}
function setClipboardText (event, value){
    if (event.clipboardData){
        return event.clipboardData.setData("text/plain", value);
    } else if (window.clipboardData){
        return window.clipboardData.setData("text", value);
    }
}
```

如果文本框只有数字，那剪贴时，就需要使用`paste`事件检查剪贴板上的文本是否无效。如果无效，可以取消默认行为：

```javascript
input.addEventListener("paste", (event) => {
    let text = getClipboardText(event);
    if (!/^\d*$/.test(text)){
        event.preventDefault();
    }
});
```

注意：Firefox、Safari和Chrome只允许在`onpaste`事件中访问`getData()`方法。

# 自动切换

在 JavaScript 中，可以用在当前字段完成时自动切换到下一个字段的方式来增强表单字段的易用性。比如，常用手机号分为国家好加手机号。因此，我们设置 2 个文本框：

```html
<form>
    <input type="text" name="phone1" id="phone-id-1" maxlength="4">
    <input type="text" name="phone2" id="phone-id-2" maxlength="11">
</form>
```

当文本框输入到最大允许字符数后，就把焦点移到下一个文本框，这样可以增加表单的易用性并加速数据输入。如下所示：

```html
<script>
    function tabForward(event){
        let target = event.target;
        if (target.value.length == target.maxLength){
            let form = target.form;
            for (let i = 0, len = form.elements.length; i < len; i++) {
                if (form.elements[i] == target) {
                    if (form.elements[i+1]) {
                        form.elements[i+1].focus();
                    }
                    return;
                }
            }
        }
    }
    let inputIds = ["phone-id-1", "phone-id-2"];
    for (let id of inputIds) {
        let textbox = document.getElementById(id);
        textbox.addEventListener("keyup", tabForward);
    }
</script>
```

这里，`tabForward()` 函数通过比较用户输入文本的长度与 `maxLength` 属性的值来检测输入是否达到了最大长度。如果两者相等，就通过循环表中的元素集合找到当前文本框，并把焦点设置到下一个元素。

注意：上面的代码只适用于之前既定的标记，没有考虑可能存在的隐藏字段。

# HTML5

HTML5 新增了一些表单提交前，浏览器会基于指定的规则进行验证，并在出错时显示适当的错误信息。而验证会基于某些条件应用到表单字段中。

### 必填字段

表单字段中添加 `required` 属性，用于标注该字段是必填项，不填则无法提交。该属性适用于`<input>`、`<textarea>`和`<select>`。如下所示：

```html
<input type="text" name="account" required>
```

也可以通过 JavaScript 检测对应元素的 `required` 属性来判断表单字段是否为必填项：

```javascript
let isRequired = document.forms[0].elements["account"].required;
```

也可以检测浏览器是否支持 `required` 属性：

```javascript
let isRequiredSupported = "required" in document.createElement("input");
```

<small>注意：不同浏览器处理必填字段的机制不同。Firefox、Chrome、IE 和Opera 会阻止表单提交并在相应字段下面显示有帮助信息的弹框，而Safari 什么也不做，也不会阻止提交表单。</small>

### 更多输入类型

HTML5 为 `<input>` 元素增加了几个新的 `type` 值。如下所示：

|类型|描述|
|:---:|:---:|
|number|数字值的输入|
|date|日期输入|
|color|颜色输入|
|range|一定范围内的值的输入|
|month|允许用户选择月份和年份|
|week|允许用户选择周和年份|
|time|允许用户选择时间（无时区）|
|datetime|允许用户选择日期和时间（有时区）|
|datetime-local|允许用户选择日期和时间（无时区）|
|email|电子邮件地址的输入|
|search|搜索（表现类似常规文本）|
|tel|电话号码的输入|
|url|URL地址的输入|

这些输入表名字段应该输入的数据类型，并且提供了默认验证。如下所示：

```html
<input type="email" name="email">
<input type="url" name="homepage">
```

要检测浏览器是否支持新类型，可以在 JavaScript 中创建 `<input>` 并设置 `type` 属性，之后读取它即可。老版本中会将我只类型设置为 `text`，而支持的会返回正确的值。如下所示：

```javascript
let input = document.createElement("input");
input.type = "email";
let isEmailSupported = (input.type == "email");
```

### 数值范围

而上面介绍的几个如 `number/range/datetime/datetime-local/date/month/week/time` 几个填写数字的类型，都可以指定 `min/max/step` 等几个与数值有关的属性。`step` 属性用于规定合法数字间隔，如 `step="2"`，则合法数字应该为 0、2、4、6，依次类推。如下所示：

```html
<input type="number" min="0" max="100" step="5" name="count">
```

上面的例子是`<input>`中只能输入从 0 到 100 中 5 的倍数。

也可以使用 `stepUp()` 和 `stepDown()` 方法对 `<input>` 元素中的值进行加减，它俩会接收一个可选参数，用于表示加减的数值。如下所示：

```javascript
input.stepUp(); // 加1
input.stepUp(5); // 加5
input.stepDown(); // 减1
input.stepDown(10); // 减10
```

### 输入模式

HTML5 还为文本添加了 `pattern` 属性，用于指定一个正则表达式。这样就可以自己设置 `<input>` 元素的输入模式了。如下所示：

```html
<input type="text" pattern="\d+" name="count">
```

注意模式的开头和末尾分别假设有`^`和`$`。这意味着输入内容必须从头到尾都严格与模式匹配。

与新增的输入类型一样，指定 `pattern` 属性也不会阻止用户输入无效内容。模式会应用到值，然后浏览器会知道值是否有效。通过访问 `pattern` 属性可以读取模式：

```javascript
let pattern = document.forms[0].elements["count"].pattern;
```

使用如下代码可以检测浏览器是否支持pattern 属性：

```javascript
let isPatternSupported = "pattern" in document.createElement("input");
```

### 检测有效性

HTML5 新增了 `checkValidity()` 方法，用来检测表单中任意给定字段是否有效。而判断的条件是约束条件，因此必填字段如果没有值会被视为无效，字段值不匹配 `pattern` 属性也会被视为无效。如下所示：

```javascript
if (document.forms[0].elements[0].checkValidity()){
    // 字段有效，继续
} else {
    // 字段无效
}
```

要检查整个表单是否有效，可以直接在表单上调用`checkValidity()`方法。这个方法会在所有字段都有效时返回`true`，有一个字段无效就会返回`false`：

```javascript
if(document.forms[0].checkValidity()){
    // 表单有效，继续
} else {
    // 表单无效
}
```

`validity` 属性会返回一个`ValidityState` 对象，表示 `<input>` 元素的校验状态。返回的对象包含一些列的布尔值的属性：

- `customError`：如果设置了 `setCustomValidity()` 就返回 true，否则返回false。
- `patternMismatch`：如果字段值不匹配指定的 `pattern` 属性则返回true。
- `rangeOverflow`：如果字段值大于 `max` 的值则返回true。
- `rangeUnderflow`：如果字段值小于 `min` 的值则返回true。
- `stepMisMatch`：如果字段值与 `min`、`max` 和 `step` 的值不相符则返回true。
- `tooLong`：如果字段值的长度超过了 `maxlength` 属性指定的值则返回true。
- `typeMismatch`：如果字段值不是 `"email"` 或`"url"` 要求的格式则返回true。
- `valid`：如果其他所有属性的值都为false 则返回true。与`checkValidity()`的条件一致。
- `valueMissing`：如果字段是必填的但没有值则返回true。

因此，通过 `validity` 属性可以检查表单字段的有效性，从而获取更具体的信息，如下所示：

```javascript
if (input.validity && !input.validity.valid){
    if (input.validity.valueMissing){
        console.log("请指定值.")
    } else if (input.validity.typeMismatch){
        console.log("请指定电子邮件地址.");
    } else {
        console.log("值无效.");
    }
}
```

### 禁用验证

通过指定 `novalidate` 属性可以禁止对表单进行任何验证：

```html
<form method="post" action="/signup" novalidate>
    <!-- 表单元素 -->
</form>
```

也可以在 JavaScript 通过 `noValidate` 属性设置，为 `true` 表示属性存在，为 `false` 表示属性不存在：

```javascript
document.forms[0].noValidate = true; // 关闭验证
```

如果一个表单中有多个提交按钮，那么可以给特定的提交按钮添加`formnovalidate` 属性，指定通过该按钮无需验证即可提交表单：

```html
<form method="post" action="/foo">
    <!-- 表单元素 -->
    <input type="submit" value="注册提交">
    <input type="submit" formnovalidate name="btnNoValidate"
value="没有验证的提交按钮">
</form>
```

也可以使用 JavaScript 设置 `formNoValidate` 属性：

```javascript
// 关闭验证
document.forms[0].elements["btnNoValidate"].formNoValidate = true;
```

# 总结

以上总结了 `<input>` 和 `<textarea>` 两个元素的一些功能，主要是 `<input>` 元素可以通过设置 `type` 属性获取不同类型的输入框，可以通过监听键盘事件并检测要插入的字符来控制文本框的内容。

还有一些与剪贴板相关的事件，并对剪贴的内容进行检测。还介绍了一些 HTML5 新增的属性和方法和新增的更多的 `<input>` 元素的类型，和一些与验证相关的属性和方法。