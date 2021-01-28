编程语言都会需要完善的错误处理策略使得应用程序更为合理的操作错误。错误处理在服务端的处理较为完善，但是浏览器端进展较为缓慢，不同浏览器的错误处理方式也不同，且默认的错误处理方式对用户也不友好。因此，必须理解各种捕获和处理错误的方式。而在 `ECMA-262` 的第3版中增加了 `try-catch` 语句块和 `throw` 语句来处理错误，以及一些错误类型来描述错误。

## try-catch

`ECMA-262` 新增的错误处理方式与 Java 相似，将可能出错的代码放在 `try` 子句中，而处理错误的代码放在 `catch` 子句中：

```javascript
try {
    // Possible error code
} catch (e) {
    // Error Handler
}
```

`catch` 子句中捕获到的错误对象包含发生错误的相关信息，通过该错误对象可以获取错误类型的 `name` 属性和保存错误消息的 `message` 属性，`e.message` 即可调用。

在使用 `try-catch` 语句块时，要知道什么时候使用最好。如果发生无法控制的错误上，就需要使用 `try-catch` 语句块来处理错误；如果明确知道代码会发生某种错误，就要采用相应的操作来防止错误发生，而不是使用 `try-catch` 语句块来处理错误。

### finally 

在 `try-catch` 语句块中，无论是 `try` 子句执行完，还是 `catch` 子句执行完，都可以接着执行 `finally` 子句中的代码，`try` 与 `catch` 都无法阻止，包括 `return` 语句。

```javascript
function test(){
    try {
        return 1;
    } catch (e){
        throw e;
    } finally {
        return false;
    }
}
```

上面的方法，最后会返回 `false`。

<small>注意：主要代码中包含了 `finally` 子句，`try` 块或 `catch` 块中的 `return` 语句就会被忽略，理解这一点很重要。在使用 `finally` 时一定要仔细确认代码的行为。</small>

## 抛出错误

使用 `throw` 抛出错误是另一种错误处理方式。`throw` 抛出任意表达式。如下所示：

```javascript
throw "Not Value Error";        // String type error
throw 31;                       // Number type error
throw false;                    // Boolean type error
throw { name: "JavaScript" };   // Object type error
```

也可以调用 `Error` 构造函数来生成一个错误对象抛出，接收的值为错误消息。如下所示：

```javascript
throw new Error("Null value Error.");
```

也可以调用特定的错误类型生成一个错误对象并抛出。如 `SyntaxError`、`TypeError` 等。使用 `ES6` 中的继承语法创建错误类型也可以，这样需要提供 `name` 属性和 `message` 属性。如下所示：

```javascript
class CustomError extends Error {
    constructor(message) {
        super(message);
        this.name = "CustomError";
        this.message = message;
    }
}
throw new CustomError("Null Value Error.");
```

这种方式有助于在捕获错误时更精准地区分错误。

## Error

`Error` 错误类型是基本类型，其它错误类型基本都是继承该类型。`Error` 提供了一个构造方法用来创建实例对象，当发生错误时，`Error` 通过构造方法创建实例对象并被抛出。

```javascript
throw new Error("Null Value Error.");
```

`Error` 错误类型提供了三个属性：

- `name`：字符串类型。表示错误名称。
- `message`：字符串类型。表示错误信息。
- `stack`：字符串类型。表示错误的堆栈。

`ECMA-262` 总共定义了 8 种错误类型，除了 `Error` 通用的错误类型外，还有如下几种类型：

- `EvalError`：该错误类型会在使用 `eval()` 函数发生错误时抛出。
- `InternalError`：该错误类型主要会在底层 JavaScript 引擎抛出异常时由浏览器抛出。如递归过多导致栈溢出。通常并不是需要处理的错误。
- `RangeError`：该错误类型会在数值变量或参数越界时抛出。
- `ReferenceError`：该错误类型会在引用无效时抛出。
- `SyntaxError`：该错误类型会在 `eval()` 在解析代码的过程中发生。
- `TypeError`：该错误类型会在变量或参数不是预期类型，或者访问不存在的方法时抛出。
- `URIError`：该错误类型只会在使用  `encodeURI()` 或 `decodeURI()` ，并且传入的 `URI` 格式错误时发生。

浏览器很少会抛出 `Error` 类型的错误，主要是开发者抛出的自定义错误。而其它错误类型可以使用 `instanceof` 进行判断。如下所示：

```javascript
try {
    // Possible error code
} catch (e) {
    if (e instanceof TypeError) {
        // Type Error Handler
    } else if (e instanceof ReferenceError) {
        // Reference Error Handler
    } else {
        // Other Error Handler
    }
}
```

## error 事件

没有被 `try-catch` 捕获的错误都会在 `window` 对象上触发 `error` 事件。该事件会传入 3 个参数：

- `message`：错误消息。
- `url`：发生错误的 `URL`。
- `line`：错误的行号。
- `colno`：错误的列号。
- `error`：`Error`对象。

```javascript
window.onerror = (message, url, line, colno, error) => {
    // 可以对错误的消息进行处理，这里打印在控制台上
    console.log(message);
    return false; // 这里返回 false 来阻止浏览器默认报告错误的行为
};
```

通过返回 `false`，函数实际上变成整个文档的 `try/catch` 语句，可以捕获所有未处理的运行时错误。适当的 `try-catch` 语句块意味着不会有错误到达浏览器这个 层次，因此就不会触发 `error` 事件。当返回 `true`，会阻止执行默认事件处理函数。

而有一些资源加载失败，会触发一个 `error` 事件，这个事件遵循 `DOM` 格式。因此可以使用 `addEventListener` 捕获。

```javascript
const image = new Image();
image.addEventListener("error", (event) => {
    console.log("Image not loaded!");
});
image.src = "resource.gif"; // 不存在，资源会加载失败。
```

## 总结

一个设计良好的错误处理对编程语言十分重要。JavaScript 提供的 `try-cath` 和 `throw` 两种处理错误的方式。但是两种错误处理方式，有不同的错误场景要求。如果编写的库或函数需要知道错误发生的具体原因，就抛出异常；如果确切知道接下来的操作，就捕获错误。而没有通过 `try-catch` 处理的错误，可以使用 `window.onerror` 事件来处理。