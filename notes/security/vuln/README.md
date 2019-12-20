# 常见漏洞

```
..toctree::
  :maxdepth: 2
  :caption: Contents:
  
  sql/index
  xss/index
  csrf
  ssrf
  cmdinjection
  pathtraversal
  fileread
  fileupload
  fileinclude
  xxe
  ssti
  xpath
  logic
  config
  middleware/index
  webcache
```



## XSS攻击

​	Xss跨站攻击即跨站脚本攻击，百度百科中介绍如下：用户在浏览网站、使用即时通讯软件、甚至在阅读电子邮件时，通常点击其中的链接。攻击者通过在链接中插入恶意代码，就能够盗取用户信息。攻击者通常会用十六进制(或其它编码方式)将链接编码，以免用户怀疑它的合法性。网站在接收到包含恶意代码的请求之后会产生一个包含恶意代码的页面，而这个页面看起来就像是那个网站应当生成的合法页面一样。

​	许多流行的留言板和论坛程序允许用户发表包含HTML和JavaScript的帖子。假设用户甲发表了一篇包含恶意脚本的帖子，那么用户乙在浏览这篇帖子时，恶意脚本就会执行，盗取用户乙的session信息。