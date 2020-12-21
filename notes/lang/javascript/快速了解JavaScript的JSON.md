JSON（JavaScript Object Notation）是一种通用的、轻量级的数据交换格式，而不是编程语言。由于 XML 过于冗余和啰嗦， 因此，使用 JSON 代替 XML 进行网络传输。

一个 JSON 对象可以被储存在它自己的文件中，这基本上就是一个文本文件，扩展名为 `.json`。

# JSON语法

基本上 JSON 对象就是基于 JavaScript 对象，因此，可以把 JavaScript 对象写入 JSON 数据，JSON 可以将JavaScript对象分为三类：

- 简单值：字符串、数值、布尔值和 `null`。`undefined` 除外。
- 对象：复杂的数据类型，对象表示有序键/值对。
- 数组：复杂的数据类型，数组表示带有索引的有序列表，可以通过索引访问数组中的值。

而在JSON中，使用如下语法规则：

- 数据在键/值对中，且由逗号分隔。
- 花括号保存对象，方括号保存数组。

```json
{
    "code": 1,
    "status": true,
    "msg": "Http Request Success",
    "data": [
        {
            “id”: 2011101010,
            "name": "小赵",
            "age": 29,
            "sex": "男",
            "birthday": “1992-04-09”,
            "address": [
                "北京市通州区万达广场",
                "天津市",
                "河北省邯郸市"
            ]
        },
        {
            “id”: 2011101011,
            "name": "小钱",
            "age": 27,
            "sex": "男",
            "birthday": “1994-05-01”,
            "address": [
                "北京市通州区万达广场",
                "天津市",
                "河北省邯郸市"
            ]
        },
        {
            “id”: 2011101013,
            "name": "小玲",
            "age": 28,
            "sex": "女",
            "birthday": “1993-09-26”,
            "address": [
                "北京市通州区万达广场",
                "天津市",
                "河北省邯郸市"
            ]
        }
    ]
}
```

<small>注意：JSON 简单值中的字符串必须使用双引号标记，因为使用单引号会导致语法错误。</small>

# 序列化与解析

ECMAScipt 5 添加了用于JSON解析与序列化的 `JSON` 对象，该对象只包含两个方法：

- `JSON.stringify()`：用于将JavaScript简单值、对象或数组序列化为JSON字符串。
- `JSON.parse()`：用于将JSON格式的字符串解析为原生JavaScript值。

下面详细介绍这两种方法。

## 序列化

`JSON.stringify()` 方法将一个JavaScript简单值、对象或数组转换为JSON字符串。如果指定一个 `replacer` 函数，则可以选择性地替换掉，或者指定的 `replacer` 的数组，则可选择性地仅包含数组指定的属性。

```javascript
stringify(value, replacer, space): string;
```
- `value`：将要序列化成一个 JSON 字符串的值。
- `replacer`：可选参数，用于转换结果的函数或数组。
- `space`：可选参数，控制结果中，每个属性的间距，用于美化输出。

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
};
let json = JSON.stringify(student);
```

使用 `JSON.stringify()` 在默认情况下会输出不包含空格或缩进的 JSON 字符串。如下是输出的 `JSON` 字符串结果：

```json
{"id":2011101011,"name":"小钱","age":27,"birthday":"1994-05-31T16:00:00.000Z","address":["北京市通州区万达广场","天津市","河北省邯郸市"]}
```

而在序列化时，会有意地将所有函数和原型对象在结果中省略。

<small>注意：值为 `undefined` 的任何属性都会被省略。</small>

### replacer

如果 `replacer` 是一个函数，则在序列化过程中，被序列化的值的每个属性都会经过该函数的转换和处理；如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中；如果该参数为 `null` 或者未提供，则对象所有的属性都会被序列化。

`JSON.stringify()` 方法的 `replacer` 参数用于转换结果。

如果 `replacer` 是一个数组，那 `JSON.stringify()` 返回的结果只会包含数组中列出的对象属性。如下所示：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
};
let json = JSON.stringify(student, ["name", "address"]);
```

上面的例子，输出的结果会根据第二个参数传入的数组 `["name", "address"]` 来获取对应 `name` 和 `address` 属性及它们的值进行序列化：

```json
{"name":"小钱","address":["北京市通州区","浙江省杭州市","河北省邯郸市"]}
```

如果 `replacer` 是一个函数，它将有键 `key` 和值 `value` 两个参数，且根据函数进行相应的序列化操作，且函数返回值应当是JSON中`key`对应的`value`值。 `key` 始终是字符串，只在值不属于某个`key`时，才会是空字符串。如下所示：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
};
let json = JSON.stringify(student, (key, value) => {
    console.log(typeof value);
    if (key == "address") {
        return value.join(",");
    }
    if (key == "birthday") {
        return undefined;
    }
    return value;    // 一定要提供该默认返回值，以返回其它属性传入的值。
});
```

最终得到的JSON 字符串是这样的：

```json
{"id":2011101011,"name":"小钱","age":27,"address":"北京市通州区,浙江省杭州市,河北省邯郸市"}
```

注意：使用 `replacer` 序列化得到的JSON在使用`parse`进行解析，就只会得到JSON中的属性。

### space

`space` 参数用于控制缩进和空格。当该参数为数值时，表示每一级缩进的空格数。如下所示：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
}
let json = JSON.stringify(student, null, 4);
```

输出的 `JSON` 格式如下所示：

```json
{
    "id": 2011101011,
    "name": "小钱",
    "age": 27,
    "birthday": "1994-05-31T16:00:00.000Z",
    "address": [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
}
```

<small>注意：除了缩进，`JSON.stringify()` 方法还很方便地插入了换行符。最大缩进值为 10，大于 10 的值自动设置为 10；最小缩进值为 1，小于 1 的值意味着没有缩进字符。</small>

当该参数为字符串时，将指定该字符串为空格；如果没有提供，或者值为 `null`，将没有空格。若两个连字符：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
}
let json = JSON.stringify(student, null, "--");
```

输出的 `JSON` 格式如下：

```json
{
--"id": 2011101011,
--"name": "小钱",
--"age": 27,
--"birthday": "1994-05-31T16:00:00.000Z",
--"address": [
----"北京市通州区",
----"浙江省杭州市",
----"河北省邯郸市"
--]
}
```

也可以使用空格 `" "` 、制表符 `'\t'` 或 `Tab`来设置缩进字符。如下所示：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ]
}
let json = JSON.stringify(student, null, '\t');
```

输出的 `JSON` 格式如下：

```json
{
	"id": 2011101011,
	"name": "小钱",
	"age": 27,
	"birthday": "1994-05-31T16:00:00.000Z",
	"address": [
		"北京市通州区",
		"浙江省杭州市",
		"河北省邯郸市"
	]
}
```

<small>注意：使用字符串时同样有 10 个字符的长度限制。如果字符串长度超过 10，则会在第 10 个字符处截断。</small>

### toJSON()

如果对象之中定义了 `toJSON()` 方法，`JSON.stringify()` 就会调用该对象的 `toJSON()` 方法，覆盖默认序列化行为。如下所示：

```javascript
let student = {
    id: 2011101011,
    name: "小钱",
    age: 27,
    sex: undefined,
    birthday: new Date(1994, 5, 1),
    address: [
        "北京市通州区",
        "浙江省杭州市",
        "河北省邯郸市"
    ],
    toJSON: function () {
        return {
            name: this.name,
            age: this.age
        };
    }
}
let json = JSON.stringify(student, null, " ");
```

输出的 `JSON` 格式如下：

```json
{
 "name": "小钱",
 "age": 27
}
```

`toJSON()` 方法可以返回任意序列化值，都可以起到相应的作用。

<small>注意：箭头函数不能用来定义`toJSON()`方法。主要原因是箭头函数的词法作用域是全局作用域，在这种情况下不适合。</small>

`toJSON()` 方法可以与 `replacer` 一起使用，因此，序列化流程的顺序非常重要。在把对象传给 `JSON.stringify()` 时会执行如下步骤。

1. 如果定义了 `toJSON()`方法且能获取到实际值，则调用`toJSON()`方法，否则使用默认的序列化。
2. 如果定义了`JSON.stringify()`方法的`replacer`函数，则使用`replacer`函数。传入的值就是第 1 步返回的值。
3. 第 2 步返回的每个值都会相应地进行序列化。
4. 如果提供了`space` 参数，则相应地进行缩进。

理解这个顺序有助于决定是创建 `toJSON()` 方法，还是使用`replacer`函数，抑或是两者都用。

## 解析

`JSON.parse()` 方法用来解析 JSON 字符串，构造由字符串描述的 JavaScript 值或对象。提供可选的 `reviver` 函数用以在返回之前对所得到的对象执行变换（操作）。

```javascript
parse(text, reviver): any;
```

- `text`：要被解析成 JavaScript 值的字符串。
- `reviver`：可选参数，用于修改解析生成的原始值。

```javascript
let json = '{' +
    '"id":2011101011,' +
    '"name":"小钱",' +
    '"age":27,' +
    '"birthday":"1994-05-31T16:00:00.000Z",' +
    '"address":[' +
        '"北京市通州区",' +
        '"浙江省杭州市",' +
        '"河北省邯郸市"' +
    ']' +
'}'
let student = JSON.parse(json);
```

输出的对象为：

```javascript
{
  id: 2011101011,
  name: '小钱',
  age: 27,
  birthday: '1994-05-31T16:00:00.000Z',
  address: [ '北京市通州区', '浙江省杭州市', '河北省邯郸市' ]
}
```

<small>注意：如果给 `JSON.parse()` 传入的 `JSON` 字符串无效，则会抛出异常。</small>

```javascript
let book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas",
        "Matt Frisbie"
    ],
    edition: 4,
    year: 2017,
    releaseDate: new Date(2017, 11, 1)
};
let jsonText = JSON.stringify(book);
let bookCopy = JSON.parse(jsonText,
(key, value) => key == "releaseDate" ? new Date(value) : value);
alert(bookCopy.releaseDate.getFullYear());
```

### reviver

`reviver` 参数是一个还原函数，如果指定了还原函数，将解析出的 JavaScript 值经过一次转换后返回最终值。该函数的调用时机在 `parse` 函数返回之前，并接收 `key` 和 `value` 两个参数。如果返回 `undefined`，结果会删除与 `undefined` 相对应的 `key`；而返回其它值，则该值会成为相应键的值并插入到返回结果中。

```javascript
let json = '{' +
    '"id":2011101011,' +
    '"name":"小钱",' +
    '"age":27,' +
    '"birthday":"1994-05-31T16:00:00.000Z",' +
    '"address":[' +
        '"北京市通州区",' +
        '"浙江省杭州市",' +
        '"河北省邯郸市"' +
    ']' +
'}'
let student = JSON.parse(json, (key, value) => {
    if (key == "birthday") {
        return new Date(value);
    }
    return value;
});
```

在上面的代码中，`JSON.parse()` 方法中，定义了一个还原函数，获取 `birthday` 键并重新创建新的 `Date` 对象。最后， `student.birthday` 属性变成了 `Date` 对象，可以调用有关 `Date` 类的方法了。

# 总结

通过上面的简单介绍，JSON 这种数据结构可以很方便地表示复杂的数据结构，如 JavaScript 中的数字、字符串、布尔值、数组、对象和 `null` 都可以用 JSON 格式表示。且比 XML 更加轻量级。而且，现代浏览器都已经原生支持全局 JSON 对象，并且使用 `JSON.stringify()` 和 `JSON.parse()` 方法实现了JavaScript与JSON之间相互转换。