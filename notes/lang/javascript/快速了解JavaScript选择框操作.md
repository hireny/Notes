选择框是使用 `<select>` 和 `<option>` 元素创建的。为方便交互，HTMLSelectElement 类型在所有表单字段的公共能力之外又提供了以下属性和方法。

- `add(newOption, relOption)`：在 `relOption` 之前向控件中添加新的 `<option>`。
- multiple：布尔值，表示是否允许多选，等价于HTML 的multiple 属性。
- options：控件中所有`<option>`元素的HTMLCollection。
- remove(index)：移除给定位置的选项。
- selectedIndex：选中项基于0 的索引值，如果没有选中项则为–1。对于允许多选的列表，始终是第一个选项的索引。
- size：选择框中可见的行数，等价于HTML 的size 属性。

选择框的type 属性可能是"select-one"或"select-multiple"，具体取决于multiple 属性是否存在。当前选中项根据以下规则决定选择框的value 属性。

-  如果没有选中项，则选择框的值是空字符串。
- 如果有一个选中项，且其value 属性有值，则选择框的值就是选中项value 属性的值。即使value 属性的值是空字符串也是如此。
- 如果有一个选中项，且其value 属性没有指定值，则选择框的值是该项的文本内容。
- 如果有多个选中项，则选择框的值根据前两条规则取得第一个选中项的值。

来看下面的选择框：

```html
<select name="location" id="selLocation">
<option value="Sunnyvale, CA">Sunnyvale</option>
<option value="Los Angeles, CA">Los Angeles</option>
<option value="Mountain View, CA">Mountain View</option>
<option value="">China</option>
<option>Australia</option>
</select>
```

如果选中这个选择框中的第一项，则字段的值就是"Sunnyvale, CA"。如果文本为"China"的项被选中，则字段的值是一个空字符串，因为该项的value 属性是空字符串。如果选中最后一项，那么字段的值是"Australia"，因为该`<option>`元素没有指定value 属性。

每个`<option>`元素在DOM 中都由一个HTMLOptionElement 对象表示。HTMLOptionElement类型为方便数据存取添加了以下属性。

- index：选项在options 集合中的索引。
- label：选项的标签，等价于HTML 的label 属性。
- selected：布尔值，表示是否选中了当前选项。把这个属性设置为true 会选中当前选项。
- text：选项的文本。
- value：选项的值（等价于HTML 的value 属性）。

大多数`<option>`属性是为了方便存取选项数据。可以使用常规DOM 功能存取这些信息，只是效率比较低，如下面的例子所示：

```javascript
let selectbox = document.forms[0].elements["location"];

// 不推荐
let text = selectbox.options[0].firstChild.nodeValue; // 选项文本
let value = selectbox.options[0].getAttribute("value"); // 选项值
```

以上代码使用标准的DOM技术获取了选择框中第一个选项的文本和值。下面再比较一下使用特殊选项属性的代码：

```javascript
let selectbox = document.forms[0].elements["location"];
// 推荐
let text = selectbox.options[0].text; // 选项文本
let value = selectbox.options[0].value; // 选项值
```

在操作选项时，最好使用特定于选项的属性，因为这些属性得到了跨浏览器的良好支持。在操作DOM 节点时，与表单控制实际的交互可能会因浏览器而异。不推荐使用标准DOM 技术修改`<option>`元素的文本和值。

最后强调一下，选择框的change 事件与其他表单字段是不一样的。其他表单字段会在自己的值改变后触发change 事件，然后字段失去焦点。而选择框会在选中一项时立即触发change 事件。

注意：不同浏览器返回的 value 属性可能会有差异。JavaScript 中的 value 属性始终等于 HTML 中的 value 睡醒。但在 HTML 中没有指定 value属性的情况下，IE8 及早期版本会返回空字符串，而 IE9 及之后版本、Safari、Firefox、Chrome 和 Opera 会返回 text 相同的值。

# 选项处理

对于只允许选择一项的选择框，获取选项最简单的方式是使用选择框的selectedIndex 属性，如
下面的例子所示：

```javascript
let selectedOption = selectbox.options[selectbox.selectedIndex];
```

这样可以获取关于选项的所有信息，比如：

```javascript
let selectedIndex = selectbox.selectedIndex;
let selectedOption = selectbox.options[selectedIndex];
console.log(`Selected index: ${selectedIndex}\n` +
`Selected text: ${selectedOption.text}\n` +
`Selected value: ${selectedOption.value}`);
```

以上代码打印出了选中项的索引及其文本和值。

对于允许多选的选择框，selectedIndex 属性就像只允许选择一项一样。设置selectedIndex会移除所有选项，只选择指定的项，而获取selectedIndex 只会返回选中的第一项的索引。

选项还可以通过取得选项的引用并将其selected 属性设置为true 来选中。例如，以下代码会选
中选择框中的第一项：

```javascript
selectbox.options[0].selected = true;
```

与selectedIndex 不同，设置选项的selected 属性不会在多选时移除其他选项，从而可以动态
选择任意多个选项。如果修改单选框中选项的selected 属性，则其他选项会被移除。要注意的是，把selected 属性设置为false 对单选框没有影响。

通过selected 属性可以确定选择框中哪个选项被选中。要取得所有选中项，需要循环选项集合逐一检测selected 属性，比如：

```javascript
function getSelectedOptions(selectbox){
let result = new Array();
for (let option of selectbox.options) {
if (option.selected) {
result.push(option);
}
}
return result;
}
```

这个函数会返回给定选择框中所有选中项的数组。首先创建一个包含结果的数组，然后通过for 循环迭代所有选项，检测每个选项的selected 属性。如果选项被选中，就将其添加到result 数组。最后是返回选中项数组。这个getSelectedOptions()函数可以用于获取选中项的信息，比如：

```javascript
let selectbox = document.getElementById("selLocation");
let selectedOptions = getSelectedOptions(selectbox);
let message = "";
for (let option of selectedOptions) {
message += 'Selected index: ${option.index}\n' +
'Selected text: ${option.text}\n' +
'Selected value: ${option.value}\n'
}
console.log(message);
```

这个例子先检索了一个选择框的所有选中项。然后通过for 循环构建包含所有选中项信息的字符
串，包括每项的索引、文本和值。以上代码既适用于单选框也适用于多选框。

# 添加选项

可以使用JavaScript 动态创建选项并将它们添加到选择框。首先，可以使用DOM方法，如下所示：

```javascript
let newOption = document.createElement("option");
newOption.appendChild(document.createTextNode("Option text"));
newOption.setAttribute("value", "Option value");
selectbox.appendChild(newOption);
```

以上代码创建了一个新的`<option>`元素，使用文本节点添加文本，设置其value 属性，然后将其添加到选择框。添加到选择框之后，新选项会立即显示出来。

另外，也可以使用Option 构造函数创建新选项，这个构造函数是DOM出现之前就已经得到浏览器支持的。Option 构造函数接收两个参数：text 和value，其中value 是可选的。虽然这个构造函数通常会创建Object 的实例，但DOM 合规的浏览器都会返回一个`<option>`元素。这意味着仍然可以使用appendChild()方法把这样创建的选项添加到选择框。比如下面的例子：

```javascript
let newOption = new Option("Option text", "Option value");
selectbox.appendChild(newOption); // 在IE8 及更低版本中有问题
```

这个方法在除IE8 及更低版本之外的所有浏览器中都没有问题。由于实现问题，IE8 及更低版本在这种情况下不能正确设置新选项的文本。

另一种添加新选项的方式是使用选择框的add()方法。DOM 规定这个方法接收两个参数：要添加的新选项和要添加到其前面的参考选项。如果想在列表末尾添加选项，那么第二个参数应该是null。IE8 及更早版本对add()方法的实现稍有不同，其第二个参数是可选的，如果要传入则必须是一个索引值，表示要在其前面添加新选项的选项。DOM 合规的浏览器要求必须传入第二个参数，因此在跨浏览器方法中不能只使用一个参数（IE9 是符合DOM规范的）。此时，传入undefined 作为第二个参数可以保证在所有浏览器中都将选项添加到列表末尾。下面是一个例子：

```javascript
let newOption = new Option("Option text", "Option value");
selectbox.add(newOption, undefined); // 最佳方案
```

以上代码可以在所有版本的IE 及DOM 合规的浏览器中使用。如果不想在最后插入新选项，则应该使用DOM 技术和insertBefore()。

注意：跟在 HTML 中一样，选项的值不是必需的。Option 构造函数也可以只接收一个参数（选项的文本）。

# 移除选项

与添加选项类似，移除选项的方法也不止一种。第一种方式是使用DOM 的removeChild()方法并传入要移除的选项，比如：

```javascript
selectbox.removeChild(selectbox.options[0]); // 移除第一项
```

第二种方式是使用选择框的remove()方法。这个方法接收一个参数，即要移除选项的索引，比如：

```javascript
selectbox.remove(0);    // 移除第一项
```

最后一种方式是直接将选项设置为等于null。这同样也是DOM 之前浏览器实现的方式。下面是一个例子：

```javascript
selectbox.options[0] = null; // 移除第一项
```

要清除选择框的所有选项，需要迭代所有选项并逐一移除它们，如下面例子所示：

```javascript
function clearSelectbox(selectbox) {
for (let option of selectbox.options) {
selectbox.remove(0);
}
}
```

这个函数可以逐一移除选择框中的每一项。因为移除第一项会自动将所有选项向前移一位，所以这样就可以移除所有选项。

# 移动和重排选项

在DOM 之前，从一个选择框向另一个选择框移动选项是非常麻烦的，要先从第一个选择框移除选项，然后以相同文本和值创建新选项，再将新选项添加到第二个选择框。DOM 方法则可以直接将某个选项从第一个选择框移动到第二个选择框，只要对相应选项使用appendChild()方法即可。如果给这个方法传入文档中已有的元素，则该元素会先从其父元素中移除，然后再插入指定位置。例如，下面的代码会从选择框中移除第一项并插入另一个选择框：

```javascript
let selectbox1 = document.getElementById("selLocations1");
let selectbox2 = document.getElementById("selLocations2");
selectbox2.appendChild(selectbox1.options[0]);
```

移动选项和移除选项都会导致每个选项的index 属性重置。

重排选项非常类似，DOM 方法同样是最佳途径。要将选项移动到选择框中的特定位置，insertBefore()方法是最合适的。不过，要把选项移动到最后，还是appendChild()方法比较方便。

下面的代码演示了将一个选项在选择框中前移一个位置：

```javascript
let optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove,
selectbox.options[optionToMove.index-1]);
```

这个例子首先获得要移动选项的索引，然后将其插入之前位于它前面的选项之前，其中第二行代码适用于除第一个选项之外的所有选项。下面的代码则可以将选项向下移动一个位置：

```javascript
let optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove,
selectbox.options[optionToMove.index+2]);
```

以上代码适用于选择框中的所有选项，包括最后一个。

# 总结

选择框是经常使用 JavaScript 来控制的一种表单控件。借助 DOM，操作选择框比以前方便了很多。使用标准的 DOM 技术，可以为选择框添加或移除选项，也可以将选项从一个选择框移动到另一个选择框，或者重排选项。