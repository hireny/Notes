在HTML中使用 `<select>` 和 `<option>` 元素创建选择框。而 `<select>` 元素对应的是 `HTMLSelectElement` 接口，`<option>` 元素对应的是 `HTMLOptionElement` 接口。这两个接口都是通过 `HTMLElement` 接口从其他 HTML 元素共享所有属性和方法。

先从 `<select>` 元素对应的 `HTMLSelectElement` 接口开始介绍专属的属性和方法。

- `autofocus`：返回 `boolean` 值，表示控件在页面加载时是否应该具有输入焦点，除非用户覆盖它。等价于 `<select>` 元素中的 `autofocus` 属性。
- `disabled`： 返回 `boolean` 值，表示控件是否被禁用。等价于 `<select>` 元素中的 `disabled` 属性。
- `form`：返回 `HTMLFormElement` 值的只读属性，表示与此元素关联的表单。
- `labels`：与此元素关联的 `<label>` 元素组成的 `NodeList`。
- `length`：返回 `number` 值，表示 `<select>` 元素中 `<option>` 元素的数量。
- `multiple`：返回`boolean` 值，表示是否允许多选，等价于`<select>` 元素中的 `multiple` 属性。
- `name`：返回 `string` 值，表示此控件的名称。等价于 `<select>` 元素中的 `name` 属性。
- `options`：返回 `HTMLOptionsCollection` 值的可读属性，表示此元素包含的 `<option>` 元素集。
- `required`：返回 `boolean` 值，表示用户在提交表单之前是否需要选择值。等价于 `<select>` 元素中的 `required` 属性。
- `selectedIndex`：返回 `number` 值，表示第一个选定的 `<option>` 元素的索引。值为 `-1` 表示未选择任何元素，对于允许多选的列表，始终是第一个选项的索引。
- `selectedOptions`：返回 `HTMLCollection` 值的可读属性，表示所选的 `<option>` 的元素集。
- `size`：返回 `number` 值，表示控件中可见项的数量。等价于 `<select>` 元素的 `size` 属性。默认值为 `1`，如果 `multiple` 为 `true` ，则为 `4`。
- `type`：返回 `string` 值的只读属性，表示表单控件类型。当 `multiple` 为 `true` 时，它返回 `"select-multiple"`，否则返回 `"select-one"`。
- `validationMessage`：返回 `string` 值的只读属性，表示一个本地化消息，描述了控件不满足的验证约束。如果`willvalidate=false`，或者它满足其约束，则此属性为空字符串。
- `validity`：返回  `ValidityState` 的只读属性，表示该控件所处的有效状态。
- `value`：返回 `string` 值，表示表单控件的值。返回 `option` 元素的 `value` 属性，如果没有该属性，则返回 `text` 属性。
- `willValidate`：返回 `boolean` 值的只读属性，表示该按钮是否是约束验证的候选者。如果任何条件禁止约束验证，则为 `false`。
- `add(elemen, before)`：将 `element` 元素添加到 `before` 元素之前。`element` 参数为要添加的 `<option>` 元素， `before` 参数为已存在的 `<option>` 元素，`before` 为可选参数。
- `checkValidity()`：检查元素是否有任何约束以及它是否满足它们。如果元素未通过约束，浏览器将在元素处触发可取消的 `invalid` 事件并返回 `false`。
- `item(index)`：从 `<select>`元素的 `options` 集合中获取一个 `<option>` 元素。
- `namedItem(name)`：获取具有指定名称的 `options` 集合中的 `<option>` 元素。而传入的 `name` 参数可以匹配 `<option>` 元素的 `id` 或 `name` 属性。
- `remove(index)`：从 `<select>` 元素的 `options` 集合中删除指定索引处的元素。
- `reportValidity()`：此方法向用户报告元素约束的问题。如果有问题，它会在元素处触发可取消的 `invalid` 事件，并返回 `false`；如果没有问题，则返回 `true`。
- `setCustomValidity(error)`：将 `<select>` 元素的自定义有效性消息设置为指定的消息。使用空字符串表示元素不具有自定义有效性错误。

下面介绍一下`<select>` 元素根据不同选中状态下的`value`属性的值：

```html
<select name="location" id="selLocation">
    <option value="Sunnyvale, CA">Sunnyvale</option>
    <option value="Los Angeles, CA">Los Angeles</option>
    <option value="Mountain View, CA">Mountain View</option>
    <option value="">China</option>
    <option>Australia</option>
</select>
```

当选中选项框中的第一项时，`<select>` 元素的 `value` 值为 `"Sunnyvale, CA"`；而选中第四项时，`<select>` 元素的 `value` 值为 `""`，因为该项的 `value` 属性是空字符串；选中最后一项，则 `value` 值为 `"Australia"`，因为该 `<option>` 元素没有指定 `value` 属性。

因此，根据以上的例子，`<select>` 元素的`value`属性根据以下规则获取值：

- 无选中项，`<select>` 元素的 `value` 属性为空字符串。
- 有一个选中项，且其 `value` 属性有值，则 `<select>` 元素的 `value` 属性的值为 `<option>` 元素的 `value` 属性的值。
- 有一个选中项，且其 `value` 属性没有指定值，则 `<select>` 元素的 `value` 属性的值是 `<option>` 元素的 `text` 值。
- 有多个选中项，则 `<select>` 元素的值会获取第一个 `<option>` 元素的`value`值或 `text` 值。

再介绍 `<option>` 元素对应的 `HTMLOptionElement` 接口专属属性和方法。

- `defaultSelected`：返回 `boolean` 值。表示默认情况下是否被选中。
- `disabled`：返回 `boolean` 值。表示该选项不可用，等价于 `<option>` 元素的 `disabled` 属性的值。
- `form`：返回 `HTMLFormElement` 值的只读属性。如果 `<option>` 是 `<select>` 元素的后代，表示与相应 `<select>` 元素的 `form` 值相同的值，如果不是，则为 `null`。
- `index`：返回 `string` 的只读属性。表示选项在 `options` 集合中的索引。
- `label`：返回 `string` 的只读属性。表示 `<option>` 元素的标签，等价于 `<option>` 元素的 `label` 属性。如果未设置此属性，则读取该属性将返回元素的文本内容。
- `selected`：返回 `boolean` 值，表示当前是否选择了该选项。
- `text`：返回 `string` 值。表示选项的文本值。
- `value`：返回 `string` 值。表示选项的值。等价于 `<option>` 元素的 `value` 属性。

<small>
这里强调一下，`<select>` 元素的 `change` 事件与其它表单字段是不一样的。其它表单字段会在自己的值改变后触发 `change` 事件，然后字段失去焦点。而 `<select>` 会在选中一项时立即触发 `change` 事件。
</small>

# 选项处理

对于只允许选择一项的 `<select>` 元素，获取选项最简单的方式是使用 `<select>` 元素的 `selectIndex` 属性，如下面的例子：

```javascript
let selectedOption = selectbox.options[selectbox.selectedIndex];
```

获取到 `<option>` 元素后，就可以根据 `<option>` 元素的属性和方法获取想要的信息。

对于允许多选的 `<select>` 元素，`selectedIndex` 属性就像只允许选择一项一样。设置 `selectedIndex` 会移除所有选项，只选择指定的项，而获取 `selectedIndex` 只会返回选中的第一项的索引。

选项还可以通过取得选项的引用并将其 `selected` 属性设置为 `true` 来选中。例如，以下代码会选中 `<select>` 的第一项：

```javascript
selectbox.options[0].selected = true;
```

与 `selectedIndex` 不同，设置选项的 `selected` 属性不会在多选时移除其他选项，从而可以动态选择任意多个选项。如果修改单选框中选项的 `selected` 属性，则其他选项会被移除。要注意的是，把 `selected` 属性设置为 `false` 对单选框没有影响。

通过 `selected` 属性可以确定选择框中哪个选项被选中。要取得所有选中项，需要循环选项集合逐一检测 `selected` 属性，比如：

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

# 添加选项

可以使用 JavaScript 动态创建选项并将它们添加到选择框。首先，可以使用 `DOM` 方法，如下所示：

```javascript
let newOption = document.createElement("option");
newOption.appendChild(document.createTextNode("Option text"));
newOption.setAttribute("value", "Option value");
selectbox.appendChild(newOption);
```

以上代码创建了一个新的`<option>`元素，使用文本节点添加文本，设置其 `value` 属性，然后将其添加到选择框。添加到选择框之后，新选项会立即显示出来。

浏览器原生提供 `Option()` 构造函数创建 `HTMLOptionElement` 实例：

```javascript
new Option(text, value, defaultSelected, selected) : HTMLOptionElement
```

- `text`：一个可选的 `string` 值参数，表示该选项的文本内容。如果省略，返回空字符串。
- `value`：一个可选的 `string` 值参数，表示该选项的值。如果省略，默认返回 `text` 属性的值。
- `defaultSelected`：一个可选的 `boolean` 值参数，表示该项是否默认选中，默认为 `false`。注意，即使设为 `true`，也不代表该项的 `selected` 属性为 `true`。
- `selected`：一个可选的 `boolean` 值参数，表示该项是否选中。默认为 `false`。

来看案例：

```javascript
let newOption = new Option("Option Text", "Option Value", true);
```

创建之后，需要将该实例添加到 HTML 的 `<select>` 元素中，这里有两种添加方法：

- `DOM` 操作中的 `appendChild()` 方法。
- `HTMLSelectElement` 接口中的 `add()` 方法。

这里详细介绍下 `add()` 方法：

```javascript
add(element: HTMLOptionElement | HTMLOptGroupElement, before?: HTMLElement | number | null): void;
```

- `element`：一个 `HTMLOptionElement` 或 `HTMLOptGroupElement` 元素的参数。
- `before`：可选的参数，是集合中的一个元素或者类型为 `number` 的索引，表示在 `element` 参数之前插入。如果传入的是 `null` 或索引不存在，新元素会添加在集合的末尾。

如果想要符合所有浏览器的规范，可以传入 `undefined` 作为第二个参数。

```javascript
let newOption = new Option("Option text", "Option value", true);
selectbox.add(newOption, undefined);
```

<small>这里注意一下 `defaultSelected` 和 `selected` 两个参数，有时候容易弄混。`selected` 属性为 `true` 时，该 `<option>` 当前状态处于已选择状态。`defaultSelected` 属性为 `true` 时，表示 `<option>` 在默认情况下为已选择状态，但不代表 `<option>` 的当前状态是已选择状态。当页面重置时，`selected` 属性值为 `true` 的 `<option>` 可能会变成未选择状态，而 `defaultSelected` 属性值为 `true` 的 `<option>` 则一定会变成已选择状态。</small>

# 移除选项

移除 `<option>` 元素的方法也不止一种，下面列举的方法都可以实现：

- `DOM` 操作中的 `removeChild()` 方法。
- `HTMLSelectElement` 接口中的 `remove()` 方法。传入的参数为要移除的 `<option>` 元素的索引。
- 直接将指定 `<option>` 元素赋值为 `null`。如 `selects.options[1] = null`。



要清除选择框的所有选项，需要迭代所有选项并逐一移除它们，如下面例子所示：

```javascript
function clearSelectbox(selectbox) {
    for (let index = 0; index < selectbox.options.length;) {
        selectbox.remove(0);
    }
}
```

# 移动和重排选项

使用 `DOM` 操作中的 `appendChild()` 方法实现从一个 `<select>` 元素中将 `<option>` 移到另一个 `<select>` 元素中，这种实现方法会将 `<option>` 元素先从其父元素中移除，然后再插入指定位置。如下所示：

```javascript
let selectbox1 = document.getElementById("selLocations1");
let selectbox2 = document.getElementById("selLocations2");
selecbox2.appendChild(selectbox1.options[0]);
```

移动选项和移除选项都会导致每个 `<option>` 的 `index` 属性重置。

重排 `<option>` 的话，使用 `DOM` 操作中的 `insertBefore()` 方法，而移到最后位置，还是使用 `appendChild()` 方法较为方便。

下面的代码演示了将一个 `<option>` 在 `<select>` 中前移一个位置：

```javascript
let optToMove = selectbox.options[1];
selectbox.insertBefore(optToMove, selectbox.options[optionToMove.index-1]);
```

这个例子首先获得要移动 `<option>` 的索引，然后将其插入之前位于它前面的 `<option>` 之前，其中第二行代码适用于除第一个 `<option>` 之外的所有 `<option>`。下面的代码则可以将`<option>`向下移动一个位置：

```javascript
let optionToMove = selectbox.options[1];
selectbox.insertBefore(optionToMove,
selectbox.options[optionToMove.index+2]);
```

# 总结

`<select>` 和 `<option>` 是在 HTML 页面布局时，经常使用的表单控件，学会使用与之对应的 `HTMLSelectElement` 和 `HTMLOptionElement` 接口中的属性和方法会很容易的操控 `<select>` 和 `<option>` 元素。借助于 `DOM` 操作 `<select>` 和 `<option>` 也较为方便。