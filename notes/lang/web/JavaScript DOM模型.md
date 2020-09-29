`DOM`（文档对象模型）是针对HTML和XML文档的一个API（应用程序编程接口）。DOM描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。DOM脱胎与Netscape及微软公司创始的DHTML（动态HTML），但现在它已经成为表现和操作页面标记的真正的跨平台、语言中立的方式。

## 节点层次

`DOM`可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类型，每种类型分别表示文档中不同的信息及（或）标记。每个结点都拥有各自的特点、数据和方法，另外也与其它节点存在某种关系。结点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点为根节点存的树形结构。以下面的HTML为例：

```html
<html>
    <head>
        <title>Sample Page</title>
    </head>
    <body>
        <p>Hello World!</p>
    </body>
</html>
```

可以将这个简单的HTML文档表示为一个层次结构。

文档节点是每个文档的根节点。在这个例子中，文档节点只有一个子节点，即`<html>`元素，我们称之为文档元素。文档元素是文档的最外层元素，文档中的其它所有元素都包含在文档元素中。每个文档只能有一个文档元素。在HTML页面中，文档元素始终都是`<html>`元素。在XML中，没有预定义的元素，因此任何元素都可能成为文档元素。

## Node类型

浏览器提供一个原生的节点对象`Node`，

DOM1级定义了一个Node接口，该接口将由DOM中的所有节点类型实现。这个Node接口在JavaScript中是作为Node类型实现的；除了IE之外，在其它所有浏览器中都可以访问到这个类型。JavaScript中的所有节点类型都继承自Node类型，因此所有节点类型都共享着相同的基本属性和方法。

每个节点都有一个nodeType属性，用于表明节点的类型。节点类型由在Node类型中定义的下列12个数值常量来表示，任何节点类型必居其一：

Node.ELEMENT_NODE(1)；
Node.ATTRIBUTE_NODE(2)；
Node.TEXT_NODE(3)；
Node.CDATA_SECTION_NODE(4)；
Node.ENTITY_REFERENCE_NODE(5)；
Node.ENTITY_NODE(6)；
Node.PROCESSING_INSTRUCTION_NODE(7)；
Node.COMMENT_NODE(8)；
Node.DOCUMENT_NODE(9)；
Node.DOCUMENT_TYPE_NODE(10)；
Node.DOCUMENT_FRAGMENT_NODE(11)；
Node.NOTATION_NODE(12)。

通过比较上面这些常量，可以很容易地确定节点的类型，例如：

```javascript
if (someNode.nodeType == Node.ELEMENT_NODE) {    // 在IE中无效
    alert("Node is an element");
}
```

这个例子比较了 `someNode.nodeType` 与 `Node.ELEMENT_NODE` 常量。如果二者相等，则意味着`someNode`确实是一个元素。然而，由于IE没有公开Node类型的构造函数，因此上面的代码在IE中会导致错误。为了确保跨浏览器兼容，最好还是将`nodeType`属性与数字值进行比较，如下所示：

```javascript
if (someNode.nodeType == 1) {  // 适用于所有浏览器
    alert("Node is an element.");
}
```

并不是所有节点类型都收到Web浏览器的支持。开发人员做常用的就是元素和文本节点。

**1.nodeName和nodeValue属性**

要了解节点的具体信息，可以使用nodeName和nodeValue这两个属性。这两个属性的值完全取决于节点的类型。在使用这两个值以前，最好像下面这样先检测一下节点的类型。

```javascript
if (someNode.nodeType == 1) {
    value = someNode.nodeName;	// nodeName的值是元素的标签名
}
```

在这个例子中，首先检查节点类型，看它是不是一个元素。如果是，则取得并保存nodeName的值。对于元素节点，nodeName中保存的始终都是元素的标签名，而nodeValue的值则始终为null。

**2.节点关系**

文档中所有的节点之间都存在这样或那样的关系。节点间的各种关系可以用传统的家族关系来描述，相当于把文档树比喻成家谱。在HTML 中，可以将`<body>`元素看成是`<html>`元素的子元素；相应地，也就可以将`<html>`元素看成是`<body>`元素的父元素。而`<head>`元素，则可以看成是`<body>`元素的同胞元素，因为它们都是同一个父元素`<html>`的直接子元素。

每个节点都有一个childNodes 属性，其中保存着一个NodeList 对象。NodeList 是一种类数组对象，用于保存一组有序的节点，可以通过位置来访问这些节点。请注意，虽然可以通过方括号语法来访问NodeList 的值，而且这个对象也有length 属性，但它并不是Array 的实例。NodeList 对象的独特之处在于，它实际上是基于DOM 结构动态执行查询的结果，因此DOM结构的变化能够自动反映在NodeList 对象中。我们常说，NodeList 是有生命、有呼吸的对象，而不是在我们第一次访问它们
的某个瞬间拍摄下来的一张快照。

下面的例子展示了如何访问保存在NodeList 中的节点——可以通过方括号，也可以使用item()方法。

```javascript
var firstChild = someNode.childNodes[0];
var secondChild = someNode.childNodes.item[1];
var count = someNode.childNodes.length;
```

无论使用方括号还是使用`item()`方法都没有问题，但使用方括号语法看起来与访问数组相似，因此颇受一些开发人员的青睐。另外，要注意length 属性表示的是访问NodeList 的那一刻，其中包含的节点数量。我们在本书前面介绍过，对arguments 对象使用Array.prototype.slice()方法可以将其转换为数组。而采用同样的方法，也可以将NodeList 对象转换为数组。来看下面的例子：

```javascript
// 在IE8及之前版本中无效
var arrayOfNodes = Array.prototype.slice.call(someNode.childNodes, 0);
```

除了IE8及更早版本之外，这行代码能在任何浏览器中运行。由于IE8及更早版本将NodeList实现为一个COM对象，而我们不能像使用JScript对象那样使用这种对象，因此上面的代码会导致错误。要想在IE中将NodeList转换为数组，必须手动枚举所有成员。下列代码在所有浏览器中都可以运行：

```javascript
function convertToArray(nodes) {
    var array = null;
    try {
        array = Array.prototype.slice.call(node, 0);    // 针对非IE浏览器
    } catch (ex) {
        array = new Array();
        for (var i = 0, len = nodes.length; i < len; i++) {
            array.push(nodes[i]);
        }
    }
    return array;
}
```

这个 convertToArray() 函数首先尝试了创建数组的最简单方式。如果导致了错误（说明是在IE8及更早版本中），则通过try-catch块来捕获错误，然后手动创建数组。这是以一种检测怪癖的形式。

每个节点都有一个parentNode 属性，该属性指向文档树中的父节点。包含在childNodes 列表中的所有节点都具有相同的父节点，因此它们的parentNode 属性都指向同一个节点。此外，包含在childNodes 列表中的每个节点相互之间都是同胞节点。通过使用列表中每个节点的previousSibling和nextSibling 属性，可以访问同一列表中的其他节点。列表中第一个节点的previousSibling 属性值为null，而列表中最后一个节点的nextSibling 属性的值同样也为null，如下面的例子所示：

```javascript
if (someNode.nextSibling === null) {
    alert("Last node in the parent's childNodes list.");
} else if (someNode.previousSibling === null) {
    alert("First node in the parent's childNodes list.");
}
```

当然，如果列表中只有一个节点，那么该节点的nextSibling 和previousSibling 都为null。

父节点与其第一个和最后一个子节点之间也存在特殊关系。父节点的firstChild 和lastChild属性分别指向其childNodes 列表中的第一个和最后一个节点。其中，someNode.firstChild 的值始终等于someNode.childNodes[0] ， 而someNode.lastChild 的值始终等于someNode.childNodes [someNode.childNodes.length-1]。在只有一个子节点的情况下，firstChild 和lastChild 指向同一个节点。如果没有子节点，那么firstChild 和lastChild 的值均为null。明确这些关系能够对我们查找和访问文档结构中的节点提供极大的便利。图10-2 形象地展示了上述关系。

在反映这些关系的所有属性当中，childNodes 属性与其他属性相比更方便一些，因为只须使用简单的关系指针，就可以通过它访问文档树中的任何节点。另外，hasChildNodes()也是一个非常有用的方法，这个方法在节点包含一或多个子节点的情况下返回true；应该说，这是比查询childNodes列表的length 属性更简单的方法。

所有节点都有的最后一个属性是ownerDocument，该属性指向表示整个文档的文档节点。这种关系表示的是任何节点都属于它所在的文档，任何节点都不能同时存在于两个或更多个文档中。通过这个属性，我们可以不必在节点层次中通过层层回溯到达顶端，而是可以直接访问文档节点。

> 虽然所有节点类型都继承自Node，但并不是每种节点都有子节点。
> 

**3.操作节点**

因为关系指针都是只读的，所以DOM 提供了一些操作节点的方法。其中，最常用的方法是appendChild()，用于向childNodes 列表的末尾添加一个节点。添加节点后，childNodes 的新增节点、父节点及以前的最后一个子节点的关系指针都会相应地得到更新。更新完成后，appendChild()返回新增的节点。来看下面的例子：

```javascript
var returnedNode = someNode.appendChild(newNode);
alert(returnedNode == newNode);    // true
alert(someNode.lastChild == newNode);    // true
```

如果传入到appendChild()中的节点已经是文档的一部分了，那结果就是将该节点从原来的位置转移到新位置。即使可以将DOM树看成是由一系列指针连接起来的，但任何DOM节点也不能同时出现在文档中的多个位置上。因此，如果在调用appendChild()时传入了父节点的第一个子节点，那么该节点就会成为父节点的最后一个子节点，如下面的例子所示。

```javascript
// someNode 有多个子节点
var returnedNode = someNode.appendChild(someNode.firstChild);
alert(returnedNode == someNode.firstChild);    // false
alert(returnedNode == someNode.lastChild);    // true
```

如果需要把节点放在 childNodes 列表中某个特定的位置上，而不是放在末尾，那么可以使用 insertBefore() 方法。这个方法接受两个参数：要插入的节点和作为参照的节点。插入节点后，被插入的节点会变成参照节点的前一个同胞节点（previousSibling），同时被方法返回。如果参照节点是null，则insertBefore()与appendChild()执行相同的操作，如下面的例子所示。

```javascript
// 插入后成为最后一个子节点
returnedNode = someNode.insertBefore(newNode, null);
alert(newNode == someNode.lastChild);    // true
// 插入后成为第一个子节点
var returnedNode = someNode.insertBefore(newNode, someNOde.firstChild);
alert(returnedNode == newNode);    // true
alert(newNode == someNode.firstChild);    // true
// 插入到最后一个子节点前面
returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
alert(newNode == someNode.childNodes[someNode.childNodes.length-2]);    // true
```

前面介绍的appendChild()和insertBefore()方法都只插入节点，不会移除节点。而下面要介绍的replaceChild()方法接受的两个参数是：要插入的节点和要替换的节点。要替换的节点将由这个方法返回并从文档树中被移除，同时由要插入的节点占据其位置。来看下面的例子。

```javascript
// 替换第一个子节点
var returnedNode = someNode.replaceChild(newNode, someNode.firstChild);
// 替换最后一个子节点
returnedNode = someNode.replaceChild(newNode, someNode.lastChild);
```

在使用replaceChild()插入一个节点时，该节点的所有关系指针都会从被它替换的节点复制过来。尽管从技术上讲，被替换的节点仍然还在文档中，但它在文档中已经没有了自己的位置。

如果只想移除而非替换节点，可以使用removeChild()方法。这个方法接受一个参数，即要移除的节点。被移除的节点将成为方法的返回值，如下面的例子所示。

```javascript
// 移除第一个子节点
var formerFirstChild = someNode.removeChild(someNode.firstChild);
// 移除最后一个子节点
var formerLastChild = someNode.removeChild(someNode.lastChild);
```

与使用replaceChild()方法一样，通过removeChild()移除的节点仍然为文档所有，只不过在文档中已经没有了自己的位置。

前面介绍的四个方法操作的都是某个节点的子节点，也就是说，要使用这几个方法必须先取得父节点（使用parentNode 属性）。另外，并不是所有类型的节点都有子节点，如果在不支持子节点的节点上调用了这些方法，将会导致错误发生。

**4.其它方法**

有两个方法是所有类型的结点都有的。第一个就是cloneNode()，用于创建调用这个方法的节点的一个完全相同的副本。cloneNode()方法接受一个布尔值参数，表示是否执行深复制。在参数为true的情况下，执行深复制，也就是复制节点及其整个子节点树；在参数为false 的情况下，执行浅复制，即只复制节点本身。复制后返回的节点副本属于文档所有，但并没有为它指定父节点。因此，这个节点副本就成为了一个“孤儿”，除非通过appendChild()、insertBefore()或replaceChild()将它添加到文档中。例如，假设有下面的HTML代码。

```html
<ul>
    <li>item 1</li>
    <li>item 2</li>
    <li>item 3</li>
</ul>
```

如果我们已经将`<ul>`元素的引用保存在了变量myList中，那么通常下列代码就可以看出使用`cloneNode()`方法的两种模式。

```javascript
var deepList = myList.cloneNode(true);
alert(deepList.childNodes.length);    // 3(IE<9)或7(其它浏览器)
var shallowList = myList.cloneNode(false);
alert(shallowList.childNodes.length);    // 0
```

在这个例子中，`deepList`中保存着一个对myList执行深复制得到的副本。因此，deepList中包含3个列表项，每个列表项中都包含文本。而变量shallowList中保存着对myList执行浅复制得到的副本，因此它不包含子节点。deepList.childNodes.length中的差异主要是因为IE8及更早版本与其它浏览器处理空白字符的方式不一样。IE9之前的版本不会为空白符创建节点。

> cloneNode()方法不会复制添加到DOM节点中的JavaScript属性，例如事件处理程序等。这个方法只复制特性、（在明确指定的情况下也复制）子节点，其它一切都不会复制。IE在此存在一个bug，即它会复制事件处理程序，所以我们建议在复制之前最好先移除事件处理程序。
> 

## Document类型

JavaScript通过Document类型表示文档。在浏览器中，document对象是HTMLDocument（继承自Document类型）的一个实例，表示整个HTML页面。而且，document对象是window对象的一个属性，因此可以将其作为全局对象来访问。Document节点具有下列特征：

nodeType 的值为9；
nodeName 的值为"#document"；
nodeValue 的值为null；
parentNode 的值为null；
ownerDocument 的值为 null；
其子节点可能是一个DocumentType（最多一个）、Element（最多一个）、ProcessingInstruction或Comment。

**1.文档的子节点**

虽然DOM标准规定Document节点的子节点可以是DocumentType、Element、ProcessingInstruction或Comment，但还有两个内置的访问其子节点的快捷方式。第一个就是documentElement属性，该属性始终指向HTML页面中的`<html>`元素。另一个就是通过childNodes列表访问元素，但通过documentElement属性则能更快捷、更直接地访问该元素。以下面这个简单的页面为例。

```html
<html>
    <body>
    </body>
</html>
```

这个页面在经过浏览器解析后，其文档中只包含一个子节点，即`<html>`元素。可以通过documentElement或childNodes列表来访问这个元素，如下所示。

```javascript
var html = document.documentElement;    // 取得对<html>的引用
alert(html === document.childNodes[0]);    // true
alert(html === document.firstChild);    // true
```

这个例子说明，documentElement、firstChild和childNodes[0]的值相同，都指向`<html>`元素。

作为HTMLDocument 的实例，document 对象还有一个body 属性，直接指向`<body>`元素。因为开发人员经常要使用这个元素，所以document.body 在JavaScript 代码中出现的频率非常高，其用法如下。

```javascript
var body = document.body; //取得对<body>的引用
```

所有浏览器都支持document.documentElement 和document.body 属性。

Document 另一个可能的子节点是DocumentType。通常将<!DOCTYPE>标签看成一个与文档其他部分不同的实体，可以通过doctype 属性（在浏览器中是document.doctype）来访问它的信息。

```javascript
var doctype = document.doctype; //取得对<!DOCTYPE>的引用
```

浏览器对document.doctype 的支持差别很大，可以给出如下总结。

- IE8 及之前版本：如果存在文档类型声明，会将其错误地解释为一个注释并把它当作Comment节点；而document.doctype 的值始终为null。
- IE9+及Firefox：如果存在文档类型声明，则将其作为文档的第一个子节点；document.doctype是一个DocumentType 节点，也可以通过document.firstChild 或document.childNodes[0]访问同一个节点。
- Safari、Chrome 和Opera：如果存在文档类型声明，则将其解析，但不作为文档的子节点。document.doctype 是一个DocumentType 节点，但该节点不会出现在document.childNodes 中。

由于浏览器对document.doctype的支持不一致，因此这个属性的用处很有限。

从技术上说，出现在`<html>`元素外部的注释应该算是文档的子节点。然而，不同的浏览器在是否解析这些注释以及能否正确处理它们等方面，也存在很大差异。以下面简单的HTML页面为例。

```html
<!--第一条注释-->
<html>
    <body>
    </body>
</html>
<!--第二条注释-->
```

看起来这个页面应该有3个子节点：注释、`<html>`元素、注释。从逻辑上讲，我们会认为document.childNodes中应该包含与这3个节点对应的3项。但是，现实中的浏览器在处理位于`<html>`外部的注释方面存在如下差异。

- IE8 及之前版本、Safari 3.1 及更高版本、Opera 和Chrome 只为第一条注释创建节点，不为第二条注释创建节点。结果，第一条注释就会成为document.childNodes 中的第一个子节点。
- IE9 及更高版本会将第一条注释创建为document.childNodes 中的一个注释节点，也会将第二条注释创建为document.childNodes 中的注释子节点。
- Firefox 以及Safari 3.1 之前的版本会完全忽略这两条注释。

同样，浏览器间的这种不一致性也导致了位于`<html>`元素外部的注释没有什么用处。

多数情况下，我们都用不着在document 对象上调用appendChild()、removeChild()和replaceChild()方法，因为文档类型（如果存在的话）是只读的，而且它只能有一个元素子节点（该节点通常早就已经存在了）。

**2.文档信息**

作为HTMLDocument 的一个实例，document 对象还有一些标准的Document 对象所没有的属性。这些属性提供了document 对象所表现的网页的一些信息。其中第一个属性就是title，包含着`<title>`元素中的文本——显示在浏览器窗口的标题栏或标签页上。通过这个属性可以取得当前页面的标题，也可以修改当前页面的标题并反映在浏览器的标题栏中。修改title 属性的值不会改变`<title>`元素。来看下面的例子。

```javascript
// 取得文档标题
var originalTitle = document.title;
// 设置文档标题
document.title = "New page title";
```

接下来要介绍的3个属性都与对网页的请求有关，它们是URL、domain和referrer。URL属性中包含页面完整的URL（即地址栏中显示的URL），domain属性只包含页面的域名，而referrer属性中则保存着链接到当前页面的那个页面的URL。在没有来源页面的情况下，referrer属性中可能会包含空字符串。所有这些信息都存在于请求的HTTP头部，只不过是通过这些属性让我们能够在JavaScript中访问它们而已，如下面的例子所示。

```javascript
// 取得完整的URL
var url = document.URL;
// 取得域名
var domain = document.domain;
// 取得来自页面的URL
var referrer = document.referrer;
```

URL与domain属性是相互关联的。例如，如果document.URL等于http://www.wrox.com/WileyCDA/  ，那么document.domain就等于 www.wrox.com 。

在这3个属性中，只有domain是可以设置的。但由于安全方面的限制，也并非可以给domain设置任何值。如果URL中包含一个子域名，例如p2p.wrox.com，那么就只能将domain设置为"wrox.com"（URL中包含"www"，如www.wrox.com 时，也是如此）。不能将这个属性设置为URL中不包含的域，如下面的例子所示。

```javascript
// 假设页面来自p2p.wrox.com域
document.domain = "wrox.com";	// 成功
document.domain = "nczonline.net";	// 出错！
```

当页面中包含来自其他子域的框架或内嵌框架时，能够设置document.domain 就非常方便了。由于跨域安全限制， 来自不同子域的页面无法通过JavaScript 通信。而通过将每个页面的document.domain 设置为相同的值，这些页面就可以互相访问对方包含的JavaScript 对象了。例如，假设有一个页面加载自www.wrox.com，其中包含一个内嵌框架，框架内的页面加载自p2p.wrox.com。 由于document.domain 字符串不一样，内外两个页面之间无法相互访问对方的JavaScript 对象。但如
果将这两个页面的document.domain 值都设置为"wrox.com"，它们之间就可以通信了。

浏览器对domain 属性还有一个限制，即如果域名一开始是“松散的”（loose），那么不能将它再设置为“紧绷的”（tight）。换句话说，在将document.domain 设置为"wrox.com"之后，就不能再将其设置回"p2p.wrox.com"，否则将会导致错误，如下面的例子所示。

```javascript
// 假设页面来自 p2p.wrox.com域
document.domain = "wrox.com";		// 松散的（成功）
document.domain = "p2p.wrox.com";	// 紧绷的（出错！）
```

所有浏览器中都存在这个限制，但IE8是实现这一限制的最早的IE版本。

**3.查找元素**

说到最常见的DOM应用，恐怕就要数取得特定的某个或某组元素的引用，然后再执行一些操作了。取得元素的操作可以使用document对象的几个方法来完成。其中，Document类型为此提供了两个方法：getElementById()和getElementsByTagName()。

第一个方法，getElementById()，接收一个参数：要取得的元素的ID。如果找到相应的元素则返回该元素，如果不存在带有相应ID 的元素，则返回null。注意，这里的ID 必须与页面中元素的id特性（attribute）严格匹配，包括大小写。以下面的元素为例。

```html
<div id="myDiv">Some text</div>
```

可以使用下面的代码取得这个元素：

```javascript
var div = document.getElementBYOd("myDiv");    // 取得<div>元素的引用
```

但是，下面的代码在除IE7及更早版本之外的所有浏览器中都将返回null。

```javascript
var div = document.getElementById("mydiv");    // 无效的ID（在IE7及更早版本中可以）
```

IE8及较低版本不区分ID的大小写，因此"myDiv"和"mydiv"会被当作相同的元素ID。

如果页面中多个元素的ID 值相同，getElementById()只返回文档中第一次出现的元素。IE7 及较低版本还为此方法添加了一个有意思的“怪癖”：name 特性与给定ID 匹配的表单元素（`<input>`、`<textarea>`、`<button>`及`<select>`）也会被该方法返回。如果有哪个表单元素的name 特性等于指定的ID，而且该元素在文档中位于带有给定ID 的元素前面，那么IE 就会返回那个表单元素。来看下面的例子。

```html
<input type="text" name="myElement" value="Text field">
<div id="myElement">A div</div>
```

基于这段HTML代码，在IE7中调用document.getElementById("myElement")，结果会返回`<input>`元素；而在其他所有浏览器中，都会返回对`<div>`元素的引用。为了避免IE 中存在的这个问题，最好的办法是不让表单字段的name 特性与其他元素的ID 相同。

另一个常用于取得元素引用的方法是getElementsByTagName()。这个方法接受一个参数，即要取得元素的标签名，而返回的是包含零或多个元素的NodeList。在HTML 文档中，这个方法会返回一个HTMLCollection 对象，作为一个“动态”集合，该对象与NodeList 非常类似。例如，下列代码
会取得页面中所有的`<img>`元素，并返回一个HTMLCollection。

```javascript
var images = document.getElementsByTagName("img");
```

这行代码会将一个HTMLCollection对象保存在images变量中。与NodeList对象类似，可以使用方括号语法或item()方法来访问HTMLCollection对象中的项。而这个对象中元素的数量则可以通过其length属性取得，如下面的例子所示。

```javascript
alert(images.length);         //输出图像的数量
alert(images[0].src);         //输出第一个图像元素的src特性
alert(images.item[0].src);    //输出第一个图像元素的src特性
```

HTMLCollection对象还有一个方法，叫做namedItem()，使用这个方法可以通过元素的name特性取得集合中的项。例如，假设上面提到的页面中包含如下`<img>`元素：

```html
<img src="myimage.gif" name="myImage">
```

那么就可以通过如下方式从images变量中取得这个`<img>`元素：

```javascript
var myImage = images.namedItem("myImage");
```

在提供按索引访问项的基础上，HTMLCollection还支持按名称访问项，这就为我们取得实际想要的元素提供了便利。而且，对命名的项也可以使用方括号语法来访问，如下所示：

```javascript
var myImage = images["myImage"];
```

对HTMLCollection而言，我们可以向方括号中传入数值或字符串形式的索引值。在后台，对数值索引就会调用item()，而对字符串索引就会调用namedItem()。

要想取得文档中的所有元素，可以向getElementsByTagName()中传入`"*"`。在JavaScript及CSS中，星号（`*`）通常表示"全部"。下面看一个例子。

```javascript
var allElements = document.getElementsByTagName("*");
```

仅此一行代码返回的HTMLCollection 中，就包含了整个页面中的所有元素——按照它们出现的先后顺序。换句话说，第一项是`<html>`元素，第二项是`<head>`元素，以此类推。由于IE 将注释（Comment）实现为元素（Element），因此在IE 中调用`getElementsByTagName("*")`将会返回所有注释节点。

> 虽然标准规定标签名需要区分大小写，但为了最大限度地与既有HTML页面兼容，传给getElementsByTagName()的标签名是不需要区分大小写的。但对于XML页面而言（包括XHTML），getElementsByTagName()方法就会区分大小写。
> 

第三个方法，也是只有HTMLDocument 类型才有的方法，是getElementsByName()。顾名思义，这个方法会返回带有给定name 特性的所有元素。最常使用getElementsByName()方法的情况是取得单选按钮；为了确保发送给浏览器的值正确无误，所有单选按钮必须具有相同的name 特性，如下面的例子所示。

```html
<fieldset>
    <legend>Which color do you prefer?</legend>
    <ul>
        <li><input type="radio" value="red" name="color" id="colorRed">
            <label for="colorRed">Red</label></li>
        <li><input type="radio" value="green" name="color" id="colorGreen">
            <label for="colorGreen">Green</label></li>
        <li><input type="radio" value="blue" name="color" id="colorBlue">
            <label for="colorBlue">Blue</label></li>
</ul>
</fieldset>
```

如这个例子所示，其中所有单选按钮的name特性值都是"color"，但它们的ID可以不同。ID的作用在于将`<label>`元素应用到每个单选按钮，而name特性则用以确保三个值只有一个被发送给浏览器。这样，我们就可以使用如下代码取得所有单选按钮：

```javascript
var radios = document.getElementsByName("color");
```

与getElementsByTagName()类似，getElementsByName()方法也会返回一个HTMLCollection。但是，对于这里的单选按钮来说，namedItem()方法则只会取得第一项（因为每一项的name特性都相同）。