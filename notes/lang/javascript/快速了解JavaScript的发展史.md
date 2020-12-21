JavaScript 是一门可以运行在浏览器上的跨平台的脚本语言，HTML + CSS 可以实现页面的布局，但也只是一个页面，要想进行网页交互，还得需要 JavaScript。

而平常说的 JavaScript ，它是有三部分组成的：

- ECMAScript，由 `ECMA-262`定义并提供核心功能的脚本语言标准。
- 文档对象模型（DOM），提供与网页交互的方法和接口。
- 浏览器对象模型（BOM），提供与浏览器交互的方法和接口。

这里要想了解 ECMAScript，就不得不提 JavaScript 的发展史了。

## 发明 JavaScript

在 1993 年，美国的 NCSA 发明了名为 Mosaic 的浏览器，在当时大受欢迎。其中参与开发该浏览器的 Marc Andreessen 和 Jim Clark 在 1994 年4 月成立了 Mosaic 公司，而由于没有 Mosaic 的商标版权，因此必须要重写浏览器代码，同年 10月，才发布了 Mosaic Netscape 0.9 测试版，成为当时最热门的浏览器。

在 1994年 10月，为避免商标拥有权问题，公司更名为 Netscape Communications Corporation（简称网景）。同年 12月，Netscape Navigator 1.0 才正式发布。而这时的浏览器只能浏览，提交表单的时候浏览器并不知道是否提交，每次都向服务器发起请求，由服务器来判断。

在 1995年 4月，Netscape 录用了 Brendan Eich 工程师，并让它负责为即将发布的 Netscape Navigator 2 设计一个 Mocha 脚本语言，同年 9月更名为 LiveScript，开发该脚本语言是想在客户端和服务端都使用它。同年，Sun 公司发布了 Java 语言，并推向市场。Netscape 为赶上这波热度，同年12月，与 Sun 公司结盟，共同开发 LiveScript，并在 Netscape Navigator 2 发布前，将 LiveScript 改名为 JavaScript，搭上了媒体热炒 Java 的顺风车。

## ECMAScript 的由来

在 1996年 3月，Netscape Navigator 2 正式内置 JavaScript 脚本语言，同年 8 月， Netscape Navigator 3.0 问世，并发布了 JavaScript 1.1 版本。因为 JavaScript 如此成功，随后不久，微软也加入了进来，发布了 IE3 并搭载了 JScript，一个 JavaScript 的克隆版，开启了两家的浏览器大战，使得 Netscape 面临丧失浏览器脚本主导权的局面。同期的还有 CEnvi 的 ScriptEase，这样形成了三足鼎立。但版本并存且没有相应规范。

Netscape 为了挽回颓势，在 1996 年，将 JavaScript 提交给了欧洲计算机制造商协会（ECMA）。TC39 承担了标准化脚本语言的任务。TC39 由来自 Netscape、Sun、微软、Borland 和 其他对这门脚本有兴趣的公司的工程师组成。在 1997年，发布了 `ECMA-262` 标准，也就是常说的 ECMAScript 脚本语言标准。同年 10月 Netscape navigator 4.0 发布。

1998 年，国际标准化组织（ISO）和国际电工委员会（IEC）也将 ECMAScript 纳入标准。至此以后，各家浏览器均将 ECMAScrpt 作为标准实现自己的 JavaScript。

## ECMAScript 发展

这里将 ECMAScript 简写为 ES。

因为 ECMA 已经推出了脚本标准，以后各家的脚本实现都是依次为标准来实现的，在这之后的发展都要以 ECMA 推出的脚本标准为准。

在 1996 年，ECMA 的 TC39 承担了脚本语言标准的任务，1997 年发布了第一个 ES 标准 `ECMA-262`，也就是我们说的 ECMAScript。这是 ECMAScript 标准的第一个版本。

1998 年 6月，ES 2.0 版发布，主要修改符合 ISO/IEC 16262国际标准的规范。

1999 年 12 月，ES 3.0 版本发布，在这个版本中，添加了正则表达式、switch 控制语句、try-catch 异常处理等还有其它一些特性。该版本得到了广泛支持。

而在 2007 年 10月，ES 4.0 草案对 3.0 版做了大幅度升级，预计是次年发布正式版本。但由于 4.0 过于激进，使得 TC39 中各方产生严重分歧，在 2008 年 10月，中止了 ES 4.0 的开发，将其中的一些功能改善一小部分，发布为 ES 3.1，算是 ES 3.0 的一次小升级，会后不就，ES 3.1 就更名为 ES 5 了。

在 2009 年 12 月，完善了 ES 5.0 并发布了该版本。在这次的更新中，将一些不是很成熟的设想，推迟到将来在考虑，而在该版本中实现了 “strict mode”、getter/setter、JSON库支持和更完整的对象属性等新功能。在 2011 年 6 月，发布了更符合 ISO/IEC 16262:2011 国际标准 ES 5.1 版本。

ES 6 版本也经过了许多的波折，在 2013 年 3 月，ES6 草案冻结，不添加新功能，设想将新功能放在 ES 7。同年 12月，ES 6 草案又发布了，进入 12 个月的讨论期，使得 ES6 在 2015 年 6月能够正式发布。该版本有许多名字，即可以叫 ES6、也可以叫 ES 2015。此次更新增加了许多重要的功能，首先是添加了 `let/const` 两个关键字，用于创建常量和变量；class 关键字提供了Java语言创建类的写法，这只是一个语法糖，只是让对象原型的写法更加清晰、更加面向对象而已；还有模块功能、Promise、字符处理、迭代器和生成器等重要功能。2016 年 6 月，发布的 ES7，也被称为 ES 2016，主要是完善 ES6 的规范，还有一些如 求幂运算符、Array.prototype.includes()等新功能。

在 2017 年 6月，TC39 发布了 ES8 版本，也即 ES 2017。该版本实现了异步函数、共享内存、字符串填充等新功能。在 2018 年 6 月，ES9 发布，即 ES 2018。主要实现了异步迭代器、Object Rest、Spread Properties、Promise.prototype.finally等新功能。在 2019 年，发布了 ES10，即 ES 2019。该版本新增 Array 的 flat() 和 flatMap() 方法、更友好的 JSON.stringify、简化异常处理等新功能。在 2020 年，发布了 ES11，即 ES 2020。该版本新增了String的matchAll方法、动态导入语句 import()、私有变量等新功能。

## 总结

随着上面的介绍，我们已经知道了 ECMAScript 是 JavaScript 的脚本标准化，随着 ES 更新变得逐渐完善，是一门成熟的脚本语言。在开发前端上，是必会的一门语言。前端 Web 浏览器如Chrome、Safari、Opera、Firefox和微软的Edge都有不同程度的支持。基本上都对 ES5 提供了完善的支持，而对 ES6 和 ES7 也在不断提升支持度，而ECMA也会每年发布新版本，逐渐的完善该语言。