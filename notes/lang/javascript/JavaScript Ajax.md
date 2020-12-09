# 概述

Ajax（Asynchronous JavaScript and XML，即异步 JavaScript 和 XML）技术用于与服务器交换数据并刷新部分页面，实现更好的用户体验。

Ajax 的核心对象是 `XMLHttpRequest`，通过 `XMLHttpRequest` 可以在不刷新页面的情况下请求特定 `URL`，获取数据。由微软实现并在 IE5 中支持，用于执行异步网络请求。虽然 Ajax 是 Asynchronous JavaScript+XML的缩写，但Ajax通信与数据格式无关，并不一定是XML格式。甚至支持 HTTP 以外的协议（如 `file://` 和 `FTP`），尽管可能受到更多出于安全等原因的限制。

使用 Ajax 需要几个步骤：

1. 创建 `XMLHttpRequest` 对象。
2. 发出 `HTTP` 请求。
3. 接收服务器传回的数据。
4. 更新网页数据。

总之，Ajax 就是通过 `XMLHttpRequest` 对象发出 `HTTP` 请求，得到服务器响应并处理。

## 创建 XMLHttpRequest

在所有现代浏览器都可以通过 `XMLHttpRequest` 构造函数创建 XHR 对象：

```javascript
let xhr = new XMLHttpRequest();
```

而在老版本 IE5 和 IE6 中使用 `ActiveX` 对象创建：

```javascript
let xhr = new ActiveXObject("Microsoft.XMLHTTP");
```

## 使用 XMLHttpRequest

创建对象后，需要使用 XHR 对象调用 `open(type, url, async)` 方法并设置三个参数：

- type：表示请求类型，如 `"get"` 、`"post"` 等。
- url：表示请求的url。
- async：表示请求是否异步。如 `true`表示异步请求，`false`表示同步请求，它会使JavaScript代码等待服务器响应之后在继续执行。

### GET

最常用的 GET 请求方法，用于查询服务器信息。对于 XHR 对象而言，GET 请求的参数必须正确编码并添加到 URL 后面，然后再给 `open()` 方法。而最常见的错误是格式不对。因此使用 `encodeURIComponent()` 对每个名/值进行编码，并且名/值对必须使用`&`分隔。如下所示：

```javascript
xhr.open("get", "example.jsp?name1=value1&name2=value2", true);
```

可以使用以下函数将查询字符串参数添加到现有的 URL 末尾：

```javascript
function addURLParam(url, name, value) {
    url += (url.indexOf("?") == -1 ? "?" : "&");
    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
    return url;
}
```

可以使用这个函数构建请求 URL，如下面的例子所示：

```javascript
let url = "example.jsp";
// 添加参数
url = addURLParam(url, "name", "Nicholas");
url = addURLParam(url, "book", "Professional JavaScript");
// 初始化请求
xhr.open("get", url, false);
```

这里使用 `addURLParam()` 函数可以保证通过 `XMLHttpRequest` 发送请求的 `URL` 格式正确。

这里的 `example.jsp` 是相对位置，也可以使用绝对 `url`。并且 `open()` 只是为发送请求做准备，而不发送请求。

定义好发送请求后，就可以调用 `send(data)` 方法，将请求发送到服务器。而 `send(data)` 方法要设置一个参数，是作为请求体发送的数据。不需要发送的话，传入`null`。

```javascript
xhr.send(null);
```

### POST

而另一个请求方法就是 `POST` 请求，用于向服务器发送数据。每个 `POST` 请求都应在请求体中携带提交的数据。`POST` 请求体中可以包含非常多且是任意格式的数据。如下例子：

```javascript
xhr.open("post", "example.jsp", true);
```

在传参上，`POST` 请求不用将参数添加在 `URL` 后面，而是使用 `send()` 方法将要发送的数据传入。

默认情况下，对服务器而言，`POST` 请求与`HTML`表单提交是不一样的。服务器逻辑需要读取原始 `POST` 数据才能取得浏览器发送的数据。不过，还可以使用 `XML` 模拟表单提交。为此，第一步需要把 Content-Type 头部设置为 "application/x-www-formurlencoded"，这是提交表单时使用的内容类型。第二步是创建对应格式的字符串。POST 数据此时使用与查询字符串相同的格式。如果网页中确实有一个表单需要序列化并通过 XHR 发送到服务器，则可以使用 `serialize()` 函数来创建相应的字符串，如下所示：

```javascript
function submitData() {
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function() {
        if (xhr.readyState == 4) {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                alert(xhr.responseText);
            } else {
                alert("Request was unsuccessful: " + xhr.status);
            }
        }
    };
    xhr.open("post", "postexample.jsp", true);
    xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    // ID为 user-info 的表单元素
    let form = document.getElementById("user-info");
    xhr.send(serialize(form));
}
```

注意：POST 请求相比 GET 请求要占用更多资源。从性能方面说，发送相同数据的数据，GET请求比 POST 请求快两倍。


## 响应

当服务器收到请求并响应后，XHR 对象会有以下属性被填充数据：

- `responseText`：作为响应体返回的文本。
- `responseXML`：如果响应的内容类型是 `"text/xml"`或`"application/xml"`，那就是包含响应数据的 XML DOM 文档。
- `status`：响应的 HTTP 状态码。
- `stautsText`：响应的 HTTP 状态描述。

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

## 属性和方法

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
### HTTP头部

在 HTTP 的每次请求和响应中都会携带一些头部字段，默认请求下，`XMLHttpRequest` 请求会发送以下头部字段。

- `Accept`：浏览器可以处理的内容类型。
- `Accept-Charset`：浏览器可以显示的字符集。
- `Accept-Encoding`：浏览器可以处理的压缩编码类型。
- `Accept-Language`：浏览器使用的语言。
- `Connection`：浏览器与服务器的连接类型。
- `Cookie`：页面中设置的 `Cookie`。
- `Host`：发送请求的页面所在的域。
- `Referer`：发送请求的页面的 `URI`。
- `User-Agent`：浏览器的用户代理字符串。

`XMLHttpRequest`对象通过一些方法暴露与请求和响应相关的头部字段。

然而 `XMLHttpRequest` 对象可以使用 `setRequestHeader()` 方法发送 HTTP 头部字段，接收两个参数：第一个参数是头部字段名，第二个参数是头部字段值。为保证请求头被发送，必须在 `open()` 之后，`send()` 之前调用。如果多次调用，设同一个字段的值会合并成单一的值发送。如下所示：

```javascript
let xhr = new XMLHttpRequest();
xhr.open("get", "example.jsp", true);
xhr.setRequestHeader("Content-Type", "application/json");
xhr.setRequestHeader("Custom-Header", "Custom-Value");
xhr.send(null);
```

自定义头部要区别于浏览器正常发送的头部，否则会影响服务器正常响应。默认头部在有些浏览器上可以重写。

`getResponseHeader()` 方法可以返回 HTTP 头部信息中指定的字段值，接收的参数为头部名称。也可以使用 `getAllResponseHeaders()` 方法获取所有，返回的可能是如下所示的字符串：

```http
Date: Sun, 14 Nov 2004 18:04:03 GMT
Server: Apache/1.3.29 (Unix)
Vary: Accept
X-Powered-By: PHP/4.3.8
Connection: close
Content-Type: text/html; charset=iso-8859-1
```

通过解析以上头部字段的输出，就可以知道服务器发送的所有头部，而不需要单独去检查了。

## XMLHttpRequest Level 2

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

`XMLHttpRequest.timeout` 属性是一个无符号长整型数，表示发送数据后等待的毫秒数，响应不成功则中断请求。是 IE8 给 `XMLHttpRequest` 对象添加的属性，并在 `XMLHttpRequest Level 2` 中添加了该特性。默认值为 `0`，当超时时，就会触发 `timeout` 事件。如下所示：

```javascript
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        try {
            if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                alert(xhr.responseText);
            } else {
                alert("Request was unsuccessful: " + xhr.status);
            }
        } catch (ex) {
            // 假设由ontimeout 处理
        }
    }
};
xhr.open("get", "timeout.php", true);
xhr.timeout = 1000; // 设置1 秒超时
xhr.ontimeout = function() {
    alert("Request did not return in a second.");
};
xhr.send(null);
```

这个例子演示了使用 `timeout` 设置超时。给 `timeout` 设置 1000 毫秒意味着，如果请求没有在 1 秒钟内返回则会中断。此时则会触发 `ontimeout` 事件处理程序，`readyState` 仍然会变成 4，因此也会调用 `onreadystatechange` 事件处理程序。不过，如果在超时之后访问 `status` 属性则会发生错误。为做好防护，可以把检查 `status` 属性的代码封装在 `try/catch` 语句中。

### overrideMimeType()

`XMLHttpRequest.overrideMimeType()` 方法用来重写 `XMLHttpRequest` 响应的 `MIME` 类型。是 Firefox 首次引入，在 `XMLHttpRequest Level 2` 中添加了该方法。因为响应返回的 `MIME` 类型决定了 `XMLHttpRequest` 对象如何处理响应。例如服务器实际发送的是 `XML`数据，但响应头中`MIME`类型设置的是`text/plain`，浏览器不会自动解析，`responseXML`属性值是`null`。此时调用`overrideMimeType()` 可以保证将响应当成 `XML` 而不是纯文本来处理：

```javascript
let xhr = new XMLHttpRequest();
xhr.open("get", "example.jsp", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

注意：为正确覆盖响应的 `MIME` 类型，该方法必须在 `send()` 方法之前调用。

# 小结

Ajax 是无需刷新当前页面即可从服务器获取数据的一种方法，其中核心对象是 `XMLHttpRequest`，最早由微软发明，并在 IE5 中引入。之后，其它浏览器都实现了该对象。W3C 虽然也引入了 Web 标准。虽然不同浏览器有些差异，但基本使用时相对较规范的，因此可以放心使用。