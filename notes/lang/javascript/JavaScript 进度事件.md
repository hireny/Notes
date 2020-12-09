# 进度事件

Progress Events 是 W3C 的工作草案，定义了客户端-服务器端通信。这些事件最初只针对 XHR，现在也推广到了其它类似的 API。有以下 6 个进度相关的事件。

- `abort`：请求中止时（比如用户取消）触发。如果发生错误导致中止，不会触发该事件。
- `error`：请求错误时触发。
- `load`：请求成功并完成响应时触发。
- `loadstart`：请求成功时并接收到响应的第一个字节时触发。
- `loadend`：请求成功并响应完成时，且在 `error`、`abort`、`load` 等事件之后触发。
- `progress`：在请求和响应的过程中不断触发。
- `timeout`：请求超时时触发。

每次请求都会首先触发 `loadstart` 事件，之后是一个或多个 `progress` 事件，接着是 `error`、`abort` 或 `load` 中的一个，最后以 `loadend` 事件结束。

## load 事件

Firefox 最初在实现 XHR 的时候，曾致力于简化交互模式。最终，增加了一个 `load` 事件用于替代 `readystatechange` 事件。`load` 事件在响应接收完成后立即触发，这样就不用检查 `readyState` 属性了。`onload` 事件处理程序会收到一个 `event` 对象，其 `target` 属性设置为 XHR 实例，在这个实例上可以访问所有 XHR 对象属性和方法。不过，并不是所有浏览器都实现了这个事件的 `event` 对象。考虑到跨浏览器兼容，还是需要像下面这样使用 XHR 对象变量：

```javascript
let xhr = new XMLHttpRequest():
xhr.onload = function() {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
        alert(xhr.responseText);
    } else {
        alert("Request was unsuccessful: " + xhr.status);
    }
};
xhr.open("get", "altevents.php", true);
xhr.send(null);
```

只要从服务器收到响应，无论状态码是什么，都会触发 `load` 事件。这意味着还需要检查 `status` 属性才能确定数据是否有效。

## progress 事件

Mozilla 在 XHR 对象上另一个创建是 `progress` 事件，在浏览器接收数据期间。这个事件会反复触发。每次触发时，`onprogress` 事件处理程序都会收到 `event` 对象，其 `target` 属性是 XHR 对象，且包含 3 个额外属性：`lengthComputable`、`position` 和 `totalSize`。其中，`lengthComputable` 是一个布尔值，表示进度信息是否可用；`position` 是接收的字节数；`totalSize` 是响应的 `Content-Length` 头部定义的总字节数。有了这些信息，就可以给用户提供进度条了。以下代码演示了如何向用户展示进度：

```javascript
let xhr = new XMLHttpRequest();
xhr.onload = function(event) {
    if ((xhr.status >= 200 && xhr.status < 300) ||
xhr.status == 304) {
        alert(xhr.responseText);
    } else {
        alert("Request was unsuccessful: " + xhr.status);
    }
};
xhr.onprogress = function(event) {
    let divStatus = document.getElementById("status");
    if (event.lengthComputable) {
        divStatus.innerHTML = "Received " + event.position + " of " +
event.totalSize +
" bytes";
    }
};
xhr.open("get", "altevents.php", true);
xhr.send(null);
```

为了保证正确执行，必须在调用 `open()` 之前添加 `onprogress` 事件处理程序。在前面的例子中，每次触发 `progress` 事件都会更新 HTML 元素中的信息。假设响应由 `Content-Length` 头部，就可以利用这些信息计算出已经收到响应的百分比。


# ProgressEvent

进度相关的事件都是继承自 `ProgressEvent` 接口的，该接口用来描述外部资源加载的进度。而浏览器原生提供 `ProgressEvent()` 构造函数来生成事件实例。如下所示：

```javascript
let event = new ProgressEvent(type, options);
```

而构造函数中接收的两个参数：第一个参数是字符串，表示事件的类型，这个参数是必须的；第二个参数是配置对象，表示事件的属性，该参数可选。配置对象除了可以使用 `Event` 接口的配置属性，还可以使用下面的属性，所有这些属性都是可选的。

- lengthComputable：