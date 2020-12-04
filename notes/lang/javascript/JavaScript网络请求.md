
# XMLHttpRequest 对象

IE5 是第一个引入 XHR 对象的浏览器。这个对象是通过 ActiveX 对象实现并包含在 MSXML 库中的。为此，XHR 对象的 3 个版本在浏览器中分别被暴露为 MSXML2.XMLHttp、MSXML2.XMLHttp.3.0 和 MXSML2.XMLHttp6.0。

所有现代浏览器都通过 XMLHttpRequest 构造函数原生支持 XHR 对象：

```javascript
let xhr = new XMLHttpRequest();
```