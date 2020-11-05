# Buttons

`BootStrap` 提供了自定义按钮样式，用于表单，对话框等操作，并支持多种尺寸和状态等。

## 例子

`BootStrap` 包括几种预定义的按钮样式，每种样式都有其自己的语义目的，并添加了一些其它空间，以进行更多控制。

```html
<button type="button" class="btn btn-primary">Primary</button>
<button type="button" class="btn btn-secondary">Secondary</button>
<button type="button" class="btn btn-success">Success</button>
<button type="button" class="btn btn-danger">Danger</button>
<button type="button" class="btn btn-warning">Warning</button>
<button type="button" class="btn btn-info">Info</button>
<button type="button" class="btn btn-light">Light</button>
<button type="button" class="btn btn-dark">Dark</button>

<button type="button" class="btn btn-link">Link</button>
```

## 禁用文字换行

如果你不希望包装按钮文本，则可以将 `.text-nowrap` 类添加到按钮。在 `Saas` 中，您可以设置 `$btn-white-space: nowrap` 为禁用每个按钮的文本换行。

## 按钮标签

这些 `.btn` 类旨在与 `<button>` 元素一起使用。但是，您也可以在 `<a>` 或 `<input>` 元素上使用这些类（尽管某些浏览器可能会使用略有不同的呈现方式）。

在`<a>`用于触发页面内功能（如折叠内容）的元素上使用按钮类时，而不是链接到新页面或当前页面内的部分时，应给这些链接一个role="button"适当的传达其目的的辅助技术，例如屏幕阅读器。

```html
<a class="btn btn-primary" href="#" role="button">Link</a>
<button class="btn btn-primary" type="submit">Button</button>
<input class="btn btn-primary" type="button" value="Input">
<input class="btn btn-primary" type="submit" value="Submit">
<input class="btn btn-primary" type="reset" value="Reset">
```

## 轮廓按钮

需要一个按钮，但是他们不需要带来沉重的背景颜色吗？将默认修饰符类替换为默认修饰符类，`.btn-outline-*`以删除任何按钮上的所有背景图像和颜色。

```html
<button type="button" class="btn btn-outline-primary">Primary</button>
<button type="button" class="btn btn-outline-secondary">Secondary</button>
<button type="button" class="btn btn-outline-success">Success</button>
<button type="button" class="btn btn-outline-danger">Danger</button>
<button type="button" class="btn btn-outline-warning">Warning</button>
<button type="button" class="btn btn-outline-info">Info</button>
<button type="button" class="btn btn-outline-light">Light</button>
<button type="button" class="btn btn-outline-dark">Dark</button>
```

## 尺寸

喜欢更大或更小的按钮吗？添加`.btn-lg`或`.btn-sm`增加尺寸。

```html
<button type="button" class="btn btn-primary btn-lg">Large button</button>
<button type="button" class="btn btn-secondary btn-lg">Large button</button>
```

```html
<button type="button" class="btn btn-primary btn-sm">Small button</button>
<button type="button" class="btn btn-secondary btn-sm">Small button</button>
```

通过添加来创建块级按钮（跨越父级的整个宽度的按钮）`.btn-block`。

```html
<button type="button" class="btn btn-primary btn-lg btn-block">Block level button</button>
<button type="button" class="btn btn-secondary btn-lg btn-block">Block level button</button>
```

## 活跃状态

处于活动状态时，按钮将显示为按下状态（北京较深，边框更深且有阴影）。无需向 `<button>` 添加类，因为它们使用伪类。但是，如果您需要以编程方式复制状态，则仍然可以使用 `.active` （并包括 `aria-pressed="true"属性`）强制使用相同的活动外观。

```html
<a href="#" class="btn btn-primary btn-lg active" role="button" aria-pressed="true">Primary link</a>
<a href="#" class="btn btn-secondary btn-lg active" role="button" aria-pressed="true">Link</a>
```

## 禁用状态

通过将 `disabled` boolean属性添加到任何 `<button>` 元素，使按钮看起来不活动。

```html
<button type="button" class="btn btn-lg btn-primary" disabled>Primary button</button>
<button type="button" class="btn btn-secondary btn-lg" disabled>Button</button>
```

使用 `<a>` 标签的禁用按钮的行为略有不同：

- `<a>` 不支持该 `disabled` 属性，因此您必须添加 `.disabled` 该类以使其在视觉上显得已禁用。
- 包括一些将来友好的样式，以禁用 `pointer-events` 锚点按钮上的所有样式。在支持该属性的浏览器中，您根本看不到禁用的光标。
- 禁用的按钮应包括 `aria-disabled="true"`属性，以指示辅助技术的元素状态。

```html
<a href="#" class="btn btn-primary btn-lg disabled" tabindex="-1" role="button" aria-disabled="true">Primary link</a>
<a href="#" class="btn btn-secondary btn-lg disabled" tabindex="-1" role="button" aria-disabled="true">Link</a>
```

> 链接功能警告 <br/>
> 本.disabled类使用pointer-events: none尝试禁用的链路功能<a>S，但CSS属性尚不规范。此外，即使在支持的浏览器中pointer-events: none，键盘导航也不会受到影响，这意味着有视力的键盘用户和辅助技术用户仍将能够激活这些链接。为安全起见，请tabindex="-1"在这些链接上添加一个属性（以防止它们获得键盘焦点），并使用自定义JavaScript禁用其功能。
> 

## 按钮插件

使用按钮执行更多操作。控制按钮状态为更多组件（如工具栏）创建按钮组。

### 切换状态

添加`data-toggle="button"`以切换按钮的`active`状态。如果你预先切换按钮，你必须手动添加`.active`类和 `aria-pressed="true"`到`<button>`。

```html
<button type="button" class="btn btn-primary" data-toggle="button" aria-pressed="false">
  Single toggle
</button>
```

### 复选框和单选按钮

Bootstrap的.button样式可以应用于<label>s 等其他元素，以提供复选框或单选样式按钮切换。添加data-toggle="buttons"到.btn-group包含这些修改后的按钮以通过JavaScript启用其切换行为，并在按钮内添加.btn-group-toggle样式<input>。请注意，您可以创建单个输入驱动的按钮或它们的组。

这些按钮的选中状态仅通过click按钮上的事件进行更新。如果您使用另一种方法来更新输入（例如，通过<input type="reset">或通过手动应用输入的checked属性来进行更新），则需要手动.active打开<label>。

请注意，预先检查的按钮要求您手动将.active类添加到输入的<label>。

```html
<div class="btn-group-toggle" data-toggle="buttons">
  <label class="btn btn-secondary active">
    <input type="checkbox" checked> Checked
  </label>
</div>
```

```html
<div class="btn-group btn-group-toggle" data-toggle="buttons">
  <label class="btn btn-secondary active">
    <input type="radio" name="options" id="option1" checked> Active
  </label>
  <label class="btn btn-secondary">
    <input type="radio" name="options" id="option2"> Radio
  </label>
  <label class="btn btn-secondary">
    <input type="radio" name="options" id="option3"> Radio
  </label>
</div>
```

### 方法

|方法|描述|
|:---:|:---:|
|`$().button('toggle')`|切换推动状态。使按钮具有已被激活的外观。|
|`$().button('dispose')`|销毁元素的按钮。|