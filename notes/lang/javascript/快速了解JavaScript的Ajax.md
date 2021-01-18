# 概述

Ajax（Asynchronous JavaScript and XML，即异步 JavaScript 和 XML）技术用于与服务器交换数据并刷新部分页面，实现更好的用户体验。

Ajax 的核心对象是 `XMLHttpRequest`，通过 `XMLHttpRequest` 可以在不刷新页面的情况下请求特定 `URL`，获取数据。由微软实现并在 IE5 中支持，用于执行异步网络请求。虽然 Ajax 是 Asynchronous JavaScript+XML的缩写，但Ajax通信与数据格式无关，并不一定是XML格式。甚至支持 HTTP 以外的协议（如 `file://` 和 `FTP`），尽管可能受到更多出于安全等原因的限制。

使用 Ajax 需要几个步骤：

1. 创建 `XMLHttpRequest` 对象。
2. 发出 `HTTP` 请求。
3. 接收服务器传回的数据。
4. 更新网页数据。

总之，Ajax 就是通过 `XMLHttpRequest` 对象发出 `HTTP` 请求，得到服务器响应并处理。

# 创建 XMLHttpRequest

在所有现代浏览器都可以通过 `XMLHttpRequest` 构造函数创建对象：

```javascript
let xhr = new XMLHttpRequest();
```

而在老版本 IE5 和 IE6 中使用 `ActiveX` 对象创建：

```javascript
let xhr = new ActiveXObject("Microsoft.XMLHTTP");
```

# 使用 XMLHttpRequest

创建对象后，需要使用 `XMLHttpRequest` 对象调用 `open(type, url, async)` 方法并设置三个参数：

- type：表示请求类型，如 `"get"` 、`"post"` 等。
- url：表示请求的url。
- async：表示请求是否异步。如 `true`表示异步请求，`false`表示同步请求，它会使JavaScript代码等待服务器响应之后再继续执行。

## 请求

`XMLHttpRequest` 使用 `open()` 方法初始化一个请求，一共可以接收五个参数：

```javascript
open(method, url, async, username, password): void;
```

- `method`：字符串，表示要传入的HTTP方法，如`/GET/POST/PUT/DELETE`等。
- `url`：字符串，表示请求发送目标URL。
- `async`：布尔值，表示请求是否为异步，默认为`true`。如果值为`false`，`send()`方法只有等待收到服务器返回结果，才会进行下一步操作。参数可选。如果 `multipart` 属性为 `true` 则这个必须为 `true`，否则将引发异常。注意，主线程上的同步请求很容易破坏用户体验，应避免；实际上，许多浏览器已完全启用主线程上的同步`XMLHttpRequest`支持。在`Worker`中允许同步请求。
- `username`：字符串，表示用户名用于认证用途，默认为`null`。参数可选。
- `password`：字符串，表示密码用于认证用途，默认为`null`。参数可选。


因此，前两个参数是必须要添加的。请求初始化后，就可以调用`send()` 方法发出 HTTP 请求。如果是异步请求（默认异步请求），则此方法会在请求发送后立即返回；如果是同步请求，则此方法直接响应到达后才会返回。该方法有一个可选参数，作为请求主体，请求方法是`GET/HEAD`，则请求体设置为`null`。

```javascript
send(body): void;
```

该方法的参数是 `XMLHttpRequest` 请求中要发送的数据体，可以设置的值为：

- `Document`：发送 `Document` 类型的数据，但发送之前会被序列化。
- `BodyInit`：可以是 `Blob/BufferSource/FormData/URLSearchParams/ReadableStream<Uint8Array>/string` 这几种类型的数据。
- `null`。如果没有指定值，默认值就是 `null`。

因此，创建完`XMLHttpRequest`对象后，就会按顺序调用`open()`和`send()`方法来发送请求。而最常用的方法就是 `GET` 和 `POST` 方法。

### GET

`GET` 请求方法，主要用于查询服务器信息。而查询的参数要正确编码并添加到`URL`后面，例如 `example.jsp?username=Alpha&password=a123456`，参数之间会用`&`符号分隔，然后才能传给 `open()` 方法。该方法最常见的错误就是格式不对，因此可以使用 `encodeURIComponent()` 函数对字符串进行编码。

```javascript
let url = "example.jsp";
url = addParam(url, "username", "Alpha");
url = addParam(url, "password", "a123456");

xhr.open('get', url, true);

function addParam(url, name, value) {
    url += (url.indexOf("?") == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}
```

`addParam()` 函数保证 `XMLHttpRequest` 发送请求的 `URL` 格式正确。

### POST

`POST` 请求方法，主要用于向服务器发送数据。`POST` 请求会在请求体中携带要提交的数据。`POST` 请求在传参上不用像 `GET` 请求那样将参数拼接在 `URL` 后面，而是使用 `send()` 方法来携带要提交的数据。

默认情况下，对服务器而言，`POST` 请求与`HTML`表单提交是不一样的。服务器逻辑需要读取原始 `POST` 数据才能取得浏览器发送的数据。不过，还可以使用 `XML` 模拟表单提交。为此，第一步需要把 Content-Type 头部设置为 "application/x-www-formurlencoded"，这是提交表单时使用的内容类型。第二步是创建对应格式的字符串。POST 数据此时使用与查询字符串相同的格式。如果网页中确实有一个表单需要序列化并通过 `XMLHttpRequest` 发送到服务器，则可以使用 `serialize()` 函数来创建相应的字符串，如下所示：

```javascript
function submitData() {
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (xhr.readyState == 4) {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                console.log(xhr.responseText);
            } else {
                alert("请求失败: " + xhr.status);
            }
        }
    };
    xhr.open("post", "example.jsp", true);
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    // ID为 user-info 的表单元素
    let form = document.getElementById("user-info");
    xhr.send(serialize(form));
}
```

注意：POST 请求相比 GET 请求要占用更多资源。从性能方面说，发送相同数据的数据，GET请求比 POST 请求快两倍。

## 响应

当服务器收到请求并响应后，`XMLHttpRequest` 对象会有以下属性被填充数据：

- `status`：响应的 HTTP 状态码。
- `stautsText`：响应的 HTTP 状态描述。
- `response`：响应的正文。
- `responseText`：作为响应体返回的文本。
- `responseXML`：如果响应的内容类型是 `"text/xml"`或`"application/xml"`，那就是包含响应数据的 XML DOM 文档。

一般都是通过 `status` 属性来确保响应是否成功返回。当 `status` 为 2xx 表示成功，为 304 表示资源未被修改，而是从缓存中取出的。这时的 `responseText` 或 `responseXML` 属性中会有内容。因此，我们可以使用 `status` 属性来判断响应是否有效，如下所示：

```javascript
xhr.open("get", "example.txt", false);
xhr.send(null);

if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText);
} else {
    console.log("Request was unsuccessful: " + xhr.status);
}
```

### response

`XMLHttpRequest.response` 属性返回响应的数据体（即 HTTP 响应的 `body` 部分）。返回的类型为 `ArrayBuffer`、`Blob`、`Document`、`Object`或`DOMString`中的一个。具体类型由 `XMLHttpRequest.responseType` 类型决定。

当请求尚未完成或尚未成功，该值为 `null`。但当 `responseType` 属性设置成 `"text"` 或空字符串(`""`) 且当请求状态在`LOADING`时，`response`属性包含到目前为止该请求已经取得的内容。

```javascript
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        console.log(xhr.response);
    }
}
```

### responseType

`XMLHttpRequest.responseType` 属性是一个字符串，返回响应数据的类型。允许手动设置返回数据的类型。如果设置为空字符串，则使用默认的`"text"`类型。

当 `responseType` 设置为一个特定的类型时，需确保服务器返回的类型和你所设置的类型是兼容的。如果不兼容，即使服务器返回了数据，数据也会变成`null`。`responseType` 属性支持以下几种值：

1. `""`：当`responseType`为空字符串时，与 `text` 相同，表示服务器返回文本数据。
2. `"arraybuffer"`：表示服务器返回的是一个包含二进制数据的`ArrayBuffer` 对象。
3. `“blob”`：表示服务器返回的是一个包含二进制数据的 `Blob`对象。
4. `“document”`：表示服务器返回的是一个`HTML Document` 或 `XML Document`，这取决于接收数据的 `MIME` 类型。
5. `“json”`：表示将接收到的服务器数据视为`JSON`来进行解析并得到。
6. `“text”`：表示服务器返回的是以`DOMString`对象表示的文本。

上面几种类型之中，`text`类型适合大多数情况，而且直接处理文本也比较方便。`document`类型适合返回`HTML/XML`文档的情况，这意味着，对于那些打开 CORS 的网站，可以直接用 Ajax 抓取网页，然后不用解析 HTML 字符串，直接对抓取回来的数据进行 DOM 操作。`blob` 类型适合读取二进制数据，比如图片文件。`XMLHttpRequest.responseType`属性要在调用 `open()` 方法之后，并且在调用 `send()` 方法之前调用。

```javascript
let xhr = new XMLHttpRequest();
xhr.open("GET", "example.jsp", true);
xhr.responseType = "json";
xhr.send(null);
```


### readyState

`XMLHttpRequest.readyState` 属性返回一个无符号短整型数字，表示`XMLHttpRequest`对象的当前状态。该对象会返回以下某个值：

- 返回 `0`，未初始化（Uninitialized），状态为 `UNSENT`。表示代理被创建，但尚未调用 `open()` 方法。
- 返回 `1`，状态为 `Open` 已打开（Open），状态为`OPENED`。表示 `open()` 方法已被调用，但尚未调用 `send()` 方法。
- 返回 `2`，已发送（Sent），状态为`HEADERS_RECEIVED`。表示已调用 `send()` 方法，并且头部和状态已经可获得，但尚未收到响应。
- 返回 `3`：接收中（Receiving），状态为`LOADING`。表示已经收到部分响应。
- 返回 `4`：已完成（Complete），状态为`DONE`。表示请求操作已完成。意味着数据传输已经彻底完成或失败。

通信过程中，每当 `XMLHttpRequest` 对象状态发送变化，`readyState` 属性值就会被改变，并会触发 `onreadystatechange`事件。并且`XMLHttpRequest`对象调用`abort()`方法，终止请求，也会造成 `readyState`属性变化。为保证兼容性，`onreadystatechange`事件应在`open()`方法之前赋值。如下所示：

```javascript
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
        } else {
            alert("Request was unsuccessful: " + xhr.status);
        }
    }
};
xhr.open("get", "example.txt", true);
xhr.send(null);
```

如果之前使用的是异步请求并想在响应之前取消，可以调用 `abort()` 方法：

```javascript
let xhr = new XMLHttpRequest();
xhr.open("get", "example.jsp", true);
setTimeout(function() {
    if (xhr) {
        xhr.abort();
        xhr = null;
    }
}, 5000);	// 5秒后，终止 Ajax 请求
```

调用这个方法后，`XMLHttpRequest` 对象会停止触发事件，并阻止访问这个对象上任何与响应相关的属性，而 `readyState` 属性变为 `4`，`status` 属性变为 `0` 。中断请求后，应该取消对 `XMLHttpRequest`  对象的引用。由于内存问题，不推荐重用 `XMLHttpRequest` 对象。

## HTTP头部

在 HTTP 的每次请求和响应中都会携带一些头部字段，默认请求下，`XMLHttpRequest` 请求会发送以下头部字段。

- `Accept`：浏览器可以处理的内容类型。
- `Accept-Charset`：浏览器可以显示的字符集。
- `Accept-Encoding`：浏览器可以处理的压缩编码类型。
- `Accept-Language`：浏览器使用的语言。
- `Connection`：浏览器与服务器的连接类型。
- `Cookie`：页面中设置的 `Cookie`。
- `Host`：发送请求的页面所在地域。
- `Referer`：发送请求的页面的 `URI`。
- `User-Agent`：浏览器的用户代理字符串。

`XMLHttpRequest`对象通过一些方法暴露与请求和响应相关的头部字段。

### setRequestHeader()

`XMLHttpRequest.setRequestHeader()` 方法是设置 HTTP 请求头部字段的方法。此方法接收两个参数：第一个参数是头部属性名，第二个参数是头部属性值。为保证请求头被发送，必须在 `open()` 之后，`send()` 之前调用。如果多次调用，设置同一个字段的值会合并成单一的值发送。如下所示：

```javascript
let xhr = new XMLHttpRequest();
xhr.open("get", "example.jsp", true);
xhr.setRequestHeader("Content-Type", "application/json");
xhr.setRequestHeader("Custom-Header", "Custom-Value");
xhr.send(null);
```

自定义头部要区别于浏览器正常发送的头部，否则会影响服务器正常响应。默认头部在有些浏览器上可以重写。而自定义一些 `header` 属性进行跨域请求时，可能会遇到 "not allowed by Access-Control-Allow-Headers in preflight response"，你可能需要在你的服务端设置 “Access-Control-Allow-Headers”。

### getResponseHeader()/getAllResponseHeaders()

`XMLHttpRequest.getResponseHeader()` 方法用于返回 HTTP 响应头中指定的属性值。如果在返回时，有多个一样的名字，那么返回的值就是用逗号和空格分隔开的字符串。

而 `XMLHttpRequest.getAllResponseHeaders()` 方法返回所有的响应头，是以 `CRLF` 分隔（回车+换行）的字符串，如果没收到服务器回应，该属性为 `null`。以下就是该方法返回的字符串样子：

```http
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: *
Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE
Access-Control-Allow-Origin: *
Access-Control-Max-Age: 3600
Content-Length: 0
Content-Type: application/json;charset=UTF-8
Date: Sun, 13 Dec 2020 11:46:23 GMT
Server: Apache-Coyote/1.1
```

通过解析以上头部字段的输出，就可以知道服务器发送的所有头部，而不需要单独去检查了。

### withCredentials

`XMLHttpRequest.withCredentials` 属性是一个`Boolean`类型，用来指定跨域请求时，是否应带有用户信息（如Cookie和认证的HTTP头信息）。默认值是 `false`，即向 `sample.com` 发送跨域请求时，不会发送 `sample.com` 设置在本机上的 Cookie。

当需要跨域 `AJAX` 请求发送 `Cookie` 时，需要设置 `XMLHttpRequest.withCredentials` 为 `true`。同源请求无需设置。而想要这个属性生效，服务器需要显示返回 `Access-Control-Allow-Credentials` 头信息。

```http
Access-Control-Allow-Credentials: true
```

注意：脚本总是遵守同源策略，无法从 `document.cookie` 或者 HTTP 响应的头信息中读取跨域的 Cookie，无论 `XMLHttpRequest.withCredentials` 的属性值是`true`或`false`。

### abort()

`XMLHttpRequest.abort()` 方法用于终止已被发出的请求。当一个请求被终止，`readyState` 和 `status` 属性都会被置为 `0`。

# 事件

`XMLHttpRequest` 对象的事件基本上都和请求进度有关，以下列出与 `XMLHttpRequest` 对象有关的事件：

- `abort`：请求中止时（比如用户取消）触发。如果发生错误导致中止，不会触发该事件。
- `error`：请求错误时触发。
- `load`：请求成功并完成响应时触发，不用检查 `readyState` 属性，可替代 `readystatechange` 事件。
- `loadstart`：请求成功时并接收到响应的第一个字节时触发。也适用于 `<img>` 和 `<video` 元素。
- `loadend`：请求成功并响应完成时，且在 `error`、`abort`、`load` 等事件之后触发。
- `progress`：在请求接收到数据时候被周期性触发。
- `timeout`：请求超时时触发。

触发的先后顺序为 `loadstart` > `progress` > `error/abort/load` > `loadend`。 

```javascript
let xhr = new XMLHttpRequest();
xhr.onloadstart = function(event) {
    console.log("请求开始：" + event);
};
xhr.addEventListener("progress", function(event) {
    let divStatus = document.getElementById("status");
    divStatus.innerHTML = event.lengthComputable;
    if (event.lengthComputable) {
        divStatus.innerHTML = divStatus.innerHTML + "\n" + "Received " + event.position + " of " +
            event.total +
            " bytes";
    }
}, false);
xhr.onabort = function() {
    console.log("请求中止");
};
xhr.onload = function(event) {
    if ((xhr.status >= 200 && xhr.status < 300) ||
        xhr.status === 304) {
        console.log(xhr.responseText);
    } else {
        console.log("请求失败：" + xhr.status);
    }
};
xhr.onerror = function(event) {
    console.error("请求错误：" + event);
};
xhr.onloadend = function(event) {
    console.log("请求完成：" + event);
};
xhr.ontimeout = function() {
    console.error("请求超时。");
};
xhr.open("get", "http://localhost:8088/servlets/ajaxGet", true);
xhr.timeout = 5000; // 5秒超时
xhr.send(null);
setTimeout(function () {
    if (xhr) {
        xhr.abort();
        xhr = null;
    }
}, 4000);		// 4 秒之后，终止AJAX请求
```

其中，`progress` 事件触发会收到 `event` 对象，该属性包含 `lengthComputable`、`position` 和 `totalSize` 这三个属性:

- `lengthComputable`：布尔值属性，表示进度信息是否可用。
- `position`：接收的字节数。
- `total`：响应的 `Content-Length` 头部定义的总字节数。

## 进度事件

进度事件定义了客户端/服务端通信，用于测量如 HTTP 请求（XMLHttpRequest或`<img>`、`<audio>`、`<video>`、`<style>`、`<link>`等底层资源的加载）等底层流程进度的事件。继承于 Event。原生提供ProgressEvent()构造函数生成事件对象。如下所示：

```javascript
ProgressEvent(type, {lengthComputable: boolean, loaded: number, total: number});
```

构造函数中有两个参数：

- 第一个参数返回的是字符串，表示事件的类型，这个参数是必须的。
- 第二个参数是配置对象，表示事件的属性，该参数可选。除了继承 `Event` 接口的配置属性外，还可以是下面的属性，属性可选。
	- `lengthComputable`：布尔值，表示加载的总量是否可以计算，默认是 `false`。
	- `loaded`：整数，表示已经加载的量，默认是 `0`。
	- `total`：整数，表示需要加载的总量，默认是 `0`。

进度相关的事件都是继承自 `ProgressEvent` 接口的，该接口用来描述外部资源加载的进度。而浏览器原生提供 `ProgressEvent()` 构造函数来生成事件实例。如下所示：

```javascript
let event = new ProgressEvent(type, options);
```

而构造函数中接收的两个参数：第一个参数是字符串，表示事件的类型，这个参数是必须的；第二个参数是配置对象，表示事件的属性，该参数可选。配置对象除了可以使用 `Event` 接口的配置属性，还可以使用下面的属性，所有这些属性都是可选的。

- `lengthComputable`：布尔值，表示加载的总量是否可以计算，默认是 `false`。
- `loaded`：整数，表示已经加载的量，默认是 `0`。
- `total`：整数，表示需要加载的总量，默认是 `0`。

### 属性

`ProgressEvent` 除了从 `Event` 继承的属性与方法外，还有三个与构造函数中对应的三个属性。

- `ProgressEvent.lengthComputable`：只读属性，是一个 `Boolean` 标志，指示基础流程是否可以计算总工作量和已经完成的工作量。换句话说，它告诉进度是否可测量。
- `ProgressEvent.loaded`：只读属性，表示一个 `unsigned long long`，代表基础流程已经完成的工作量。可以通过属性和 `ProgressEvent.total` 来计算完成的工作量。使用 HTTP 下载资源时，这仅代表内容本身的一部分，而不代表头和其它开销。
- `ProgressEvent.total`：只读属性，表示一个 `unsigned long long`，表示基础流程正在执行的工作总量。使用 HTTP 下载资源时，这仅代表内容本身，而不代表头和其它开销。

如果 `ProgressEvent.lengthComputable` 为 `false`，`ProgressEvent.total` 实际上是没有意义的。

# XMLHttpRequest Level 2

`XMLHttpRequest Level 2` 是在 `XMLHttpRequest Level 1` 的基础上进一步的扩展。但并非所有浏览器都实现了 `XMLHttpRequest Level 2` 的所有功能，也可能只实现了部分功能。

### FormData

在 `XMLHttpRequest Level 2` 中新增了 `FormData` 类型，主要用于将数据编译成键值对，使用`XMLHttpRequest`来发送数据。也用于创建与表单类似格式的数据。创建一个`FormData`对象，如下所示：

```javascript
let data = new FormData();
data.append("username", "Clark");
data.append("account", "addie");


let request = new XMLHttpRequest():
request.open("post", "http://example.com/submitform.jsp");
request.send(data);
```

`append()` 方法接收两个参数：键，相当于表单字段名；值，相当于表单字段名的值。当然，也可以直接给 `FormData` 构造函数传入表单元素，也可以将表单中的数据作为键值对填充进去，也可以附加额外的数据到`FormData`对象中：

```javascript
let form = document.querySelector("form");
let data = new FormData(form);

data.append("serialnumber", 134);
```

而使用 `FormData`之后，可以不再需要给 `XMLHttpRequest`对象显式地设置任何请求头部。`XMLHttpRequest`对象能够识别作为 `FormData`对象传入的数据类型并自动配置相应的头部。

### timeout

`XMLHttpRequest.timeout` 属性是一个表示发送数据后等待的毫秒数的一个整数，响应请求时间超出该属性设置的毫秒数，就会中断该请求。IE8 给 `XMLHttpRequest` 添加了该属性，并在 `XMLHttpRequest Level 2` 中添加了该特性。默认值为 `0`，当超时时，就会触发 `timeout` 事件。如下所示：

```javascript
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
            console.log(xhr.responseText);
        } else {
            console.log("请求失败：" + xhr.status);
        }
    }
};
xhr.ontimeout = function() {
    console.log("请求超时。" );
};
xhr.open("get", "http://localhost:8088/servlets/ajaxGet", true);
xhr.timeout = 1000; // 1秒超时
xhr.send(null);
```

上面的例子演示了设置超时`timout`属性值，超时后请求中断触发`ontimeout`事件，并将 `status` 属性值置 `0`。但`readyState`属性值仍为 `4`，因此也会调用`onreadystatechange` 事件。

### upload

`XMLHttpRequest.upload` 属性会返回一个 `XMLHttpRequestUpload` 对象，代表上传进度。这个对象不透明，但作为一个 `XMLHttpRequestEventTarget`，可以通过对其绑定事件来追踪它的进度。

`XMLHttpRequest.upload` 属性可以监听 `loadstart/progress/abort/error/load/timeout/loadend` 这几种事件。

```html
<body>
<progress id="uploadprogress" min="0" max="100" value="0">0</progress> <br>
<input type="file" id="upload_files" /> <br>
<button onclick="upload()">上传</button>
</body>
<script>
    function upload() {
        let resource = document.getElementsByTagName('input')[0].files[0];
        // 此处进行 ajax 上传
        let xhr = new XMLHttpRequest();
        // 预置的 formdata 对象，详情查看 ajax level 2,
        let data = new FormData();
        data.append("resource", resource);
        xhr.open("post", "http://localhost:8088/servlets/upload");
        xhr.onload = function() {
            if (xhr.status === 200) {
                console.log("上传成功");
            } else {
                console.log("上传出错");
            }
        };
        xhr.onerror = function(e) {
            console.error('An error occurred while submitting the form. Maybe your file is too big');
        };
        // 上传进度事件
        xhr.upload.addEventListener('progress', function (ev) {
           if (ev.lengthComputable) {
               let complete = (ev.loaded / ev.total * 100 | 0);
               let progress = document.getElementById('uploadprogress');
               progress.value = complete;
           }
        });
        xhr.send(data);
    }
</script>
```

当进行上传操作时，如果要跨域上传，需要在服务器设置 CORS。

跨源资源共享（CORS，Cross-Origin Resource Sharing）定义浏览器与服务器如何实现跨源通信。使得 `XMLHttpRequest` 请求克服了同源使用的限制。简单的授权访问，在服务器设置请求头：

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE
Access-Control-Max-Age: 3600
Access-Control-Allow-Headers: Content-Type,X-Requested-With,accept,Origin,Access-Control-Request-Method,Access-Control-Request-Headers
```

### overrideMimeType()

`XMLHttpRequest.overrideMimeType()` 方法用来重写 `XMLHttpRequest` 响应的 `MIME` 类型。是 Firefox 首次引入，在 `XMLHttpRequest Level 2` 中添加了该方法。因为响应返回的 `MIME` 类型决定了 `XMLHttpRequest` 对象如何处理响应。例如服务器实际发送的是 `XML`数据，但响应头中`MIME`类型设置的是`text/plain`，浏览器不会自动解析，`responseXML`属性值是`null`。此时调用`overrideMimeType()` 可以保证将响应当成 `XML` 而不是纯文本来处理：

```javascript
let xhr = new XMLHttpRequest();
xhr.open("get", "example.jsp", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

注意：为了正确覆盖响应的 `MIME` 类型，该方法必须在 `send()` 方法之前调用。

# 小结

Ajax 是无需刷新当前页面即可从服务器获取数据的一种方法，其中核心对象是 `XMLHttpRequest`，最早由微软发明，并在 IE5 中引入。之后，其它浏览器都实现了该对象。W3C 虽然也引入了 Web 标准。虽然不同浏览器有些差异，但基本使用是相对较规范的，因此可以放心使用。

> 文章首发于微信公众号「海人的博客」