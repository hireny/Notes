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

# DOM操作

浏览器中充斥着隐藏的陷阱和不兼容问题，用JavaScript代码处理DOM的某些部分要比处理其它部分更复杂一些。

## 动态脚本

使用`<script>`元素可以向页面中插入JavaScript代码，一种是通过其`src`特性包含外部文件，另一种方式就是用这个元素本身包含代码。这一节讨论的动态脚本，指的是在页面加载时不存在，但将来的某一时刻通过修改DOM动态添加的脚本。跟操作HTML元素一样，创建动态脚本也有两种方式：插入外部文件和直接插入JavaScript代码。

动态加载的外部JavaScript文件能够立即运行，比如下面的`<script>`元素：

```html
<script type="text/javascript" src="client.js"></script>
```

这个`<script>`元素包含了第九章的客户端检测脚本。而创建这个节点的DOM代码如下所示：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.src = "client.js";
document.body.appendChild(script);
```

显然，这里的DOM代码如实反映了相应的HTML代码。不过，在执行最后一行代码把`<script>`元素添加到页面中之前，是不会下载外部文件的。也可以把这个元素添加到`<head>`元素中，效果相同。整个过程可以使用下面的函数来封装：

```javascript
function loadScript(url) {
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    document.body.appendChild(script);
}
```

然后，就可以通过调用这个函数来加载外部的JavaScript文件了：

```javascript
loadScript("client.js");
```

加载完成后，就可以在页面中的其它地方使用这个脚本了。问题只有一个：怎么知道脚本加载完成呢？遗憾的是，并没有什么标准方式来探知这一点。不过，与此相关的一些事件倒是可以派上用场，但要取决于所用的浏览器。

另一种指定JavaScript代码的方式是行内方式，如下图的例子所示：

```html
<script type="text/javascript">
    function sayHi() {
        console.log("Hi");
    }
</script>
```

从逻辑上讲，下面的DOM代码是有效的：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.appendChild(document.createTextNode("function sayHi(){alert('hi');}"));
document.body.appendChild(script);
```

在Firefox、Safari、Chrome和Opera中，这些DOM代码可以正常运行。但在IE中，则会导致错误。IE将`<script>`视为一个特殊的元素，不允许DOM访问其子节点。不过，可以使用`<script>`元素的`text`属性来指定JavaScript代码，像下面的例子这样：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.text = "function sayHi() {console.log("Hi");}";
document.body.appendChild(script);
```

经过这样修改后的代码可以在IE、Firefox和Safari3及之后版本中运行。Safari3.0之前的版本虽然不能正确地支持`text`属性，但却允许使用文本节点技术来指定代码。如果需要兼容早期版本的Safari，可以使用下列代码：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
var code = "function sayHi(){alert('hi');}";
try {
    script.appendChild(document.createTextNode("code"));
} catch (ex){
    script.text = "code";
}
document.body.appendChild(script);
```

这里，首先尝试标准的DOM文本节点方法，因为除了IE（在IE中会导致抛出错误），所有浏览器都支持这种方式。如果这行代码抛出了错误，那么说明是IE，于是就必须使用`text`属性了。整个过程可以用以下函数来表示：

```javascript
function loadScriptString(code){
    var script = document.createElement("script");
    script.type = "text/javascript";
    try {
        script.appendChild(document.createTextNode(code));
    } catch (ex){
        script.text = code;
    }
    document.body.appendChild(script);
}
```

下面是调用这个函数的示例：

```javascript
loadScriptString("function sayHi() {console.log("Hi");}");
```

以这种方式加载的代码会在全局作用域中执行，而且当脚本执行后将立即可用。实际上，这样执行代码与在全局作用域中把相同的字符串传递给`eval()`是一样的。

## 动态样式

能够把CSS样式包含到HTML页面中的元素有两个。其中，`<link>`元素用于包含来自外部的文件，而`<style>`元素用于指定嵌入的样式。与动态脚本类似，所谓动态样式是指在页面刚加载时不存在的样式；动态样式是在页面加载完成后动态添加到页面中的。

我们以下面这个典型的`<link>`元素为例：

```html
<link rel="stylesheet" type="text/css" href="styles.css">
```

使用DOM代码可以很容易地动态创建出这个元素：

```javascript
var link = document.createElement("link");
link.rel = "stylesheet";
link.type = "text/css";
link.href = "style.css";
var head = document.getElementsByTagName("head")[0];
head.appendChild(link);
```

以上代码在所有主流浏览器中都可以正常运行。需要注意的是，必须将`<link>`元素添加到`<head>`而不是`<body>`元素，才能保证在所有浏览器中的行为一致。整个过程可以用以下函数来表示：

```javascript
function loadStyles(url) {
    var link = document.createElement("link");
    link.rel = "stylesheet";
    link.type = "text/css";
    link.href = url;
    var head = document.getElementsByTagName("head")[0];
    head.appendChild(link);
}
```

调用`loadStyles()` 函数的代码如下所示：

```javascript
loadStyles("styles.css");
```

加载外部样式文件的过程是异步的，也就是加载样式与执行JavaScript代码的过程没有固定的次序。

另一种定义样式的方式是使用`<style>`元素来包含嵌入式CSS，如下所示：

```css
<style type="text/css">
body {
    background-color: red;
}
</style>
```

按照相同的逻辑，下列DOM代码应该是有效的：

```javascript
var style = document.createElement("style");
style.type = "text/css";
style.appendChild(document.createTextNode("body{background-color:red}"));
var head = document.getElementsByTagName("head")[0];
head.appendChild(style);
```

以上代码可以在Firefox、Safari、Chrome和Opera中运行，在IE中则会报错。IE将`<style>`视为一个特殊的、与`<style>`类似的节点，不允许访问其子节点。事实上，IE此时抛出的错误与向`<script>`元素添加子节点时抛出的错误相同。解决IE中这个问题的办法，就是访问元素的`styleSheet`属性，该属性又有一个`cssText`属性，可以接受CSS代码，如下面的例子所示。

```javascript
var style = document.createElement("style");
style.type = "text/css";
try{
    style.appendChild(document.createTextNode("body{background-color:red}"));
} catch (ex){
    style.styleSheet.cssText = "body{background-color:red}";
}
var head = document.getElementsByTagName("head")[0];
head.appendChild(style);
```

与动态添加嵌入式脚本类似，重写后的代码使用了`try-catch`语句来捕获IE抛出的错误，然后再使用针对IE的特殊方式来设置样式。因此，通用的解决方案如下。

```javascript
function loadStyleString(css) {
    var style = document.createElement("style");
    style.type = "text/css";
    try {
        style.appendChild(document.createTextNode(css));
    } catch (ex) {
        style.styleSheet.cssText = css;
    }
    var head = document.getElementsByTagName("head")[0];
    head.appendChild(style);
}
```

调用这个函数的示例如下：

```javascript
loadStyleString("body{background-color:red}");
```

这种方式会实时地向页面中添加样式，因此能够马上看到变化。

## 操作表格

创建表格会涉及非常多的标签，使用DOM方法创建和修改就避免不了大量代码的编写。假设要使用DOM来创建下面的HTML表格。

```html
<table border="1" width="100%">
    <tbody>
        <tr>
            <td>Cell 1,1</td>
            <td>Cell 2,1</td>
        </tr>
        <tr>
            <td>Cell 1,2</td>
            <td>Cell 2,2</td>
        </tr>
    </tbody>
</table>
```

要使用DOM编写这些元素，得需要像下面这么多的代码：

```javascript
// 创建table
var table = document.createElement("table");
table.border = 1;
table.width = "100%";
// 创建tbody
var tbody = document.createElement("tbody");
table.appendChild(tbody);
// 创建第一行
var row1 = document.createElement("tr");
tbody.appendChild(row1);
var cell1_1 = document.createElement("td");
cell1_1.appendChild(document.createTextNode("Cell 1,1"));
row1.appendChild(cell1_1);
var cell2_1 = document.createElement("td");
cell2_1.appendChild(document.createTextNode("Cell 2,1"));
row1.appendChild(cell2_1);
//创建第二行
var row2 = document.createElement("tr");
tbody.appendChild(row2);
var cell1_2 = document.createElement("td");
cell1_2.appendChild(document.createTextNode("Cell 1,2"));
row2.appendChild(cell1_2);
var cell2_2= document.createElement("td");
cell2_2.appendChild(document.createTextNode("Cell 2,2"));
row2.appendChild(cell2_2);
//将表格添加到文档主体中
document.body.appendChild(table);
```

显然，DOM代码很长，还有点不太好懂。为了方便构建表格，HTML DOM还为`<table>`、`<tbody>`和`<tr>`元素添加了一些属性和方法。

为`<table>`元素添加的属性和方法如下。

- caption：保存着对`<caption>`元素（如果有）的指针。
- tBodies：是一个`<tbody>`元素的HTMLCollection。
- tFoot：保存着对`<tfoot>`元素（如果有）的指针。
- tHead：保存着对`<thead>`元素（如果有）的指针。
- rows：是一个表格中所有行的HTMLCollection。
- createTHead()：创建`<thead>`元素，将其放到表格中，返回引用。
- createTFoot()：创建`<tfoot>`元素，将其放到表格中，返回引用。
- createCaption()：创建`<caption>`元素，将其放到表格中，返回引用。
- deleteTHead()：删除`<thead>`元素。
- deleteTFoot()：删除`<tfoot>`元素。
- deleteCaption()：删除`<caption>`元素。
- deleteRow(pos)：删除指定位置的行。
- insertRow(pos)：向rows 集合中的指定位置插入一行。
为`<tbody>`元素添加的属性和方法如下。
- rows：保存着`<tbody>`元素中行的HTMLCollection。
- deleteRow(pos)：删除指定位置的行。
- insertRow(pos)：向rows 集合中的指定位置插入一行，返回对新插入行的引用。

为`<tr>`元素添加的属性和方法如下。

- cells：保存着`<tr>`元素中单元格的HTMLCollection。
- deleteCell(pos)：删除指定位置的单元格。
- insertCell(pos)：向cells 集合中的指定位置插入一个单元格，返回对新插入单元格的引用。

使用这些属性和方法，可以极大地减少创建表格所需的代码数量。例如，使用这些属性和方法可以将前面的代码重写如下。

```javascript
//创建table
var table = document.createElement("table");
table.border = 1;
table.width = "100%";
//创建tbody
var tbody = document.createElement("tbody");
table.appendChild(tbody);
//创建第一行
tbody.insertRow(0);
tbody.rows[0].insertCell(0);
tbody.rows[0].cells[0].appendChild(document.createTextNode("Cell 1,1"));
tbody.rows[0].insertCell(1);
tbody.rows[0].cells[1].appendChild(document.createTextNode("Cell 2,1"));
//创建第二行
tbody.insertRow(1);
tbody.rows[1].insertCell(0);
tbody.rows[1].cells[0].appendChild(document.createTextNode("Cell 1,2"));
tbody.rows[1].insertCell(1);
tbody.rows[1].cells[1].appendChild(document.createTextNode("Cell 2,2"));
//将表格添加到文档主体中
document.body.appendChild(table);
```

在这次的代码中，创建`<table>`和`<tbody>`的代码没有变化。不同的是创建两行的部分，其中使用了HTML DOM 定义的表格属性和方法。在创建第一行时，通过`<tbody>`元素调用了insertRow()方法，传入了参数0——表示应该将插入的行放在什么位置上。执行这一行代码后，就会自动创建一行并将其插入到`<tbody>`元素的位置0 上，因此就可以马上通过tbody.rows[0]来引用新插入的行。

创建单元格的方式也十分相似，即通过`<tr>`元素调用insertCell()方法并传入放置单元格的位置。然后，就可以通过tbody.rows[0].cells[0]来引用新插入的单元格，因为新创建的单元格被插入到了这一行的位置0 上。

总之，使用这些属性和方法创建表格的逻辑性更强，也更容易看懂，尽管技术上这两套代码都是正确的。

## 使用NodeList

理解NodeList 及其“近亲”NamedNodeMap 和HTMLCollection，是从整体上透彻理解DOM的关键所在。这三个集合都是“动态的”；换句话说，每当文档结构发生变化时，它们都会得到更新。因此，它们始终都会保存着最新、最准确的信息。从本质上说，所有NodeList 对象都是在访问DOM文档时实时运行的查询。例如，下列代码会导致无限循环：

```javascript
var divs = document.getElementsByTagName("div"),
    i, div;
for (i = 0; i < divs.length; i++) {
    div = document.createElement("div");
    document.body.appendChild(div);
}
```

第一行代码会取得文档中所有`<div>`元素的HTMLCollection。由于这个集合是"动态的"，因此只要有新`<div>`元素被添加到页面中，这个元素也会被添加到该集合中。浏览器不会将创建的所有集合都保存在一个列表中，而是在下一次访问集合时再更新集合。结果，在遇到上例中所示的循环代码时，就会导致一个有趣的问题。每次循环都要对条件i < divs.length 求值，意味着会运行取得所有`<div>`元素的查询。考虑到循环体每次都会创建一个新`<div>`元素并将其添加到文档中，因此divs.length 的值在每次循环后都会递增。既然i 和divs.length 每次都会同时递增，结果它们的值永远也不会相等。

如果想要迭代一个NodeList，最好是使用length属性初始化第二个变量，然后将迭代器与该变量进行比较，如下面的例子所示：

```javascript
var divs = document.getElementsByTagName("div"),
    i,len,div;
for (i=0, len=divs.length; i < len; i++){
    div = document.createElement("div");
    document.body.appendChild(div);
}
```

这个例子中初始化了第二个变量`len`。由于len中保存着对divs.length在循环开始时的一个快照，因此就会避免上一个例子中出现的无限循环问题。在本章演示迭代NodeList对象的例子中，使用的都是这种更为保险的方式。

一般来说，应该尽量减少访问NodeList的次数。因为每次访问NodeList，都会运行一次基于文档的查询。所以，可以考虑将从NodeList中取得的缓存起来。

# 上述小结

DOM是语言中立的API，用于访问和操作HTML和XML文档。DOM1级将HTML和XML文档形象地看作一个层次化的节点树。可以使用JavaScript来操作这个节点树，进而改变底层文档的外观和结构。

DOM由各种节点构成，简要总结如下。

- 最基本的节点类型是Node，用于抽象地表示文档中一个独立的部分；所有其它类型都继承自Node。
- Document 类型表示整个文档，是一组分层节点的根节点。在JavaScript 中，document 对象是Document 的一个实例。使用document 对象，有很多种方式可以查询和取得节点。
- Element 节点表示文档中的所有HTML 或XML 元素，可以用来操作这些元素的内容和特性。
- 另外还有一些节点类型，分别表示文本内容、注释、文档类型、CDATA 区域和文档片段。

访问DOM 的操作在多数情况下都很直观，不过在处理`<script>`和`<style>`元素时还是存在一些复杂性。由于这两个元素分别包含脚本和样式信息，因此浏览器通常会将它们与其他元素区别对待。这些区别导致了在针对这些元素使用innerHTML 时，以及在创建新元素时的一些问题。

理解DOM的关键，就是理解DOM 对性能的影响。DOM操作往往是JavaScript 程序中开销最大的部分，而因访问NodeList 导致的问题为最多。NodeList 对象都是“动态的”，这就意味着每次访问NodeList 对象，都会运行一次查询。有鉴于此，最好的办法就是尽量减少DOM操作。

# Selectors API

Selectors API是由W3C发起制定的一个标准，致力于让浏览器原生支持CSS查询。所有实现这一功能的JavaScript库都会写一个基础的CSS解析器，然后再使用已有的DOM方法查询文档并找到匹配的节点。尽管库开发人员在不知疲倦地改进这一过程的性能，但到头来
都只能通过运行JavaScript 代码来完成查询操作。而把这个功能变成原生API 之后，解析和树查询操作可以在浏览器内部通过编译后的代码来完成，极大地改善了性能。

Selectors API Level 1的核心是两个方法：querySelector()和querySelectorAll()。在兼容的浏览器中，可以通过Document及Element类型的实例调用它们。目前已完全支持Selectors API Level 1的浏览器有IE 8+，Firefox3.5+、Safari 3.1+、Chrome和Opera 10+。

## querySelector()方法

querySelector()方法接收一个CSS选择符，返回与该匹配模式的第一个元素，如果没有找到匹配的元素，返回null。

```javascript
//取得body 元素
var body = document.querySelector("body");
//取得ID 为"myDiv"的元素
var myDiv = document.querySelector("#myDiv");
//取得类为"selected"的第一个元素
var selected = document.querySelector(".selected");
//取得类为"button"的第一个图像元素
var img = document.body.querySelector("img.button");
```

通过Document类型调用querySelector()方法时，会在文档元素的范围内查找匹配的元素。而通过Element类型调用querySelector()方法时，只会在该元素后代元素的范围内查找匹配的元素。

CSS选择符可以简单也可以复杂，视情况而定。如果传入了不被支持的选择符，querySelector()会抛出错误。

## querySelectorAll()方法

querySelectorAll()方法接收的参数与querySelector()方法一样，都是一个CSS选择符，但返回的是所有匹配的元素二不仅仅是一个元素。这个方法返回的是一个NodeList的实例。

具体来说，返回的值实际上是带有所有属性和方法的NodeList，而其底层实现则类似于一组元素的快照，而非不断对文档进行搜索的动态查询。这样实现可以避免使用NodeList，对象通常会引起的大多数性能问题。

只要传给querySelectorAll()方法的CSS选择符有效，该方法都会返回一个NodeList对象，而不管找到多少匹配的元素。如果没有找到匹配的元素，NodeList都是空的。

只要传给querySelectorAll()方法的CSS 选择符有效，该方法都会返回一个NodeList 对象，而不管找到多少匹配的元素。如果没有找到匹配的元素，NodeList 就是空的。

与querySelector()类似，能够调用querySelectorAll()方法的类型包括Document、DocumentFragment 和Element。下面是几个例子。

```javascript
//取得某<div>中的所有<em>元素（类似于getElementsByTagName("em")）
var ems = document.getElementById("myDiv").querySelectorAll("em");
//取得类为"selected"的所有元素
var selecteds = document.querySelectorAll(".selected");
//取得所有<p>元素中的所有<strong>元素
var strongs = document.querySelectorAll("p strong");
```

要取得返回的NodeList中的每一个元素，可以使用item()方法，也可以使用方括号语法，比如：

```javascript
var i, len, strong;
for (i=0, len=strongs.length; i < len; i++){
    strong = strongs[i]; //或者strongs.item(i)
    strong.className = "important";
}
```

同样与querySelector()类似，如果传入了浏览器不支持选择符或者选择符中有语法错误，querySelectorAll()会抛出错误。

## matchesSelector()方法

Selectors API Level 2 规范为Element 类型新增了一个方法matchesSelector()。这个方法接收一个参数，即CSS 选择符，如果调用元素与该选择符匹配，返回true；否则，返回false。看例子。

```javascript
if (document.body.matchesSelector("body.page1")) {
    // true
}
```

在取得某个元素引用的情况下，使用这个方法能够方便地检测它是否会被querySelector()或querySelectorAll()方法返回。

截至2011 年年中，还没有浏览器支持matchesSelector()方法；不过，也有一些实验性的实现。IE 9+通过msMatchesSelector()支持该方法，Firefox 3.6+通过mozMatchesSelector()支持该方法，Safari 5+和Chrome 通过webkitMatchesSelector()支持该方法。因此，如果你想使用这个方法，最好是编写一个包装函数。

```javascript
function matchesSelector(element, selector){
    if (element.matchesSelector){
        return element.matchesSelector(selector);
    } else if (element.msMatchesSelector){
        return element.msMatchesSelector(selector);
    } else if (element.mozMatchesSelector){
        return element.mozMatchesSelector(selector);
    } else if (element.webkitMatchesSelector){
        return element.webkitMatchesSelector(selector);
    } else {
        throw new Error("Not supported.");
    }
}
if (matchesSelector(document.body, "body.page1")){
    //执行操作
}
```

# 元素遍历

对于元素间的空格，IE9 及之前版本不会返回文本节点，而其他所有浏览器都会返回文本节点。这样，就导致了在使用childNodes 和firstChild 等属性时的行为不一致。为了弥补这一差异，而同时又保持DOM规范不变，Element Traversal 规范（www.w3.org/TR/ElementTraversal/）新定义了一组属性。

Element Traversal API为DOM元素添加了以下5个属性。

- childElementCount：返回子元素（不包括文本节点和注释）的个数。
- firstElementChild：指向第一个子元素；firstChild的元素板。
- lastElementChild：指向最后一个子元素；lastChild的元素板。
- previousElementSibling：指向前一个同辈元素；previousSibling的元素板。
- nextElementSibling：指向后一个同辈元素；nextSibling的元素板。

支持的浏览器为DOM元素添加了这些属性，利用这些元素不必担心空白文本节点，从而可以更方便地查找DOM元素了。

下面来看一个例子。过去，要跨浏览器遍历某元素的所有子元素，需要像下面这样写代码。

```javascript
var i,
    len,
    child = element.firstChild;
while(child != element.lastChild){
    if (child.nodeType == 1){ //检查是不是元素
        processChild(child);
    }
    child = child.nextSibling;
}
```

而使用Element Traversal新增的元素，代码会更简洁。

```javascript
var i,
    len,
    child = element.firstElementChild;
while(child != element.lastElementChild){
    processChild(child); //已知其是元素
    child = child.nextElementSibling;
}
```

支持Element Traversal规范的浏览器有IE 9+、Firefox 3.5+、Safari 4+、Chrome和Opera 10+。

# HTML5

对于传统HTML而言，HTML5是一个叛逆。所有值钱的版本对JavaScript接口的描述斗不过三言两语，主要篇幅都用于定义标记，与JavaScript相关的内容一概交由DOM规范去定义。

而HTML5规范则围绕如何使用新增标记定义了大量JavaScript API。其中一些API与DOM重叠，定义了浏览器应该支持的DOM扩展。

## 与类相关的扩充

HTML4 在Web 开发领域得到广泛采用后导致了一个很大的变化，即class 属性用得越来越多，一方面可以通过它为元素添加样式，另一方面还可以用它表示元素的语义。于是，自然就有很多JavaScript代码会来操作CSS 类，比如动态修改类或者搜索文档中具有给定类或给定的一组类的元素，等等。为了让开发人员适应并增加对class 属性的新认识，HTML5 新增了很多API，致力于简化CSS 类的用法。

### getElementsByClassName()方法

HTML5添加的getElementsByClassName()方法是最受人欢迎的一个方法，可以通过document对象及所有HTML元素调用该方法。这个方法最早出现在JavaScript库中，是通过既有DOM功能实现的，而原生的实现具有极大的性能优势。

getElementsByClassName()方法接收一个参数，即一个包含一或多个类名的字符串，返回带有指定类的所有元素的NodeList。传入多个类名时，类名的先后顺序不重要。来看下面的例子。

```javascript
//取得所有类中包含"username"和"current"的元素，类名的先后顺序无所谓
var allCurrentUsernames = document.getElementsByClassName("username current");
//取得ID 为"myDiv"的元素中带有类名"selected"的所有元素
var selected = document.getElementById("myDiv").getElementsByClassName("selected");
```

调用这个方法时，只有位于调用元素子树中的元素才会返回。在document对象上调用getElementsByClassName()始终会返回与类名匹配的所有元素，在元素上调用该方法就只会返回后代元素中匹配的元素。

使用这个方法可以更方便地为某些类的元素添加事件处理程序，从而不必再局限于使用ID或标签名。不过别忘了，因为返回的对象是NodeList，所以使用这个方法与使用getElementsBYTagName()以及其它返回NodeList的DOM方法都具有同样的性能问题。

### classList属性

在操作类名时，需要通过className属性添加、删除和替换类名。因为className中是一个字符串，所以即使只修改字符串一部分，也必须每次都设置整个字符串的值。比如，以下面的HTML代码为例。

```html
<div class="bd user disabled">...</div>
```

这个`<div>`元素一共有三个类名。要从中删除一个类名，需要把这三个类名拆开，删除不想要的那个，然后再把其他类名拼成一个新字符串。请看下面的例子。

```javascript
//删除"user"类
//首先，取得类名字符串并拆分成数组
var classNames = div.className.split(/\s+/);
//找到要删的类名
var pos = -1,
    i,
    len;
for (i=0, len=classNames.length; i < len; i++){
    if (classNames[i] == "user"){
        pos = i;
        break;
    }
}
//删除类名
classNames.splice(i,1);
//把剩下的类名拼成字符串并重新设置
div.className = classNames.join(" ");
```

为了从`<div>`元素的class属性中删除"user"，以上这些代码都是必需的。必须是通过类似的算法替换类名并确认元素中是否包含该类名。添加类名可以通过拼接字符串完成，但必须是通过检测确定不会多次添加相同的类名。很多JavaScript库都实现了这个方法，以简化这些操作。

HTML5新增了一种操作类名的方式，可以让操作更简单也更安全，那就是为所有元素添加classList属性。这个classList属性是新集合类型DOMTokenList的实例。与其它DOM集合类似，DOMTokenList有一个表示自己包含多少元素的length属性，而要取得每个元素可以使用item()方法，也可以使用方括号语法。此外，这个新类型还定义如下方法。

- add(value)：将给定的字符串值添加到列表中。如果值已经存在， 就不添加了。
- contains(value)：表示列表中是否存在给定的值，如果存在则返回true，否则返回false。
- remove(value)：从列表中删除给定的字符串。
- toggle(value)：如果列表中已经存在给定的值，删除它；如果列表中没有给定的值，添加它。

这样，前面那么多行代码用下面这一行代码就可以代替了。

```javascript
div.classList.remove("user");
```

以上代码能够确保其它类名不受此次修改的影响。其它方法也能极大地减少类似基本操作的复杂性，如下面的例子所示。

```javascript
//删除"disabled"类
div.classList.remove("disabled");
//添加"current"类
div.classList.add("current");
//切换"user"类
div.classList.toggle("user");
//确定元素中是否包含既定的类名
if (div.classList.contains("bd") && !div.classList.contains("disabled")){
    //执行操作
)
//迭代类名
for (var i=0, len=div.classList.length; i < len; i++){
    doSomething(div.classList[i]);
}
```

有了classList属性，除非你需要全部删除所有类名，或者完全重写元素的class属性，否则就用不到className属性了。

支持classList属性的浏览器有Firefox 3.6+和Chrome。

## 焦点管理

HTML5也添加了辅助管理DOM焦点的功能。首先就是document.activeElement属性，这个属性始终会引用DOM中当前获得了焦点的元素。元素获得焦点的方式有页面加载、用户输入（通常是通过按Tab键）和在代码中调用focus()方法。来看几个例子。

```javascript
var button = document.getElementById("myButton");
button.focus();
console.log(document.activeElement === button);    // true
```

默认情况下，文档刚刚加载完成时，document.activeElement 中保存的是document.body元素的引用。文档加载期间，document.activeElement的值为null。

另外就是新增了document.hasFocus()方法，这个方法用于确定文档是否获得了焦点。

```javascript
var button = document.getElementById("myButton");
button.focus();
alert(document.hasFocus()); //true
```

通过检测文档是否获得了焦点，可以知道用户是不是正在与页面交互。

查询文档获知哪个元素获得了焦点，以及确定文档是否获得了焦点，这两个功能最重要的用途是提高Web应用的无障碍性。无障碍Web应用的一个主要标志就是恰当的焦点管理，而确认知道哪个元素获得了焦点是一个极大的进步，至少我们不再像过去那样靠猜测了。

实现了这两个属性的浏览器的包括IE4+、Firefox 3+、Safari 4+、Chrome和Opera 8+。

## HTMLDocument的变化

HTML5 扩展了HTMLDocument，增加了新的功能。与HTML5 中新增的其他DOM 扩展类似，这些变化同样基于那些已经得到很多浏览器完美支持的专有扩展。所以，尽管这些扩展被写入标准的时间相对不长，但很多浏览器很早就已经支持这些功能了。

### readyState属性

IE4 最早为document 对象引入了readyState 属性。然后，其他浏览器也都陆续添加这个属性，
最终HTML5 把这个属性纳入了标准当中。Document 的readyState 属性有两个可能的值：

- loading，正在加载文档；
- complete，已经加载完文档。

使用document.readyState 的最恰当方式，就是通过它来实现一个指示文档已经加载完成的指示器。在这个属性得到广泛支持之前，要实现这样一个指示器，必须借助onload 事件处理程序设置一个标签，表明文档已经加载完毕。document.readyState 属性的基本用法如下。

```javascript
if (document.readyState == "complete") {
    // 执行操作
}
```

支持readyState属性的浏览器有IE4+、Firefox 3.6+、Safari、Chrome和Opera 9+。

### 兼容模式

自从IE6 开始区分渲染页面的模式是标准的还是混杂的，检测页面的兼容模式就成为浏览器的必要功能。IE 为此给document 添加了一个名为compatMode 的属性，这个属性就是为了告诉开发人员浏览器采用了哪种渲染模式。就像下面例子中所展示的那样，在标准模式下，document.compatMode 的值等于"CSS1Compat"，而在混杂模式下，document.compatMode 的值等于"BackCompat"。

```javascript
if (document.compatMode == "CSS1Compat") {
    alert("Standards mode");
} else {
    alert("Quicks mode");
}
```

后来，陆续实现这个属性的浏览器有Firefox、Safari 3.1+、Opera 和Chrome。最终，HTML5 也把这个属性纳入标准，对其实现做出了明确规定。

### head属性

作为对document.body 引用文档的`<body>`元素的补充，HTML5 新增了document.head 属性，
引用文档的`<head>`元素。要引用文档的`<head>`元素，可以结合使用这个属性和另一种后备方法。

```javascript
var head = document.head || document.getElementsByTagName("head")[0];
```

如果可用，就使用document.head，否则仍然使用getElementsByTagName()方法。

实现document.head 属性的浏览器包括Chrome 和Safari 5。

## 字符集属性

HTML5新增了几个与文档字符集有关的属性。其中，charset属性表示文档中实际使用的字符集，也可以用来指定新字符集。默认情况下，这个属性的值为"UTF-16"，但可以通过`<meta>`元素、响应头部或直接设置charset属性修改这个值。来看一个例子。

```javascript
alert(document.charset);    // UTF-16
document.charset = "UTF-8";
```

另一个属性是defaultCharset，表示根据默认浏览器及操作系统的设置，当前文档的字符集应该是什么。如果文档没有使用默认的字符集，那charset和defaultCharset属性的值可能会不一样，例如：

```javascript
if (document.charset != document.defaultCharset) {
    alert("Custom character set being used.");
}
```

通过这两个属性可以得到文档使用的字符编码的具体信息，也能对字符编码进行准确地控制。运行适当的情况下，可以保证用户正常查看页面或使用应用。

支持document.charset 属性的浏览器有IE 、Firefox 、Safari 、Opera 和Chrome 。支持document.defaultCharset 属性的浏览器有IE、Safari 和Chrome。

## 自定义数据属性

HTML5 规定可以为元素添加非标准的属性，但要添加前缀data-，目的是为元素提供与渲染无关的信息，或者提供语义信息。这些属性可以任意添加、随便命名，只要以data-开头即可。来看一个例子。

```html
<div id="myDiv" data-appId="12345" data-myname="Nicholas"></div>
```

添加了自定义属性之后，可以通过元素的dataset属性来访问自定义属性的值。dataset属性的值是DOMStringMap的一个实例，也就是一个名值对儿的映射。在这个映射中，每个data-name形式的属性都会有一个对应的属性，只不过属性名没有data-前缀（比如，自定义属性是data-myname，那映射中对应的属性就是myname）。还是看一个例子吧。

```javascript
//本例中使用的方法仅用于演示
var div = document.getElementById("myDiv");
//取得自定义属性的值
var appId = div.dataset.appId;
var myName = div.dataset.myname;
//设置值
div.dataset.appId = 23456;
div.dataset.myname = "Michael";
//有没有"myname"值呢？
if (div.dataset.myname){
    alert("Hello, " + div.dataset.myname);
}
```

如果需要给元素添加一些不可见的数据以便进行其它处理，那就要用到自定义数据属性。在跟踪链接或混搭应用中，通过自定义数据属性能方便地知道点击来自页面中的哪个部分。

在编写本书时，支持自定义数据属性的浏览器有Firefox 6+和Chrome。

## 插入标记

虽然DOM为操作节点提供了细致入微的控制手段，但在需要给文档插入大量新HTML标记的情况下，通过DOM操作仍然非常麻烦，因为不仅要创建一系列DOM节点，而且还要小心地按照正确的顺序把它们连接起来。相对而言，使用插入标记的技术，直接插入HTML字符串不仅更简单，速度也更快。以下与插入标记相关的DOM扩展已经纳入了HTML5规范。

### innerHTML属性

在读模式下，innerHTML属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应的HTML标记。在写模式下，innerHTML会根据指定的值创建新的DOM树，然后用这个DOM树完全替换调用元素原先的所有子节点。下面是一个例子。

```html
<div id="content">
    <p>This is a <strong>paragraph</strong> with a list following it.</p>
    <ul>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
    </ul>
</div>
```

对于上面的`<div>`元素来说，它的innerHTML属性会返回如下字符串。

```html
<p>This is a <strong>paragraph</strong> with a list following it.</p>
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
```

但是，不同浏览器返回的文本格式会有所不同。IE和Opera会将所有标签转换为大写形式，而Safari、Chrome和Firefox则会原原本本地按照原先文档中（或指定这些标签时）的格式返回HTML，包括空格和缩进。

在写模式下，innerHTML 的值会被解析为DOM 子树，替换调用元素原来的所有子节点。因为它的值被认为是HTML，所以其中的所有标签都会按照浏览器处理HTML 的标准方式转换为元素（同样，这里的转换结果也因浏览器而异）。如果设置的值仅是文本而没有HTML 标签，那么结果就是设置纯文本，如下所示。

```javascript
div.innerHTML = "Hello world!";
```

为innerHTML设置的包含HTML的字符串值与解析后innerHTML的值大不相同。来看下面的例子。

```javascript
div.innerHTML = "Hello & welcome, <b>\"reader\"!</b>";
```

以上操作得到的结果如下：

```html
<div id="content">Hello &amp; welcome, <b>&quot;reader&quot;!</b></div>
```

设置了innerHTML之后，可以像访问文档中的其它节点一样访问新创建的节点。

使用innerHTML属性也有一些限制。比如，在大多数浏览器中，通过innerHTML 插入`<script>`元素并不会执行其中的脚本。IE8 及更早版本是唯一能在这种情况下执行脚本的浏览器，但必须满足一些条件。一是必须为`<script>`元素指定defer 属性，二是`<script>`元素必须位于（微软所谓的）“有作用域的元素”（scoped element）之后。`<script>`元素被认为是“无作用域的元素”（NoScope element），也就是在页面中看不到的元素，与`<style>`元素或注释类似。如果通过innerHTML 插入的字符串开头就是一个“无作用域的元素”，那么IE 会在解析这个字符串前先删除该元素。换句话说，以下代码达不到目的：

```javascript
div.innerHTML = "<script defer>alert('hi');<\/script>"; //无效
```

此时，innerHTML字符串一开始（而且整个）就是一个"无作用域的元素"，所以这个字符串会变成空字符串。如果想插入这段脚本，必须在前面添加一个"有作用于的元素"，可以是一个文本节点，也可以是一个没有结束标签的元素如`<input>`。例如，下面这几行代码都可以正常执行：

```javascript
div.innerHTML = "_<script defer>alert('hi');<\/script>";
div.innerHTML = "<div>&nbsp;</div><script defer>alert('hi');<\/script>";
div.innerHTML = "<input type=\"hidden\"><script defer>alert('hi');<\/script>";
```

第一行代码会在`<script>`元素前插入一个文本节点。事后，为了不影响页面显示，你可能需要移除这个文本节点。第二行代码采用的方法类似，只不过使用的是一个包含非换行空格的`<div>`元素。如果仅仅插入一个空的`<div>`元素，还是不行；必须要包含一点儿内容，浏览器才会创建文本节点。同样，为了不影响页面布局，恐怕还得移除这个节点。第三行代码使用使用的是一个隐藏的`<input>`域，也能达到相同的效果。不过，由于隐藏的`<input>`域不影响页面布局，因此这种方式在大多数情况下都是首选。

大多数浏览器都支持以直观的方式通过innerHTML插入`<style>`元素，例如：

```javascript
div.innerHTML = "<style type=\"text/css\">body {background-color: red; }</style>";
```

但在IE8及更早版本中，`<style>`也是一个"没有作用域的元素"，因此必须像下面这样给它前置一个"有作用域的元素"：

```javascript
div.innerHTML = "_<style type=\"text/css\">body {background-color: red; }</style>";
div.removeChild(div.firstChild);
```

并不是所有元素都支持innerHTML属性。不支持innerHTML的元素有：：`<col>`、`<colgroup>`、`<frameset>`、`<head>`、`<html>`、`<style>`、`<table>`、`<tbody>`、`<thead>`、`<tfoot>`和`<tr>`。此外，在IE8 及更早版本中，`<title>`元素也没有innerHTML 属性。

无论什么时候，只要使用innerHTML从外部插入HTML，都应该首先以可靠的方式处理HTML。IE8为此提供了window.toStaticHTML()方法，这个方法接收一个参数，即一个HTML字符串；返回一个经过无害处理后的版本——从源HTML中删除所有脚本节点和事件处理程序。下面就是一个例子：

```javascript
var text = "<a href=\"#\" onclick=\"alert('hi')\">Click Me</a>";
var sanitized = window.toStaticHTML(text); //Internet Explorer 8 only
alert(sanitized); //"<a href=\"#\">Click Me</a>"
```

这个例子将一个HTML链接字符串传给了toStaticHTML()方法，得到的无害版本中去掉了onclick属性。虽然目前只有IE8原生支持这个方法，但我们还是建议读者在通过innerHTML插入代码之前，尽可能先手工检查一下其中的文本内容。

### outerHTML属性

在读模式下，outerHTML 返回调用它的元素及所有子节点的HTML 标签。在写模式下，outerHTML会根据指定的HTML 字符串创建新的DOM 子树，然后用这个DOM子树完全替换调用元素。下面是一
个例子。

```html
<div id="content">
    <p>This is a <strong>paragraph</strong> with a list following it.</p>
    <ul>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
    </ul>
</div>
```

如果在`<div>`元素上调用outerHTML，会返回与上面相同的代码，包括`<div>`本身。不过，由于浏览器解析和解释HTML标记的不同，结果也可能会有所不同。（这里的不同与使用innerHTML属性时存在的差异性质是一样的）。

使用outerHTML属性以下面这种方式设置值：

```javascript
div.outerHTML = "<p>This is a paragraph.</p>";
```

这行代码完成的操作与下面这些DOM脚本代码一样：

```javascript
var p = document.createElement("p");
p.appendChild(document.createTextNode("This is a paragraph."));
div.parentNode.replaceChild(p, div);
```

结果，就是新创建的`<p>`元素会取代DOM树中的`<div>`元素。

支持outerHTML属性的浏览器有IE 4+、Safari 4+、Chrome 和Opera 8+。Firefox 7 及之前版本都不支持outerHTML 属性。

### insertAdjacentHTML()方法

插入标记的最后一个新增方式是insertAdjacentHTML()方法。这个方法最早也是在IE 中出现的，它接收两个参数：插入位置和要插入的HTML 文本。第一个参数必须是下列值之一：

- "beforebegin"，在当前元素之前插入一个紧邻的同辈元素；
- "afterbegin"，在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素；
- "beforeend"，在当前元素之下插入一个新的子元素或在最后一个子元素之后再插入新的子元素；
- "afterend"，在当前元素之后插入一个紧邻的同辈元素。

注意，这些值都必须是小写形式。第二个参数是一个HTML 字符串（与innerHTML 和outerHTML的值相同），如果浏览器无法解析该字符串，就会抛出错误。以下是这个方法的基本用法示例。

```javascript
//作为前一个同辈元素插入
element.insertAdjacentHTML("beforebegin", "<p>Hello world!</p>");
//作为第一个子元素插入
element.insertAdjacentHTML("afterbegin", "<p>Hello world!</p>");
//作为最后一个子元素插入
element.insertAdjacentHTML("beforeend", "<p>Hello world!</p>");
//作为后一个同辈元素插入
element.insertAdjacentHTML("afterend", "<p>Hello world!</p>");
```

支持insertAdjacentHTML()方法的浏览器有IE、Firefox 8—+、Safari、Opera和Chrome。

### 内存与性能问题

使用本节介绍的方法替换子节点可能会导致浏览器的内存占用问题，尤其是在IE 中，问题更加明显。在删除带有事件处理程序或引用了其他JavaScript 对象子树时，就有可能导致内存占用问题。假设某个元素有一个事件处理程序（或者引用了一个JavaScript 对象作为属性），在使用前述某个属性将该元素从文档树中删除后，元素与事件处理程序（或JavaScript 对象）之间的绑定关系在内存中并没有一并
删除。如果这种情况频繁出现，页面占用的内存数量就会明显增加。因此，在使用innerHTML、outerHTML 属性和insertAdjacentHTML()方法时，最好先手工删除要被替换的元素的所有事件处理程序和JavaScript 对象属性。

不过，使用这几个属性——特别是使用innerHTML，仍然还是可以为我们提供很多便利的。一般来说，在插入大量新HTML 标记时，使用innerHTML 属性与通过多次DOM 操作先创建节点再指定它们之间的关系相比，效率要高得多。这是因为在设置innerHTML 或outerHTML 时，就会创建一个HTML解析器。这个解析器是在浏览器级别的代码（通常是C++编写的）基础上运行的，因此比执行JavaScript快得多。不可避免地，创建和销毁HTML 解析器也会带来性能损失，所以最好能够将设置innerHTML或outerHTML 的次数控制在合理的范围内。例如，下列代码使用innerHTML 创建了很多列表项：

```javascript
for (var i=0, len=values.length; i < len; i++){
    ul.innerHTML += "<li>" + values[i] + "</li>"; //要避免这种频繁操作！！
}
```

这种每次循环都设置一次innerHTML的做法效率很低。而且，每次循环还要从innerHTML中读取一次信息，就意味着每次循环要访问两次innerHTML。做好的做法是单独构建字符串，然后再一次性地将结果字符串赋给innerHTML，像下面这样：

```javascript
var itemsHtml = "";
for (var i=0, len=values.length; i < len; i++){
    itemsHtml += "<li>" + values[i] + "</li>";
}
ul.innerHTML = itemsHtml;
```

这个例子的效率要高得多，因为它只对innerHTML执行了一次赋值操作。

## scrollIntoView()方法

如何滚动页面也是DOM规范没有解决的一个问题。为了解决这个问题，浏览器实现了一些方法，以方便开发人员更好地控制页面滚动。在各种专有方法中，HTML5 最终选择了scrollIntoView()作为标准方法。

scrollIntoView()可以在所有HTML 元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。如果给这个方法传入true 作为参数，或者不传入任何参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传入false 作为参数，调用元素会尽可能全部出现在视口中，（可能的话，调用元素的底部会与视口顶部平齐。）不过顶部不一定平齐，例如：

```javascript
// 让元素可见
document.forms[0].scrollIntoView();
```

当页面发生变化时，一般会用这个方法来吸引用户的注意力。实际上，为某个元素设置焦点也会导致浏览器滚动并显示出获得焦点的元素。

支持scrollIntoView()方法的浏览器有IE、Firefox、Safari和Opera。

# 专有扩展

标准非常重要，但专项功能缺失时，开发商会向DOM中添加专有扩展，弥补功能上的不足。表面看起来不太好，但实际上专有扩展为Web开发提供很多重要功能，最终都在HTML5规范中得到了标准化。

## 文档模式

IE8引入了一个新的概念叫"文档模式"（document model）。页面的文档模式决定了可以使用什么功能。换句话说，文档模式决定了你可以使用哪个级别的CSS，可以在JavaScript中使用哪些API，以及如何对待文档类型（doctype）。到了IE9，总共有以下4中文档模式。

- IE5：以混杂模式渲染页面（IE5的默认模式就是混杂模式）。IE8及更高版本中的新功能都无法使用。
- IE7：以IE7标准模式渲染页面。IE8及更高版本中的新功能都无法使用。
- IE8：以IE8标准模式渲染页面。IE8中的新功能都可以使用，因此可以使用Selectors API，更多CSS2级选择符和某些CSS3功能，还有一些HTML5的功能。不过IE9中的新功能无法使用。
- IE9：以IE9标准模式渲染页面。IE9中的新功能都可以使用，比如ECMAScript5、完整的CSS3以及更多HTML5功能。这个文档模式是最高级的模式。

要理解IE8及更高版本的工作原理，必须理解文档模式。

要强制浏览器以某种模式渲染页面，可以使用HTTP头部信息X-UA-Compatible，或通过等价的`<meta>`标签来设置：

```html
<meta http-equiv="X-UA-Compatible" content="IE-IEVersion">
```

注意，这里IE的版本（IEVersion）有以下一些不同的值，而且这些值并不一定与上述4种文档模式对应。

- Edge：始终以最新的文档模式来渲染页面。忽略文档类型声明。对于IE8，始终保持以IE8标准模式渲染页面。对于IE9，则以IE9标准模式渲染页面。
- EmulateIE9：如果有文档类型声明，则以IE9 标准模式渲染页面，否则将文档模式设置为IE5。
- EmulateIE8：如果有文档类型声明，则以IE8 标准模式渲染页面，否则将文档模式设置为IE5。
- EmulateIE7：如果有文档类型声明，则以IE7 标准模式渲染页面，否则将文档模式设置为IE5。
- 9：强制以IE9 标准模式渲染页面，忽略文档类型声明。
- 8：强制以IE8 标准模式渲染页面，忽略文档类型声明。
- 7：强制以IE7 标准模式渲染页面，忽略文档类型声明。
- 5：强制将文档模式设置为IE5，忽略文档类型声明。

比如，要想让文档模式像在IE7中一样，可以使用下面这行代码：

```html
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7">
```

没有规定说必须在页面中设置X-UA-Compatible。默认情况下，浏览器会通过文档类型声明来确定是使用最佳的可用文档模式，还是使用混杂模式。

通过document.documentMode属性可以知道给定页面使用的是什么文档模式。这个属性是IE8中新增的，它会返回使用的文档模式的版本号（在IE9中，可能返回的版本号为5、7、8、9）：

```javascript
var mode = document.documentMode;
```

知道页面采用的是什么文档模式，有助于理解页面的行为方式。无论在什么文档模式下，都可以访问这个属性。

## children属性

由于IE9之前的版本与其它浏览器在处理文本节点中的空白符时有差异，因此就出现了children属性。这个属性是与childNodes没有什么区别，即在元素只包含元素子节点时，这两个属性的值相同。下面是访问children属性的示例代码：

```javascript
var childCount = element.children.length;
var firstChild = element.children[0];
```

支持children属性的浏览器有IE5、Firefox 3.5、Safari 2（但有bug）、Safari 3（完全支持）、Opera8和Chrome（所有版本）。IE8 及更早版本的children 属性中也会包含注释节点，但IE9 之后的版本则只返回元素节点。

## contains()方法

在实际开发中，经常需要知道某个节点是不是另一个节点的后代。IE为此率先引入了contains()方法，以便不通过在DOM文档树中查找即可获得这个信息。调用contains()方法的应该是祖先节点，也就是搜索开始的节点，这个方法接收一个参数，即要检测的后代节点。如果被检测的节点是后代节点，该方法返回true；否则，返回false。以下是一个例子：

```javascript
alert(document.documentElement.contains(document.body));    // true
```

这个例子测试了`<body>`元素是不是`<html>`元素的后代，在格式正确的HTML页面中，以上代码返回true。支持contains()方法的浏览器有IE、Firefox 9+、Safari、Opera和Chrome。

使用DOM Level3 compareDocumentPosition()也能够确定节点间的关系。支持这个方法的浏览器有IE9+、Firefox、Safari、Opera 9.5+和Chrome。如前所述，这个方法用于确定两个节点间的关系，返回一个表示该关系的位掩码（ bitmask）。下表列出了这个位掩码的值。

|掩码|节点关系|
|:---:|:---:|
|1|无关（给定的节点不在当前文档中）|
|2|居前（给定的节点在DOM树中位于参考节点之前）|
|4|居后（给定的节点在DOM树中位于参考节点之后）|
|8|包含（给定的节点是参考节点的祖先）|
|16|被包含（给定的节点是参考节点的后代）|

为模仿contains()方法，应该关注的是掩码16。可以对compareDocumentPosition()的结果执行按位与，以确定参考节点（调用compareDocumentPosition()方法的当前节点）是否包含给定的节点（传入的节点）。来看下面的例子：

```javascript
var result = document.documentElement.compareDocumentPosition(document.body);
alert(!!(result & 16));
```

执行上面的代码后，结果会变成20（表示"居后"的4加上表示"被包含"的16）。对掩码16执行按位操作会返回一个非零数值，而两个逻辑非操作符会将该数值转换成布尔值。

使用一些浏览器及能力检测，就可以写出如下所示的一个通用的contains函数：

```javascript
function contains(refNode, otherNode) {
    if (typeof refNode.contains == "function" && (!client.engine.webkit || client.engine.webkit >= 522)) {
        return refNode.contains(otherNode);
    } else if (typeof refNode.compareDocumentPosition == "function") {
        return !!(refNode.compareDocumentPosition(otherNode) & 16);
    } else {
        var node = otherNode.parentNode;
        do {
            if (node === refNode) {
                return true;
            } else {
                node = node.parentNode;
            }
        } while (node !== null);
        return false;
    }
}
```

这个函数组合使用了三种方式来确定一个节点是不是另一个节点的后代。函数的第一个参数是参考节点，第二个参数是要检查的节点。在函数体内，首先检测refNode 中是否存在contains()方法（能
力检测）。这一部分代码还检查了当前浏览器所用的WebKit 版本号。如果方法存在而且不是WebKit（!client.engine.webkit），则继续执行代码。否则，如果浏览器是WebKit 且至少是Safari 3（WebKit
版本号为522 或更高），那么也可以继续执行代码。在WebKit 版本号小于522 的Safari 浏览器中，contains()方法不能正常使用。

接下来检查是否存在compareDocumentPosition()方法，而函数的最后一步则是自otherNode开始向上遍历DOM 结构，以递归方式取得parentNode，并检查其是否与refNode 相等。在文档树的顶端，parentNode 的值等于null，于是循环结束。这是针对旧版本Safari 设计的一个后备策略。

## 插入文本

IE原来专有的插入标记的属性innerHTML和outerHTML已经被HTML5纳入规范。但另外两个插入文本的专有属性则没有这么好的运气。这两个没有被HTML5看中的属性是innerText和outerText。

### innerText属性

通过innerText属性可以操作元素中包含的所有文本内容，包括子文档树中的文本。在通过innerText读取值时，它会按照由浅入深的顺序，将子文档树中的所有文本拼接起来。在通过innerText写入值时，结果会删除元素的所有子节点，插入包含相应文本值的文本节点。来看下面这个HTML代码示例。

```html
<div id="content">
    <p>This is a <strong>paragraph</strong> with a list following it.</p>
    <ul>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
    </ul>
</div>
```

对于这个例子中的`<div>`元素而言，其innerText属性会返回下列字符串：

```javascript
This is a paragraph with a list following it.
Item 1
Item 2
Item 3
```

由于不同浏览器处理空白符的方式不同，因此输出的文本可能会也可能不会包含原始HTML代码中的缩进。

使用innerText属性设置这个`<div>`元素的内容，则只需要一行代码：

```javascript
div.innerText = "Hello world!";
```

执行这行代码后，页面的HTML代码就会变成如下所示。

```html
<div id="content">Hello world!</div>
```

设置innerText属性移除了先前存在的所有子节点，完全改变了DOM子树。此外，设置innerText属性的同时，也对文本中存在的HTML语法字符（小于号、大于号、引号即和号）进行了编码。再看一个例子。

```javascript
div.innerText = "Hello & welcome, <b>\"reader\"!</b>";
```

运行以上代码之后，会得到如下所示的结果。

```html
<div id="content">Hello &amp; welcome, &lt;b&gt;&quot;reader&quot;!&lt;/b&gt;</div>
```

设置innerText永远只会生成当前节点的一个子文本节点，而为了确保只生成一个子文本节点，就必须要对文本进行HTML编码。利用这一点，可以通过innerText属性过滤掉HTML标签。方法是将innerText设置为等于innerText，这样就可以去掉所有HTML标签，比如：

```javascript
div.innerText = div.innerText;
```

执行这行代码后，就用原来的文本内容替换了容器元素中的所有内容（包括子节点、因而也就去掉了HTML标签）。

支持innerText属性的浏览器包括IE4+、Safari 3+、Opera 8+和Chrome。Firefox 虽然不支持innerText，但支持作用类似的textContent 属性。textContent 是DOM Level 3 规定的一个属性，其他支持textContent 属性的浏览器还有IE9+、Safari 3+、Opera 10+和Chrome。为了确保跨浏览器兼容，有必要编写一个类似于下面的函数来检测可以使用哪个属性。

```javascript
function getInnerText(element) {
    return (typeof element.textContent = "string") ? element.textContent : element.innerText;
}
function setInnerText(element, text) {
    if (typeof element.textContent == "string") {
        element.textContent = text;
    } else {
        element.innerText = text;
    }
}
```

这两个函数都接收一个元素作为参数，然后检查这个元素是不是有textContent属性。如果有，那么typeof element.textContent应该是"string"；如果没有，那么这两个函数就会改为使用innerText。可以像下面这样调用这两个函数。

```javascript
setInnerText(div, "Hello world!");
alert(getInnerText(div));    // Hello world!
```

使用这两个函数可以确保在不同的浏览器中使用正确的属性。

### outerText属性

除了作用范围扩大到了包含调用它的节点之外，outerText与innerText基本上没有多大区别。在读取文本值时，outerText与innerText的结果完全一样。但在写模式下，outerText就完全不同了：outerText不只是替换调用它的元素的子节点，而是会替换整个元素（包括子节点）。比如：

```javascript
div.outerText = "Hello world!";
```

这行代码实际上相当于如下两行代码：

```javascript
var text = document.createTextNode("Hello world!");
div.parentNode.replaceChild(text, div);
```

本质上，新的文本节点会完全取代调用outerText 的元素。此后，该元素就从文档中被删除，无法访问。

支持outerText 属性的浏览器有IE4+、Safari 3+、Opera 8+和Chrome。由于这个属性会导致调用它的元素不存在，因此并不常用。我们也建议读者尽可能不要使用这个属性。

## 滚动

如前所述，HTML5 之前的规范并没有就与页面滚动相关的API 做出任何规定。但HTML5 在将scrollIntoView()纳入规范之后，仍然还有其他几个专有方法可以在不同的浏览器中使用。下面列出的几个方法都是对HTMLElement 类型的扩展，因此在所有元素中都可以调用。

- scrollIntoViewIfNeeded(alignCenter)：只在当前元素在视口中不可见的情况下，才滚动浏览器窗口或容器元素，最终让它可见。如果当前元素在视口中可见，这个方法什么也不做。如果将可选的alignCenter 参数设置为true，则表示尽量将元素显示在视口中部（垂直方向）。Safari 和Chrome 实现了这个方法。
- scrollByLines(lineCount)：将元素的内容滚动指定的行高，lineCount 值可以是正值，也可以是负值。Safari 和Chrome 实现了这个方法。
- scrollByPages(pageCount)：将元素的内容滚动指定的页面高度，具体高度由元素的高度决定。Safari 和Chrome 实现了这个方法。

希望大家要注意的是，scrollIntoView()和scrollIntoViewIfNeeded()的作用对象是元素的容器，而scrollByLines()和scrollByPages()影响的则是元素自身。下面还是来看几个实例吧。

```javascript
// 将页面主体滚动5行
document.body.scrollByLines(5);
// 在当前元素不可见的时候，让它进入浏览器的视口
document.images[0].scrollIntoViewIfNeeded();
// 将页面主体往回滚动1页
document.body.scrollByPages(-1);
```

由于scrollIntoView()是唯一一个所有浏览器都支持的方法，因此还是这个方法最常用。

# 上述小结

虽然DOM 为与XML 及HTML 文档交互制定了一系列核心API，但仍然有几个规范对标准的DOM进行了扩展。这些扩展中有很多原来是浏览器专有的，但后来成为了事实标准，于是其他浏览器也都提
供了相同的实现。本章介绍的三个这方面的规范如下。

- Selectors API，定义了两个方法，让开发人员能够基于CSS 选择符从DOM中取得元素，这两个方法是querySelector()和querySelectorAll()。
- Element Traversal，为DOM元素定义了额外的属性，让开发人员能够更方便地从一个元素跳到另一个元素。之所以会出现这个扩展，是因为浏览器处理DOM 元素间空白符的方式不一样。
- HTML5，为标准的DOM 定义了很多扩展功能。其中包括在innerHTML 属性这样的事实标准基础上提供的标准定义，以及为管理焦点、设置字符集、滚动页面而规定的扩展API。

虽然目前DOM 扩展的数量还不多，但随着Web 技术的发展，相信一定还会涌现出更多扩展来。很多浏览器都在试验专有的扩展，而这些扩展一旦获得认可，就能成为“伪”标准，甚至会被收录到规范的更新版本中。

# DOM变化

## 其它方面的变化

DOM其它部分在"DOM2级核心"中也发生了一些变化。这些变化与XML命名空间无关，而是更倾向于确保API的可靠性及完整性。

### DocumentType类型的变化

DocumentType类型新增了3个属性：publicId、systemId和internalSubset。其中，前两个属性表示的是文档类型声明中的两个信息段，这两个信息段在DOM1级中是没有办法访问到的。以下面的HTML文档类型声明为例。

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
"http://www.w3.org/TR/html4/strict.dtd">
```

对这个文档类型声明而言，publicId是"-//W3C//DTD HTML 4.01//EN"，而systemId是"http://www.w3.org/TR/html4/strict.dtd" 。在支持DOM2级的浏览器中，应该可以运行下列代码。

```javascript
alert(document.doctype.publicId);
alert(document.doctype.systemId);
```

实际上，很少需要在网页中访问此类信息。

最后一个属性internalSubset，用于访问包含在文档类型声明中的额外定义，以下面的代码为例。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"
[<!ELEMENT name (#PCDATA)>] >
```

访问document.doctype.internalSubset将得到`"<!ELEMENT name (#PCDATA)>"`。这种内部子集（internal subset）在HTML中极少用到，在XML中可能会更常见一些。

### Document类型的变化

Document类型的变化中唯一与命名空间无关的方法是importNode()。这个方法的用途是从一个文档中取得一个节点，然后将其导入到另一个文档中，使其成为这个文档结构的一部分。需要注意的是，每个节点都有一个ownerDocument属性，表示所属的文档。如果调用appendChild()传入的节点属于不同的文档（ownerDocument属性的值不一样），则会导致错误。但在调用importNode()时传入不同文档的节点则会返回一个新节点，这个新节点的所有权归当前文档所有。

说起来，importNode()方法与Element的cloneNode()方法非常相似，它接受两个参数：要复制的节点和一个表示是否复制子节点的布尔值。返回的结果是原来节点的副本，但能够在当前文档中使用。来看下面的例子：

```javascript
var newNode = document.importNode(oldNode, true); //导入节点及其所有子节点
document.body.appendChild(newNode);
```

这个方法在HTML文档中并不常用，在XML文档中用的比较多。

"DOM2级视图"模块添加了一个名为defaultView的属性，其中保存着一个指针，指向拥有给定文档的窗口（或框架）。初次之外，"视图"规范没有提供什么时候其它视图可用的信息，因而这是唯一一个新增的属性。除IE之外的所有浏览器都支持defaultView属性。在IE中一个等价的属性名叫parentWindow（Opera也支持这个属性）。因此，要确定文档归属窗口，可以使用以下代码。

```javascript
var parentWindow = document.defaultView || document.parentWindow;
```

除了上述一个方法和一个属性之外，“DOM2 级核心”还为document.implementation 对象规定了两个新方法：createDocumentType()和createDocument()。前者用于创建一个新的DocumentType节点，接受3 个参数：文档类型名称、publicId、systemId。例如，下列代码会创建一个新的HTML 4.01 Strict 文档类型。

```javascript
var doctype = document.implementation.createDocumentType("html",
"-//W3C//DTD HTML 4.01//EN",
"http://www.w3.org/TR/html4/strict.dtd");
```

由于既有文档的文档类型不能改变，因此createDocumentType()只在创建新文档时有用；创建新文档时需要用到createDocument()方法。这个方法接受3 个参数：针对文档中元素的namespaceURI、文档元素的标签名、新文档的文档类型。下面这行代码将会创建一个空的新XML 文档。

```javascript
var doc = document.implementation.createDocument("", "root", null);
```

这行代码会创建一个没有命名空间的新文档，文档元素为`<root>`，而且没有指定文档类型。要想创建一个XHTML文档，可以使用以下代码。

```javascript
var doctype = document.implementation.createDocumentType("html",
" -//W3C//DTD XHTML 1.0 Strict//EN",
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd");
var doc = document.implementation.createDocument("http://www.w3.org/1999/xhtml",
"html", doctype);
```

这样，就创建了一个带有适当命名空间和文档类型的新的XHTML。不过，新文档当前只有文档元素`<html>`，剩下的所有元素都需要继续添加。

“DOM2 级HTML”模块也为document.implementation 新增了一个方法，名叫createHTMLDocument()。这个方法的用途是创建一个完整的HTML 文档，包括`<html>`、`<head>`、`<title>`和`<body>`元素。这个方法只接受一个参数，即新创建文档的标题（放在`<title>`元素中的字符串），返回新的HTML 文档，如下所示：

```javascript
var htmldoc = document.implementation.createHTMLDocument("New Doc");
alert(htmldoc.title); //"New Doc"
alert(typeof htmldoc.body); //"object"
```

通过调用createHTMLDocument()创建的这个文档，是HTMLDocument类型的实例，因而具有该类型的所有属性和方法，包括title和body属性。只有Opera和Safari支持这个方法。

### Node类型的变化

Node 类型中唯一与命名空间无关的变化，就是添加了isSupported()方法。与DOM1级为document.implementation 引入的hasFeature()方法类似，isSupported()方法用于确定当前节点具有什么能力。这个方法也接受相同的两个参数：特性名和特性版本号。如果浏览器实现了相应特性，而且能够基于给定节点执行该特性，isSupported()就返回true。来看一个例子：

```javascript
if (document.body.isSupported("HTML", "2.0")) {
    // 执行只有"DOM2"级"HTML"才支持的操作
}
```

由于不同实现在决定对什么特性返回true 或false 时并不一致，这个方法同样也存在与hasFeature()方法相同的问题。为此，我们建议在确定某个特性是否可用时，最好还是使用能力检测。

DOM3 级引入了两个辅助比较节点的方法：isSameNode()和isEqualNode()。这两个方法都接受一个节点参数，并在传入节点与引用的节点相同或相等时返回true。所谓相同，指的是两个节点引用的是同一个对象。所谓相等，指的是两个节点是相同的类型，具有相等的属性（nodeName、nodeValue，等等），而且它们的attributes 和childNodes 属性也相等（相同位置包含相同的值）。来看一个例子。

```javascript
var div1 = document.createElement("div");
div1.setAttribute("class", "box");
var div2 = document.createElement("div");
div2.setAttribute("class", "box");
alert(div1.isSameNode(div1)); //true
alert(div1.isEqualNode(div2)); //true
alert(div1.isSameNode(div2)); //false
```

这里创建了两个具有相同特性的`<div>`元素。这两个元素相等，但不相同。

DOM3 级还针对为DOM 节点添加额外数据引入了新方法。其中，setUserData()方法会将数据指定给节点，它接受3 个参数：要设置的键、实际的数据（可以是任何数据类型）和处理函数。以下代码可以将数据指定给一个节点。

```javascript
document.body.setUserData("name", "Nicholas", function () {});
```

然后，使用getUserData()并传入相同的键，就可以取得该数据，如下所示：

```javascript
var value = document.body.getUserData("name");
```

传入setUserData()中的处理函数会在带有数据的节点被复制、删除、重命名或引入一个文档时调用，因而你可以事先决定在上述操作发生时如何处理用户数据。处理函数接受5 个参数：表示操作类型的数值（1 表示复制，2 表示导入，3 表示删除，4 表示重命名）、数据键、数据值、源节点和目标节点。在删除节点时，源节点是null；除在复制节点时，目标节点均为null。在函数内部，你可以决定如何存储数据。来看下面的例子。

```javascript
var div = document.createElement("div");
div.setUserData("name", "Nicholas", function(operation, key, value, src, dest){
    if (operation == 1){
        dest.setUserData(key, value, function(){}); 
    }
});
var newDiv = div.cloneNode(true);
alert(newDiv.getUserData("name"));    // Nicholas
```

这里，先创建了一个`<div>`元素，然后又为它添加了一些数据（用户数据）。在使用cloneNode()复制这个元素时，就会调用处理函数，从而将数据自动复制到了副本节点。结果在通过副本节点调用
getUserData()时，就会返回与原始节点中包含的相同的值。

### 框架的变化

框架和内嵌框架分别用HTMLFrameElement 和HTMLIFrameElement 表示，它们在DOM2级中都有了一个新属性，名叫contentDocument。这个属性包含一个指针，指向表示框架内容的文档对象。在此之前，无法直接通过元素取得这个文档对象（只能使用frames 集合）。可以像下面这样使用这个属性。

```javascript
var iframe = document.getElementById("myIframe");
var iframeDoc = iframe.contentDocument; //在IE8 以前的版本中无效
```

由于contentDocument 属性是Document 类型的实例，因此可以像使用其他HTML 文档一样使用它，包括所有属性和方法。Opera、Firefox、Safari 和Chrome 支持这个属性。IE8 之前不支持框架中
的contentDocument 属性，但支持一个名叫contentWindow 的属性，该属性返回框架的window 对象，而这个window 对象又有一个document 属性。因此，要想在上述所有浏览器中访问内嵌框架的文
档对象，可以使用下列代码。

```javascript
var iframe = document.getElementById("myIframe");
var iframeDoc = iframe.contentDocument || iframe.contentWindow.document;
```

所有浏览器都支持contentWindow属性。

# 样式

在HTML 中定义样式的方式有3 种：通过<link/>元素包含外部样式表文件、使用<style/>元素定义嵌入式样式，以及使用style 特性定义针对特定元素的样式。“DOM2 级样式”模块围绕这3 种应用样式的机制提供了一套API。要确定浏览器是否支持DOM2 级定义的CSS 能力，可以使用下列代码。

```javascript
var supportsDOM2CSS = document.implementation.hasFeature("CSS", "2.0");
var supportsDOM2CSS2 = document.implementation.hasFeature("CSS2", "2.0");
```

## 访问元素的样式

任何支持style特性的HTML元素在JavaScript中都有一个对应的style属性。这个style对象是CSSStyleDeclaration的实例，包含着通过HTML的style 特性指定的所有样式信息，但不包含与外部样式表或嵌入样式表经层叠而来的样式。在style特性中指定的任何CSS属性都将表现为这个style对象的相应属性。对于使用短划线（分隔不同的词汇，例如background-image）的CSS属性名，必须将其转换成驼峰大小写形式，才能通过JavaScript来访问。下面列出了几个常见的CSS属性及其在style对象中对应的属性名。

|CSS属性|JavaScript属性|
|:---:|:---:|
|background-image|style.backgroundImage|
|color|style.color|
|display|style.display|
|font-family|style.fontFamily|

多数情况下，都可以通过简单地转换属性名的格式来实现转换。其中一个不能直接转换的CSS 属性就是float。由于float 是JavaScript 中的保留字，因此不能用作属性名。“DOM2 级样式”规范规定样式对象上相应的属性名应该是cssFloat；Firefox、Safari、Opera 和Chrome 都支持这个属性，而IE支持的则是styleFloat。

只要取得一个有效的DOM元素的引用，就可以随时使用JavaScript为其设置样式。以下是几个例子。

```javascript
var myDiv = document.getElementById("myDiv");
//设置背景颜色
myDiv.style.backgroundColor = "red";
//改变大小
myDiv.style.width = "100px";
myDiv.style.height = "200px";
//指定边框
myDiv.style.border = "1px solid black";
```

在以这种方式改变样式时，元素的外观会自动被更新。

通过style对象同样可以取得在style特性中指定的样式。以下面的HTML代码为例。

```html
<div id="myDiv" style="background-color:blue; width:10px; height:25px"></div>
```

在style特性中指定的样式信息可以通过下列代码取得。

```javascript
alert(myDiv.style.backgroundColor); //"blue"
alert(myDiv.style.width); //"10px"
alert(myDiv.style.height); //"25px"
```

如果没有为元素设置style特性，那么style对象中可能会包含一些默认的值，但这些值并不能准确地反映该元素的样式信息。

### DOM样式属性和方法

“DOM2级样式”规范还为style 对象定义了一些属性和方法。这些属性和方法在提供元素的style特性值的同时，也可以修改样式。下面列出了这些属性和方法。

- cssText：如前所述，通过它能够访问到style 特性中的CSS 代码。
- length：应用给元素的CSS 属性的数量。
- parentRule：表示CSS 信息的CSSRule 对象。本节后面将讨论CSSRule 类型。
- getPropertyCSSValue(propertyName)：返回包含给定属性值的CSSValue 对象。
- getPropertyPriority(propertyName)：如果给定的属性使用了!important 设置，则返回"important"；否则，返回空字符串。
- getPropertyValue(propertyName)：返回给定属性的字符串值。
- item(index)：返回给定位置的CSS 属性的名称。
- removeProperty(propertyName)：从样式中删除给定属性。
- setProperty(propertyName,value,priority)：将给定属性设置为相应的值，并加上优先权标志（"important"或者一个空字符串）。

通过cssText属性可以访问style特性中的CSS代码。在读取模式下，返回浏览器对style特性中CSS代码的内部表示。在写入模式下，赋给cssText的值会重写整个style特性的值；也就是说，以前通过style特性指定的样式信息都将丢失。例如，如果通过style特性为元素设置了边框，然后再以不包含边框的规则重写cssText，那么就会抹去元素上的边框。下面是使用cssText属性的一个例子。

```javascript
myDiv.style.cssText = "width: 25px; height: 100px; background-color: green";
alert(myDiv.style.cssText);
```

设置cssText 是为元素应用多项变化最快捷的方式，因为可以一次性地应用所有变化。

设计length 属性的目的，就是将其与item()方法配套使用，以便迭代在元素中定义的CSS 属性。在使用length 和item()时，style 对象实际上就相当于一个集合，都可以使用方括号语法来代替item()来取得给定位置的CSS 属性，如下面的例子所示。

```javascript
for (var i = 0. len = myDiv.style.length; i < len; i++) {
    alert(myDiv.style[i]);   // 或者myDiv.style.item(i);
}
```

无论是使用方括号语法还是使用item()语法，都可以去的CSS属性名（"background-color"，不是"backgroundColor"）。然后，就可以在getPropertyValue()中使用取得的属性名进一步取得属性的值。如下所示。

```javascript
var prop, value, i, len;
for (i=0, len=myDiv.style.length; i < len; i++){
    prop = myDiv.style[i]; //或者 myDiv.style.item(i)
    value = myDiv.style.getPropertyValue(prop);
    alert(prop + " : " + value);
}
```

getPropertyValue()方法取得的始终都是CSS 属性值的字符串表示。如果你需要更多信息，可以使用getPropertyCSSValue()方法，它返回一个包含两个属性的CSSValue 对象，这两个属性分别是：cssText 和cssValueType。其中，cssText 属性的值与getPropertyValue()返回的值相同，而cssValueType 属性则是一个数值常量，表示值的类型：0 表示继承的值，1 表示基本的值，2 表示值列表，3 表示自定义的值。以下代码既输出CSS 属性值，也输出值的类型。

```javascript
var prop, value, i, len;
for (i=0, len=myDiv.style.length; i < len; i++){
    prop = myDiv.style[i]; //或者myDiv.style.item(i)
    value = myDiv.style.getPropertyCSSValue(prop);
    alert(prop + " : " + value.cssText + " (" + value.cssValueType + ")");
}
```

在实际开发中，getPropertyCSSValue()使用得比getPropertyValue()少得多。。IE9+、Safari 3+以及Chrome 支持这个方法。Firefox 7 及之前版本也提供这个访问，但调用总返回null。

要从元素的样式中移除某个CSS 属性，需要使用removeProperty()方法。使用这个方法移除一个属性，意味着将会为该属性应用默认的样式（从其他样式表经层叠而来）。例如，要移除通过style特性设置的 border 属性，可以使用下面的代码。

```javascript
myDiv.style.removeProperty("border");
```

在不确定某个给定的CSS 属性拥有什么默认值的情况下，就可以使用这个方法。只要移除相应的属性，就可以为元素应用默认值。

### 计算的样式

虽然style 对象能够提供支持style 特性的任何元素的样式信息，但它不包含那些从其他样式表层叠而来并影响到当前元素的样式信息。“DOM2 级样式”增强了document.defaultView，提供了
getComputedStyle()方法。这个方法接受两个参数：要取得计算样式的元素和一个伪元素字符串（例如":after"）。如果不需要伪元素信息，第二个参数可以是null。getComputedStyle()方法返回一个CSSStyleDeclaration 对象（与style 属性的类型相同），其中包含当前元素的所有计算的样式。以下面这个HTML 页面为例。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Computed Styles Example</title>
    <style type="text/css">
        #myDiv {
            background-color: blue;
            width: 100px;
            height: 200px;
        }
    </style>
</head>
<body>
    <div id="myDiv" style="background-color: red; border: 1px solid black"></div>
</body>
</html>
```

应用给这个例子中`<div>`元素的样式一方面来自嵌入式样式表（`<style>`元素中的样式），另一方面来自其style特性。但是，style特性中设置了backgroundColor和border，没有设置width和height，后者是通过样式表规则应用的。以下代码可以取得这个元素计算后的样式。

```javascript
var myDiv = document.getElementById("myDiv");
var computedStyle = document.defaultView.getComputedStyle(myDiv, null);
alert(computedStyle.backgroundColor); // "red"
alert(computedStyle.width); // "100px"
alert(computedStyle.height); // "200px"
alert(computedStyle.border); // 在某些浏览器中是"1px solid black"
```

在这个元素计算后的样式中，背景颜色的值是"red"，宽度值是"100px"，高度值是"200px"。我们注意到，背景颜色不是"blue"，因为这个样式在自身的style 特性中已经被覆盖了。边框属性可能
会也可能不会返回样式表中实际的border 规则（Opera 会返回，但其他浏览器不会）。存在这个差别的原因是不同浏览器解释综合（rollup）属性（如border）的方式不同，因为设置这种属性实际上会涉及很多其他属性。在设置border 时， 实际上是设置了四个边的边框宽度、颜色、样式属性（ border-left-width 、border-top-color 、border-bottom-style ， 等等）。因此， 即使computedStyle.border 不会在所有浏览器中都返回值，但computedStyle.borderLeftWidth 会
返回值。

IE不支持getComputedStyle()方法，但它有一种类似的概念。在IE中，每个具有style属性的元素还有一个currentStyle属性。这个属性是CSSStyleDeclaration的实例，包含当前元素全部计算后的样式。取得这些样式的方法也差不多，如下面的例子所示。

```javascript
var myDiv = document.getElementById("myDiv");
var computedStyle = myDiv.currentStyle;
alert(computedStyle.backgroundColor); //"red"
alert(computedStyle.width); //"100px"
alert(computedStyle.height); //"200px"
alert(computedStyle.border); //undefined
```

与DOM 版本的方式一样，IE 也没有返回border 样式，因为这是一个综合属性。

无论在哪个浏览器中，最重要的一条是要记住所有计算的样式都是只读的；不能修改计算后样式对象中的CSS 属性。此外，计算后的样式也包含属于浏览器内部样式表的样式信息，因此任何具有默认值
的CSS 属性都会表现在计算后的样式中。例如，所有浏览器中的visibility 属性都有一个默认值，但这个值会因实现而异。在默认情况下，有的浏览器将visibility 属性设置为"visible"，而有的浏览器则将其设置为"inherit"。换句话说，不能指望某个CSS 属性的默认值在不同浏览器中是相同的。如果你需要元素具有某个特定的默认值，应该手工在样式表中指定该值。

## 操作样式表

CSSStyleSheet 类型表示的是样式表，包括通过`<link>`元素包含的样式表和在`<style>`元素中定义的样式表。有读者可能记得，这两个元素本身分别是由HTMLLinkElement 和HTMLStyleElement 类型表示的。但是，CSSStyleSheet 类型相对更加通用一些，它只表示样式表，而不管这些样式表在HTML中是如何定义的。此外，上述两个针对元素的类型允许修改HTML 特性，但CSSStyleSheet 对象则是一套只读的接口（有一个属性例外）。使用下面的代码可以确定浏览器是否支持DOM2 级样式表。

```javascript
var supportsDOM2StyleSheets =
document.implementation.hasFeature("StyleSheets", "2.0");
```

CSSStyle继承自StyleSheet，后者可以作为一个基础接口来定义非CSS样式表。从StyleSheet接口继承而来的属性如下。

- disabled：表示样式表是否被禁用的布尔值。这个属性是可读/写的，将这个值设置为true 可以禁用样式表。
- href：如果样式表是通过`<link>`包含的，则是样式表的URL；否则，是null。
- media：当前样式表支持的所有媒体类型的集合。与所有DOM 集合一样，这个集合也有一个length 属性和一个item()方法。也可以使用方括号语法取得集合中特定的项。如果集合是空列表，表示样式表适用于所有媒体。在IE 中，media 是一个反映`<link>`和`<style>`元素media特性值的字符串。
- ownerNode：指向拥有当前样式表的节点的指针，样式表可能是在HTML 中通过`<link>`或<style/>引入的（在XML 中可能是通过处理指令引入的）。如果当前样式表是其他样式表通过@import 导入的，则这个属性值为null。IE 不支持这个属性。
- parentStyleSheet：在当前样式表是通过@import 导入的情况下，这个属性是一个指向导入它的样式表的指针。
- title：ownerNode 中title 属性的值。
- type：表示样式表类型的字符串。对CSS 样式表而言，这个字符串是"type/css"。

除了disabled属性之外，其它属性都是只读的。在支持以上所有这些属性的基础上，CSSStyleSheet类型还支持下列属性和方法：

- cssRules：样式表中包含的样式规则的集合。IE不支持这个属性，但有一个类似的rules属性。
- ownerRule：如果样式表示通过@import导入的，这个属性就是一个指针，指向表示导入的规则；否则，值为null。IE不支持这个属性。
- deleteRule(index)：删除cssRules集合中指定位置的规则。IE不支持这个方法，但支持一个类似的removeRule()方法。
- insertRule(rule, index)：向cssRules集合中指定的位置插入rule字符串。IE不支持这个方法，但支持一个类似的addRule()方法。

应用于文档的所有样式表是通过document.styleSheets 集合来表示的。通过这个集合的length属性可以获知文档中样式表的数量，而通过方括号语法或item()方法可以访问每一个样式表。来看一个例子。

```javascript
var sheet = null;
for (var i = 0, len = document.styleSheets.length; i < len; i++) {
    sheet = document.styleSheets[i];
    alert(sheet.href);
}
```

以上代码可以输出文档中使用的每一个样式表的href属性（`<style>`元素包含的样式表没有href属性）。

不同浏览器的document.styleSheets 返回的样式表也不同。所有浏览器都会包含`<style>`元素和rel 特性被设置为"stylesheet"的`<link>`元素引入的样式表。IE 和Opera 也包含rel 特性被设置为"alternate stylesheet"的`<link>`元素引入的样式表。

也可以直接通过`<link>`或`<style>`元素取得CSSStyleSheet 对象。DOM 规定了一个包含CSSStyleSheet 对象的属性，名叫sheet；除了IE，其他浏览器都支持这个属性。IE 支持的是styleSheet 属性。要想在不同浏览器中都能取得样式表对象，可以使用下列代码。

```javascript
function getStyleSheet(element) {
    return element.sheet || element.styleSheet;
}
// 取得第一个<link/>元素引入的样式表
var link = document.getElementsByTagName("link")[0];
var sheet = getStyleSheet(link);
```

这里的getStyleSheet()返回的样式表对象与document.styleSheets 集合中的样式表对象相同。

### CSS规则

CSSRule 对象表示样式表中的每一条规则。实际上，CSSRule 是一个供其他多种类型继承的基类型，其中最常见的就是CSSStyleRule 类型，表示样式信息（其他规则还有@import、@font-face、@page 和@charset，但这些规则很少有必要通过脚本来访问）。CSSStyleRule 对象包含下列属性。

- cssText：返回整条规则对应的文本。由于浏览器对样式表的内部处理方式不同，返回的文本可能会与样式表中实际的文本不一样；Safari 始终都会将文本转换成全部小写。IE 不支持这个属性。
- parentRule：如果当前规则是导入的规则，这个属性引用的就是导入规则；否则，这个值为null。IE 不支持这个属性。
- parentStyleSheet：当前规则所属的样式表。IE 不支持这个属性。
- selectorText：返回当前规则的选择符文本。由于浏览器对样式表的内部处理方式不同，返回的文本可能会与样式表中实际的文本不一样（例如，Safari 3 之前的版本始终会将文本转换成全部小写）。在Firefox、Safari、Chrome 和IE 中这个属性是只读的。Opera 允许修改selectorText。
- style：一个CSSStyleDeclaration 对象，可以通过它设置和取得规则中特定的样式值。
- type：表示规则类型的常量值。对于样式规则，这个值是1。IE 不支持这个属性。

其中三个最常用的属性是cssText、selectorText和style。cssText属性与style.cssText属性类似，但并不相同。前者包含选择符文本和围绕样式信息的花括号，后者只包含样式信息（类似于元素的style.cssText）。此外，cssText是只读的，而style.cssText也可以被重写。

大多数情况下，仅使用style 属性就可以满足所有操作样式规则的需求了。这个对象就像每个元素上的style 属性一样，可以通过它读取和修改规则中的样式信息。以下面的CSS 规则为例。

```css
div.box {
    background-color: blue;
    width: 100px;
    height: 200px;
}
```

假设这条规则位于页面中的第一个样式表中，而且这个样式表中只有这一条样式规则，那么通过下列代码可以取得这条规则的各种信息。

```javascript
var sheet = document.styleSheets[0];
var rules = sheet.cssRules || sheet.rules; //取得规则列表
var rule = rules[0]; //取得第一条规则
alert(rule.selectorText); //"div.box"
alert(rule.style.cssText); //完整的CSS 代码
alert(rule.style.backgroundColor); //"blue"
alert(rule.style.width); //"100px"
alert(rule.style.height); //"200px"
```

使用这种方式，可以像确定元素的行内样式信息一样，确定与规则相关的样式信息。与使用元素的方式一样，在这种方式下也可以修改样式信息，如下面的例子所示。

```javascript
var sheet = document.styleSheets[0];
var rules = sheet.cssRules || sheet.rules; //取得规则列表
var rule = rules[0]; //取得第一条规则
rule.style.backgroundColor = "red"
```

必须要注意的是，以这种方式修改规则会影响页面中适用于该规则的所有元素。换句话说，如果有两个带有box 类的`<div>`元素，那么这两个元素都会应用修改后的样式。

### 创建规则

DOM 规定，要向现有样式表中添加新规则，需要使用insertRule()方法。这个方法接受两个参数：规则文本和表示在哪里插入规则的索引。下面是一个例子。

```javascript
sheet.insertRule("body { background-color: silver }", 0); //DOM 方法
```

这个例子插入的规则会改变元素的背景颜色。插入的规则将称为样式表中的第一条规则（插入到了位置0）——规则的次序在确定层叠之后应用到文档的规则时至关重要。Firefox、Safari、Opera 和Chrome
都支持insertRule()方法。

IE8 及更早版本支持一个类似的方法，名叫addRule()，也接收两必选参数：选择符文本和CSS样式信息；一个可选参数：插入规则的位置。在IE 中插入与前面例子相同的规则，可使用如下代码。

```javascript
sheet.addRule("body", "background-color: silver", 0); //仅对IE 有效
```

有关这个方法的规定中说，最多可以使用addRule()添加4095条样式规则。超出这个上限的调用将会导致错误。

要以跨浏览器的方式向样式表中插入规则，可以使用下面的函数。这个函数接受4 个参数：要向其中添加规则的样式表以及与addRule()相同的3 个参数，如下所示。

```javascript
function insertRule(sheet, selectorText, cssText, position){
    if (sheet.insertRule){
        sheet.insertRule(selectorText + "{" + cssText + "}", position);
    } else if (sheet.addRule){
        sheet.addRule(selectorText, cssText, position);
    }
}
```

下面是调用这个函数的实例代码。

```javascript
insertRule(document.styleSheets[0], "body", "background-color: silver", 0);
```

虽然可以像这样来添加规则，但随着要添加规则的增多，这种方法就会变得非常繁琐。因此，如果要添加的规则非常多，还是建议使用动态加载样式表的技术。

### 删除规则

从样式表中删除规则的方法是deleteRule()，这个方法接受一个参数：要删除的规则的位置。例如，要删除样式表中的第一条规则，可以使用以下代码。

```javascript
sheet.deleteRule(0); //DOM 方法
```

IE支持的类似方法叫removeRule()，使用方法相同，如下所示：

```javascript
sheet.removeRule(0);    //仅对IE有效
```

下面是一个能够跨浏览器删除规则的函数。第一个参数是要操作的样式表，第二个参数是要删除的规则的索引。

```javascript
function deleteRule(sheet, index){
    if (sheet.deleteRule){
        sheet.deleteRule(index);
    } else if (sheet.removeRule){
        sheet.removeRule(index);
    }
}
```

调用这个函数的方式如下。

```javascript
deleteRule(document.styleSheets[0], 0);
```

与添加规则相似，删除规则也不是实际Web 开发中常见的做法。考虑到删除规则可能会影响CSS层叠的效果，因此请大家慎重使用。

## 元素大小

本节介绍的属性和方法并不属于“DOM2 级样式”规范，但却与HTML 元素的样式息息相关。DOM中没有规定如何确定页面中元素的大小。IE 为此率先引入了一些属性，以便开发人员使用。目前，所有
主要的浏览器都已经支持这些属性。

### 偏移量

首先要介绍的属性涉及偏移量（offset dimension），包括元素在屏幕上占用的所有可见的空间。元素的可见大小由其高度、宽度决定，包括所有内边距、滚动条和边框大小（注意，不包括外边距）。通过
下列4 个属性可以取得元素的偏移量。

- offsetHeight：元素在垂直方向上占用的空间大小，以像素计。包括元素的高度、（可见的）水平滚动条的高度、上边框高度和下边框高度。
- offsetWidth：元素在水平方向上占用的空间大小，以像素计。包括元素的宽度、（可见的）垂直滚动条的宽度、左边框宽度和右边框宽度。
- offsetLeft：元素的左外边框至包含元素的左内边框之间的像素距离。
- offsetTop：元素的上外边框至包含元素的上内边框之间的像素距离。

其中，offsetLeft 和offsetTop 属性与包含元素有关，包含元素的引用保存在offsetParent属性中。offsetParent 属性不一定与parentNode 的值相等。例如，`<td>`元素的offsetParent 是作为其祖先元素的`<table>`元素，因为`<table>`是在DOM层次中距`<td>`最近的一个具有大小的元素。

要想知道某个元素在页面上的偏移量，将这个元素的offsetLeft 和offsetTop 与其offsetParent的相同属性相加，如此循环直至根元素，就可以得到一个基本准确的值。以下两个函数就可以用于分别取得元素的左和上偏移量。

```javascript
function getElementLeft(element){
    var actualLeft = element.offsetLeft;
    var current = element.offsetParent;
    while (current !== null){
        actualLeft += current.offsetLeft;
        current = current.offsetParent;
    }
    return actualLeft;
}
function getElementTop(element){
    var actualTop = element.offsetTop;
    var current = element.offsetParent;
    while (current !== null){
        actualTop += current. offsetTop;
        current = current.offsetParent;
    }
    return actualTop;
}
```

这两个函数利用offsetParent 属性在DOM 层次中逐级向上回溯，将每个层次中的偏移量属性合计到一块。对于简单的CSS 布局的页面，这两函数可以得到非常精确的结果。对于使用表格和内嵌框架布局的页面，由于不同浏览器实现这些元素的方式不同，因此得到的值就不太精确了。一般来说，页面中的所有元素都会被包含在几个`<div>`元素中，而这些`<div>`元素的offsetParent 又是`<body>`元素，所以getElementLeft()与getElementTop()会返回与offsetLeft 和offsetTop相同的值。

### 客户区大小

元素的客户区大小（client dimension），指的是元素内容及其内边距所占据的空间大小。有关客户区大小的属性有两个：clientWidth 和clientHeight。其中，clientWidth 属性是元素内容区宽度加上左右内边距宽度；clientHeight 属性是元素内容区高度加上上下内边距高度。图12-2 形象地说明了这些属性表示的大小。

从字面上看，客户区大小就是元素内部的空间大小，因此滚动条占用的空间不计算在内。最常用到这些属性的情况，就是像第8 章讨论的确定浏览器视口大小的时候。如下面的例子所示，要确定浏览器
视口大小，可以使用document.documentElement 或document.body（在IE7 之前的版本中）的clientWidth 和clientHeight。

```javascript
function getViewport(){
    if (document.compatMode == "BackCompat"){
        return {
            width: document.body.clientWidth,
            height: document.body.clientHeight
        };
    } else {
        return {
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        };
    }
}
```

这个函数首先检查document.compatMode 属性，以确定浏览器是否运行在混杂模式。Safari 3.1之前的版本不支持这个属性，因此就会自动执行else 语句。Chrome、Opera 和Firefox 大多数情况下都运行在标准模式下，因此它们也会前进到else 语句。这个函数会返回一个对象，包含两个属性：width和height；表示浏览器视口（`<html>`或`<body>`元素）的大小。

### 滚动大小

最后要介绍的是滚动大小（scroll dimension），指的是包含滚动内容的元素的大小。有些元素（例如`<html>`元素），即使没有执行任何代码也能自动地添加滚动条；但另外一些元素，则需要通过CSS 的overflow 属性进行设置才能滚动。以下是4 个与滚动大小相关的属性。

- scrollHeight：在没有滚动条的情况下，元素内容的总高度。
- scrollWidth：在没有滚动条的情况下，元素内容的总宽度。
- scrollLeft：被隐藏在内容区域左侧的像素数。通过设置这个属性可以改变元素的滚动位置。
- scrollTop：被隐藏在内容区域上方的像素数。通过设置这个属性可以改变元素的滚动位置。

图12-3 展示了这些属性代表的大小。

scrollWidth 和scrollHeight 主要用于确定元素内容的实际大小。例如，通常认为`<html>`元素是在Web 浏览器的视口中滚动的元素（IE6 之前版本运行在混杂模式下时是`<body>`元素）。因此，带有
垂直滚动条的页面总高度就是document.documentElement.scrollHeight。

对于不包含滚动条的页面而言， scrollWidth 和scrollHeight 与clientWidth 和clientHeight 之间的关系并不十分清晰。在这种情况下，基于document.documentElement 查看这些属性会在不同浏览器间发现一些不一致性问题，如下所述。

- Firefox 中这两组属性始终都是相等的，但大小代表的是文档内容区域的实际尺寸，而非视口的尺寸。
- Opera、Safari 3.1 及更高版本、Chrome 中的这两组属性是有差别的，其中scrollWidth 和scrollHeight 等于视口大小，而clientWidth 和clientHeight 等于文档内容区域的大小。
- IE（在标准模式）中的这两组属性不相等，其中scrollWidth和scrollHeight等于文档内容区的大小，而clientWidth和clientHeight等于视口大小。

在确定文档的总高度时（包括基于视口的最小高度时），必须取得scrollWidth/clientWidth 和scrollHeight/clientHeight 中的最大值，才能保证在跨浏览器的环境下得到精确的结果。下面就是这样一个例子。

```javascript
var docHeight = Math.max(document.documentElement.scrollHeight,
document.documentElement.clientHeight);
var docWidth = Math.max(document.documentElement.scrollWidth,
document.documentElement.clientWidth);
```

注意，对于运行在混杂模式下的IE，则需要用document.body 代替document.documentElement。

通过scrollLeft 和scrollTop 属性既可以确定元素当前滚动的状态，也可以设置元素的滚动位置。在元素尚未被滚动时，这两个属性的值都等于0。如果元素被垂直滚动了，那么scrollTop 的值会大于0，且表示元素上方不可见内容的像素高度。如果元素被水平滚动了，那么scrollLeft 的值会大于0，且表示元素左侧不可见内容的像素宽度。这两个属性都是可以设置的，因此将元素的scrollLeft 和scrollTop 设置为0，就可以重置元素的滚动位置。下面这个函数会检测元素是否位
于顶部，如果不是就将其回滚到顶部。

```javascript
function scrollToTop(element) {
    if (element.scrollTop != 0) {
        element.scrollTop = 0;
    }
}
```

这个函数既取得了scrollTop的值，也设置了它的值。

### 确定元素大小

IE、Firefox 3+、Safari 4+、Opera 9.5 及Chrome 为每个元素都提供了一个getBoundingClientRect()方法。这个方法返回会一个矩形对象，包含4 个属性：left、top、right 和bottom。这些属性给出了元素在页面中相对于视口的位置。但是，浏览器的实现稍有不同。IE8 及更早版本认为文档的左上角坐标是(2, 2)，而其他浏览器包括IE9 则将传统的(0,0)作为起点坐标。因此，就需要在一开始检查一下位于(0,0)处的元素的位置，在IE8 及更早版本中，会返回(2,2)，而在其他浏览器中会返回(0,0)。来看下面的函数：

```javascript
function getBoundingClientRect(element){
    if (typeof arguments.callee.offset != "number"){
        var scrollTop = document.documentElement.scrollTop;
        var temp = document.createElement("div");
temp.style.cssText = "position:absolute;left:0;top:0;";
        document.body.appendChild(temp);
        arguments.callee.offset = -temp.getBoundingClientRect().top - scrollTop;
        document.body.removeChild(temp);
        temp = null;
    }
    var rect = element.getBoundingClientRect();
    var offset = arguments.callee.offset;
    return {
        left: rect.left + offset,
        right: rect.right + offset,
        top: rect.top + offset,
        bottom: rect.bottom + offset
    };
}
```

这个函数使用了它自身的属性来确定是否要对坐标进行调整。第一步是检测属性是否有定义，如果没有就定义一个。最终的offset 会被设置为新元素上坐标的负值，实际上就是在IE 中设置为2，在Firefox 和Opera 中设置为0。为此，需要创建一个临时的元素，将其位置设置在(0,0)，然后再调用其getBoundingClientRect()。而之所以要减去视口的scrollTop，是为了防止调用这个函数时窗口被滚动了。这样编写代码，就无需每次调用这个函数都执行两次getBoundingClientRect()了。接下来，再在传入的元素上调用这个方法并基于新的计算公式创建一个对象。

对于不支持getBoundingClientRect()的浏览器，可以通过其他手段取得相同的信息。一般来说，right 和left 的差值与offsetWidth 的值相等，而bottom 和top 的差值与offsetHeight相等。而且，left 和top 属性大致等于使用本章前面定义的getElementLeft()和getElementTop()函数取得的值。综合上述，就可以创建出下面这个跨浏览器的函数：

```javascript
function getBoundingClientRect(element){
    var scrollTop = document.documentElement.scrollTop;
    var scrollLeft = document.documentElement.scrollLeft;
    if (element.getBoundingClientRect){
        if (typeof arguments.callee.offset != "number"){
            var temp = document.createElement("div");
            temp.style.cssText = "position:absolute;left:0;top:0;";
            document.body.appendChild(temp);
            arguments.callee.offset = -temp.getBoundingClientRect().top - scrollTop;
            document.body.removeChild(temp);
            temp = null;
        }
        var rect = element.getBoundingClientRect();
        var offset = arguments.callee.offset;
        return {
            left: rect.left + offset,
            right: rect.right + offset,
            top: rect.top + offset,
            bottom: rect.bottom + offset
        };
    } else {
        var actualLeft = getElementLeft(element);
        var actualTop = getElementTop(element);
        return {
            left: actualLeft - scrollLeft,
            right: actualLeft + element.offsetWidth - scrollLeft,
            top: actualTop - scrollTop,
            bottom: actualTop + element.offsetHeight - scrollTop
        }
    }
}
```

这个函数在getBoundingClientRect()有效时，就使用这个原生方法，而在这个方法无效时则使用默认的计算公式。在某些情况下，这个函数返回的值可能会有所不同，例如使用表格布局或使用滚动元素的情况下。

# 遍历

"DOM2级遍历和范围"模块定义了两个用于辅助完成顺序遍历DOM结构的类型：NodeIterator和TreeWalker。这两个类型能够基于给定的起点对DOM结构执行深度优先（depth-first）的遍历操作。在与DOM兼容的浏览器中（Firefox 1 及更高版本、Safari 1.3 及更高版本、Opera 7.6 及更高版本、Chrome0.2 及更高版本），都可以访问到这些类型的对象。IE 不支持DOM 遍历。使用下列代码可以检测浏览器对DOM2 级遍历能力的支持情况。

```javascript
var supportsTraversals = document.implementation.hasFeature("Traversal", "2.0");
var supportsNodeIterator = (typeof document.createNodeIterator == "function");
var supportsTreeWalker = (typeof document.createTreeWalker == "function");
```

如前所述，DOM遍历是深度优先的DOM结构遍历，也就是说，移动的方向至少有两个（取决于使用的遍历类型）。遍历以给定节点为根，不可能向上超出DOM树的根节点。以下面的HTML页面为例。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Example</title>
</head>
<body>
    <p><b>Hello</b> world!</p>
</body>
</html>
```

任何节点都可以作为遍历的根节点。如果假设`<body>`元素为根节点，那么遍历的第一步就是访问`<p>`元素，然后再访问同为`<body>`元素后代的两个文本节点。不过，这次遍历永远不会到达`<html>`、`<head>`元素，也不会到达不属于`<body>`元素子树的任何节点。而以document为根节点的遍历则可以访问到文档中的全部节点。下图展示遍历的顺序

从document开始开始依次向前，访问的第一个节点是document，访问的最后一个节点是包含"world!"的文本节点。从文档最后的文本节点开始，遍历可以反向移动到DOM树的顶端。此时，访问的第一个节点是包含"Hello"的文本节点，访问的最后一个节点是document节点。NodeIterator和TreeWalker都以这种方式执行遍历。

## NodeIterator

NodeIterator类型是两者中比较简单的一个，可以使用document.createNodeIterator()方法创建它的新实例。这个方法接受下列4个参数。

- root：想要作为搜索起点的树中的节点。
- whatToShow：表示要访问哪些节点的数字代码。
- filter：是一个NodeFilter 对象，或者一个表示应该接受还是拒绝某种特定节点的函数。
- entityReferenceExpansion：布尔值，表示是否要扩展实体引用。这个参数在HTML 页面中没有用，因为其中的实体引用不能扩展。

whatToShow参数是一个位掩码，通过应用一个或多个过滤器（filter）来确定要访问哪些节点。这个参数的值以常量形式在NodeFilter类型中定义。如下所示。

- NodeFilter.SHOW_ALL：显示所有类型的节点。
- NodeFilter.SHOW_ELEMENT：显示元素节点。
- NodeFilter.SHOW_ATTRIBUTE：显示特性节点。由于DOM结构原因，实际上不能使用这个值。
- NodeFilter.SHOW_TEXT：显示文本节点。
- NodeFilter.SHOW_CDATA_SECTION：显示CDATA 节点。对HTML 页面没有用。
- NodeFilter.SHOW_ENTITY_REFERENCE：显示实体引用节点。对HTML 页面没有用。
- NodeFilter.SHOW_ENTITYE：显示实体节点。对HTML 页面没有用。
- NodeFilter.SHOW_PROCESSING_INSTRUCTION：显示处理指令节点。对HTML 页面没有用。
- NodeFilter.SHOW_COMMENT：显示注释节点。
- NodeFilter.SHOW_DOCUMENT：显示文档节点。
- NodeFilter.SHOW_DOCUMENT_TYPE：显示文档类型节点。
- NodeFilter.SHOW_DOCUMENT_FRAGMENT：显示文档片段节点。对HTML 页面没有用。
- NodeFilter.SHOW_NOTATION：显示符号节点。对HTML 页面没有用。

除了NodeFilter.SOW_ALL之外，可以使用按位或操作符来组合多个选项，如下面的例子所示：

```javascript
var whatToShow = NodeFilter.SHOW_ELEMENT | NodeFilter.SHOW_TEXT;
```

可以通过createNodeIterator()方法的filter 参数来指定自定义的NodeFilter 对象，或者指定一个功能类似节点过滤器（node filter）的函数。每个NodeFilter 对象只有一个方法，即accept-Node()；如果应该访问给定的节点，该方法返回NodeFilter.FILTER_ACCEPT，如果不应该访问给定的节点，该方法返回NodeFilter.FILTER_SKIP。由于NodeFilter 是一个抽象的类型，因此不能直接创建它的实例。在必要时，只要创建一个包含acceptNode()方法的对象，然后将这个对象传入createNodeIterator()中即可。例如，下列代码展示了如何创建一个只显示`<p>`元素的节点迭代器。

```javascript
var filter = {
    acceptNode: function(node){
        return node.tagName.toLowerCase() == "p" ?
            NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
    }
};
var iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
```

第三个参数也可以是一个与acceptNode()方法类似的函数，如下所示。

```javascript
var filter = function(node){
    return node.tagName.toLowerCase() == "p" ?
        NodeFilter.FILTER_ACCEPT :
        NodeFilter.FILTER_SKIP;
};
var iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
```

一般来说，这就是在JavaScript 中使用这个方法的形式，这种形式比较简单，而且也跟其他的JavaScript 代码很相似。如果不指定过滤器，那么应该在第三个参数的位置上传入null。

下面的代码创建了一个能够访问所有类型节点的简单的NodeIterator。

```javascript
var iterator = document.createNodeIterator(document, NodeFilter.SHOW_ALL, null, false);
```

NodeIterator 类型的两个主要方法是nextNode()和previousNode()。顾名思义，在深度优先的DOM 子树遍历中，nextNode()方法用于向前前进一步，而previousNode()用于向后后退一步。在刚刚创建的NodeIterator 对象中，有一个内部指针指向根节点，因此第一次调用nextNode()会返回根节点。当遍历到DOM 子树的最后一个节点时，nextNode()返回null。previousNode()方法的工作机制类似。当遍历到DOM 子树的最后一个节点，且previousNode()返回根节点之后，再次调用它就会返回null。

以下面的HTML片段为例。

```html
<div id="div1">
    <p><b>Hello</b> world!</p>
    <ul>
        <li>List item 1</li>
        <li>List item 2</li>
        <li>List item 3</li>
    </ul>
</div>
```

假设我们想要遍历`<div>`元素中的所有元素，那么可以使用下列代码。

```javascript
var div = document.getElementById("div1");
var iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT,null, false);
var node = iterator.nextNode();
while (node !== null) {
    alert(node.tagName); //输出标签名
    node = iterator.nextNode();
}
```

在这个例子中，第一次调用nextNode()返回`<p>`元素。因为在到达DOM子树末端时nextNode()返回null，所以这里使用了while 语句在每次循环时检查对nextNode()的调用是否返回了null。执行上面的代码会显示如下标签名：

```html
DIV
P
B
UL
LI
LI
LI
```

也许用不着显示那么多信息，你只想返回遍历中遇到的`<li>`元素。很简单，只要使用一个过滤器即可，如下面的例子所示。

```javascript
var div = document.getElementById("div1");
var filter = function(node){
    return node.tagName.toLowerCase() == "li" ?
NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
};
var iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT,filter, false);
var node = iterator.nextNode();
while (node !== null) {
    alert(node.tagName); //输出标签名
    node = iterator.nextNode();
}
```

在这个例子中，迭代器只会返回`<li>`元素。

由于nextNode()和previousNode()方法都基于NodeIterator在DOM结构中的内部指针工作，所以DOM结构的变化会反映在遍历的结果中。

## TreeWalker

TreeWalker 是NodeIterator 的一个更高级的版本。除了包括nextNode()和previousNode()在内的相同的功能之外，这个类型还提供了下列用于在不同方向上遍历DOM 结构的方法。

- parentNode()：遍历到当前节点的父节点；
- firstChild()：遍历到当前节点的第一个子节点；
- lastChild()：遍历到当前节点的最后一个子节点；
- nextSibling()：遍历到当前节点的下一个同辈节点；
- previousSibling()：遍历到当前节点的上一个同辈节点。

创建TreeWalker 对象要使用document.createTreeWalker()方法，这个方法接受的4 个参数与document.createNodeIterator()方法相同：作为遍历起点的根节点、要显示的节点类型、过滤器和一个表示是否扩展实体引用的布尔值。由于这两个创建方法很相似，所以很容易用TreeWalker来代替NodeIterator，如下面的例子所示。

```javascript
var div = document.getElementById("div1");
var filter = function(node){
    return node.tagName.toLowerCase() == "li"?
NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
};
var walker= document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT,filter, false);
var node = iterator.nextNode();
while (node !== null) {
    alert(node.tagName); //输出标签名
    node = iterator.nextNode();
}
```

在这里，filter 可以返回的值有所不同。除了NodeFilter.FILTER_ACCEPT 和NodeFilter.FILTER_SKIP 之外，还可以使用NodeFilter.FILTER_REJECT。在使用NodeIterator 对象时，NodeFilter.FILTER_SKIP 与NodeFilter.FILTER_REJECT 的作用相同：跳过指定的节点。但在使用TreeWalker 对象时，NodeFilter.FILTER_SKIP 会跳过相应节点继续前进到子树中的下一个节点，而NodeFilter.FILTER_REJECT 则会跳过相应节点及该节点的整个子树。例如，将前面例子中的NodeFilter.FILTER_SKIP 修改成NodeFilter.FILTER_REJECT，结果就是不会访问任何节点。这是
因为第一个返回的节点是`<div>`，它的标签名不是"li"，于是就会返回NodeFilter.FILTER_REJECT，这意味着遍历会跳过整个子树。在这个例子中，`<div>`元素是遍历的根节点，于是结果就会停止遍历。

当然，TreeWalker 真正强大的地方在于能够在DOM结构中沿任何方向移动。使用TreeWalker遍历DOM 树，即使不定义过滤器，也可以取得所有`<li>`元素，如下面的代码所示。

```javascript
var div = document.getElementById("div1");
var walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, null, false);
walker.firstChild(); //转到<p>
walker.nextSibling(); //转到<ul>
var node = walker.firstChild(); //转到第一个<li>
while (node !== null) {
    alert(node.tagName);
    node = walker.nextSibling();
}
```

因为我们知道`<li>`元素在文档结构中的位置，所以可以直接定位到那里，即使用firstChild()转到`<p>`元素，使用nextSibling()转到`<ul>`元素，然后再使用firstChild()转到第一个`<li>`元素。注意，此处TreeWalker 只返回元素（由传入到createTreeWalker()的第二个参数决定）。因此，可以放心地使用nextSibling()访问每一个`<li>`元素，直至这个方法最后返回null。

TreeWalker 类型还有一个属性，名叫currentNode，表示任何遍历方法在上一次遍历中返回的节点。通过设置这个属性也可以修改遍历继续进行的起点，如下面的例子所示。

```javascript
var node = walker.nextNode();
alert(node === walker.currentNode); //true
walker.currentNode = document.body; //修改起点
```

与NodeIterator 相比，TreeWalker 类型在遍历DOM时拥有更大的灵活性。由于IE 中没有对应的类型和方法，所以使用遍历的跨浏览器解决方案非常少见。

# 范围

为了让开发人员更方便地控制页面，“DOM2 级遍历和范围”模块定义了“范围”（range）接口。通过范围可以选择文档中的一个区域，而不必考虑节点的界限（选择在后台完成，对用户是不可见的）。在常规的DOM 操作不能更有效地修改文档时，使用范围往往可以达到目的。Firefox、Opera、Safari 和Chrome 都支持DOM 范围。IE 以专有方式实现了自己的范围特性。

## DOM中的范围

DOM2 级在Document 类型中定义了createRange()方法。在兼容DOM 的浏览器中，这个方法属于document 对象。使用hasFeature()或者直接检测该方法，都可以确定浏览器是否支持范围。

```javascript
var supportsRange = document.implementation.hasFeature("Range", "2.0");
var alsoSupportsRange = (typeof document.createRange == "function");
```

如果浏览器支持范围，那么就可以使用createRange()来创建DOM范围，如下所示：

```javascript
var range = document.createRange();
```

与节点类似，新创建的范围也直接与创建它的文档关联在一起，不能用于其他文档。创建了范围之后，接下来就可以使用它在后台选择文档中的特定部分。而创建范围并设置了其位置之后，还可以针对范围的内容执行很多种操作，从而实现对底层DOM 树的更精细的控制。

每个范围由一个Range 类型的实例表示，这个实例拥有很多属性和方法。下列属性提供了当前范围在文档中的位置信息。

- startContainer：包含范围起点的节点（即选区中第一个节点的父节点）。
- startOffset：范围在startContainer 中起点的偏移量。如果startContainer 是文本节点、注释节点或CDATA 节点，那么startOffset 就是范围起点之前跳过的字符数量。否则，startOffset 就是范围中第一个子节点的索引。
- endContainer：包含范围终点的节点（即选区中最后一个节点的父节点）。
- endOffset：范围在endContainer 中终点的偏移量（与startOffset 遵循相同的取值规则）。
- commonAncestorContainer：startContainer 和endContainer 共同的祖先节点在文档树中位置最深的那个。

在把范围放到文档中特定的位置时，这些属性都会被赋值。

### 用DOM范围实现简单选择

要使用范围来选择文档中的一部分，最简的方式就是使用selectNode()或selectNodeContents()。这两个方法都接受一个参数，即一个DOM 节点，然后使用该节点中的信息来填充范围。其中，selectNode()方法选择整个节点，包括其子节点；而selectNodeContents()方法则只选择节点的子节点。以下面的HTML 代码为例。

```html
<!DOCTYPE html>
<html>
<body>
<p id="p1"><b>Hello</b> world!</p>
</body>
</html>
```

我们可以使用下列代码来创建范围：

```javascript
var range1 = document.createRange();
range2 = document.createRange();
p1 = document.getElementById("p1");
range1.selectNode(p1);
range2.selectNodeContents(p1);
```

这里创建的两个范围包含文档中不同的部分：rang1 包含<p/>元素及其所有子元素，而rang2 包
含<b/>元素、文本节点"Hello"和文本节点"world!"（如图12-6 所示）。

在调用selectNode()时，startContainer、endContainer 和commonAncestorContainer都等于传入节点的父节点，也就是这个例子中的document.body。而startOffset 属性等于给定节点在其父节点的childNodes 集合中的索引（在这个例子中是1——因为兼容DOM的浏览器将空格算作一个文本节点），endOffset 等于startOffset 加1（因为只选择了一个节点）。

在调用selectNodeContents()时，startContainer、endContainer 和commonAncestorContainer等于传入的节点，即这个例子中的`<p>`元素。而startOffset 属性始终等于0，因为范围从给定节点的第一个子节点开始。最后，endOffset 等于子节点的数量（node.childNodes.length），在这个例子中是2。

此外，为了更精细地控制将哪些节点包含在范围中，还可以使用下列方法。

- setStartBefore(refNode)：将范围的起点设置在refNode 之前，因此refNode 也就是范围选区中的第一个子节点。同时会将startContainer 属性设置为refNode.parentNode，将startOffset 属性设置为refNode 在其父节点的childNodes 集合中的索引。
- setStartAfter(refNode)：将范围的起点设置在refNode 之后，因此refNode 也就不在范围之内了，其下一个同辈节点才是范围选区中的第一个子节点。同时会将startContainer 属性设置refNode.parentNode，将startOffset 属性设置为refNode 在其父节点的childNodes 集合中的索引加1。
- setEndBefore(refNode)：将范围的终点设置在refNode 之前，因此refNode 也就不在范围之内了，其上一个同辈节点才是范围选区中的最后一个子节点。同时会将endContainer 属性设置为refNode.parentNode，将endOffset 属性设置为refNode 在其父节点的childNodes集合中的索引。
- setEndAfter(refNode)：将范围的终点设置在refNode 之后，因此refNode 也就是范围选区中的最后一个子节点。同时会将endContainer 属性设置为refNode.parentNode，将endOffset 属性设置为refNode 在其父节点的childNodes 集合中的索引加1。

在调用这些方法时，所有属性都会自动为你设置好。不过，要想创建复杂的范围选区，也可以直接指定这些属性的值。

### 用DOM范围实现复杂选择

要创建复杂的范围就得使用setStart()和setEnd()方法。这两个方法都接受两个参数：一个参照节点和一个偏移量值。对setStart()来说，参照节点会变成startContainer，而偏移量值会变成startOffset。对于setEnd()来说，参照节点会变成endContainer，而偏移量值会变成endOffset。

可以使用这两个方法来模仿selectNode()和selectNodeContents()。来看下面的例子：

```javascript
var range1 = document.createRange();
    range2 = document.createRange();
    p1 = document.getElementById("p1");
    p1Index = -1;
    i, len;
for (i=0, len=p1.parentNode.childNodes.length; i < len; i++) {
    if (p1.parentNode.childNodes[i] == p1) {
        p1Index = i;
        break;
    }
}
range1.setStart(p1.parentNode, p1Index);
range1.setEnd(p1.parentNode, p1Index + 1);
range2.setStart(p1, 0);
range2.setEnd(p1, p1.childNodes.length);
```

显然，要选择这个节点（使用range1），就必须确定当前节点（p1）在其父节点的childNodes集合中的索引。而要选择这个节点的内容（使用range2），也不比计算什么；只要通过setStart()和setEnd()设置默认值即可。模仿selectNode()和selectNodeContents()并不是setStart()和setEnd()的主要用途，它们更胜一筹的地方在于能够选择节点的一部分。

假设你只想选择前面HTML示例代码中从"Hello"的"llo"到"world!"的"o"——很容易做到。第一步是取得所有节点的引用，如下面的例子所示：

```javascript
var p1 = document.getElementById("p1");
    helloNode = p1.firstChild.firstChild;
    worldNode = p1.lastChild;
```

实际上，"Hello"文本节点是`<p>`元素的孙子节点，因为它本身是`<b>`元素的一个子节点。因此，p1.firstChild 取得的是`<b>`，而p1.firstChild.firstChild 取得的才是这个文本节点。"world!"文本节点是`<p>`元素的第二个子节点（也是最后一个子节点），因此可以使用p1.lastChild 取得该节点。然后，必须在创建范围时指定相应的起点和终点，如下面的例子所示。

```javascript
var range = document.createRange();
range.setStart(helloNode, 2);
range.setEnd(worldNode, 3);
```

因为这个范围的选区应该从"Hello"中"e"的后面开始，所以在setStart()中传入helloNode的同时，传入了偏移量2（即"e"的下一个位置；"H"的位置是0）。设置选区的终点时，在setEnd()中传入worldNode的同时传入了偏移量3，表示选区之外的第一个字符的位置，这个字符是"r"，它的位置是3（位置0上还有一个空格）。如图12-7所示。

由于helloNode和worldNode都是文本节点，因此它们分别变成了新键范围的startContainer和endContainer。此时startOffset和endOffset分别用以确定两个节点所包含的文本中的位置，而不是用以确定子节点的位置（就像传入的参数为元素节点时那样）。此时的commonAncestorContainer是`<p>`元素，也就是同时包含这两个节点的第一个祖先元素。

当然，仅仅是选择了文档中的某一部分用处并不大。但重要的是，选择之后才可以对选区进行操作。

### 操作DOM范围中的内容

在创建范围时 ，内部会为这个范围创建一个文档片段，范围所属的全部节点都被添加到了这个文档片段中。为了创建这个文档片段，范围内容的格式必须正确有效。在前面的例子中，我们创建的选区分别开始和结束于两个文本节点的内部，因此不能算是格式良好的DOM结构，也就无法通过DOM来表示。但是，范围知道自身缺少哪些开标签和闭标签，它能够重新构建有效的DOM结构以便我们对其进行操作。

对于前面的例子而言，范围经过计算知道选区中缺少一个开始的`<b>`标签，因此就会在后台动态加入一个该标签，同时还会在前面加入一个表示结束的`</b>`标签以结束"He"。于是，修改后的DOM就变成了如下所示。

```html
<p><b>He</b><b>llo</b> world!</p>
```

另外，文本节点"world!"也被拆分为两个文本节点，一个包含"wo"，另一个包含"rld!"。最终的DOM树如图所示，右侧是表示范围的文档片段的内容。

像这样创建了范围之后，就可以使用各种方法对范围的内容进行操作了（注意，表示范围的内部文档片段中的所有节点，都只是指向文档中相应节点的指针）。

第一个方法，也是最容易理解的方法，就是deleteContents()。这个方法能够从文档中删除范围所包含的内容。例如：

```javascript
var p1 = document.getElementById("p1");
    helloNode = p1.firstChild.firstChild;
    worldNode = p1.lastChild;
    range = document.createRange();
range.setStart(helloNode, 2);
range.setEnd(worldNode, 3);
range.deleteContents();
```

执行以上代码后，页面中会显示如下HTML代码：

```html
<p><b>He</b>rld!</p>
```

由于范围选区在修改底层DOM结构时能够保证格式良好，因此即使内容被删除了，最终的DOM结构依旧是格式良好的。

与deleteContents()方法相似，extractContents()也会从文档中移除范围选区。但这两个方法的区别在于，extractContents()会返回范围的文档片段。利用这个返回的值，可以将范围的内容插入到文档中的其它地方。如下面的例子所示：

```javascript
var p1 = document.getElementById("p1");
    helloNode = p1.firstChild.firstChild;
    worldNode = p1.lastChild;
    range = document.createRange();

range.setStart(helloNode, 2);
range.setEnd(worldNode, 3);

var fragment = range.extractContents();
p1.parentNode.appendChild(fragment);
```

在这个例子中，我们将提取出来的文档片段添加到了文档`<body>`元素的末尾。（记住，在将文档片段传入appendChild()方法中时，添加到文档中的只是片段的子节点，而非片段本身。）结果得到如下HTML代码：

```html
<p><b>He</b>rld!<p>
<b>llo</b> wo
```

还一种做法，即使用cloneContents()创建范围对象的一个副本，然后在文档的其它地方插入该副本。如下面的例子所示：

```javascript
var p1 = document.getElementById("p1"),
    helloNode = p1.firstChild.firstChild,
    worldNode = p1.lastChild,
    range = document.createRange();
range.setStart(helloNode, 2);
range.setEnd(worldNode, 3);
var fragment = range.cloneContents();
p1.parentNode.appendChild(fragment);
```

这个方法与extractContents()非常类似，因为它们都返回文档片段。它们的主要区别在于，cloneContents()返回的文档片段包含的是范围中节点的副本，而不是实际的节点。执行上面的操作后，页面中的HTML代码应该如下所示：

```html
<p><b>Hello</b> world!</p>
<b>llo</b> wo
```

有一点请读者注意，那就是在调用上面介绍的方法之前，拆分的节点并不会产生格式良好的文档片段。换句话说，原始的HTML 在DOM 被修改之前会始终保持不变。

### 插入DOM范围中的内容

利用范围，可以删除或复制内容，还可以像前面介绍的那样操作范围中的内容。使用insertNode()方法可以向范围选区的开始处插入一个节点。假设我们想在前面例子中的HTML 前面插入以下HTML代码：

```html
<span style="color: red">Inserted text</span>
```

那么，就可以使用下列代码：

```javascript
var p1 = document.getElementById("p1");
    helloNode = p1.firstChild.firstChild;
    worldNode = p1.lastChild;
    range = document.createRange();

range.setStart(helloNode, 2);
range.setEnd(worldNode, 3);
var span = document.createElement("span");
span.style.color = "red";
span.appendChild(document.createTextNode("Inserted text"));
range.insertNode(span);
```

运行以上JavaScript代码，就会得到如下HTML代码：

```html
<p id="p1"><b>He<span style="color: red">Inserted text</span>llo</b> world</p>
```

注意，`<span>`正好被插入到了"Hello"中的"llo"前面，而该位置就是范围选区的开始位置。还要注意的是，由于这里没有使用上一节介绍的方法，结果原始的HTML 并没有添加或删除`<b>`元素。使用这种技术可以插入一些帮助提示信息，例如在打开新窗口的链接旁边插入一幅图像。

除了向范围内部插入内容之外，还可以环绕范围插入内容，此时就要使用surroundContents()方法。这个方法接受一个参数，即环绕范围内容的节点。在环绕范围插入内容时，后台会执行下列步骤。

(1) 提取出范围中的内容（类似执行extractContent()）；
(2) 将给定节点插入到文档中原来范围所在的位置上；
(3) 将文档片段的内容添加到给定节点中。

可以使用这种技术来突出显示网页中的某些词句，例如下列代码：

```javascript
var p1 = document.getElementById("p1");
    helloNode = p1.firstChild.firstChild;
    worldNode = p1.lastChild;
    range = document.createRange();

range.selectNode(helloNode);
var span = document.createElement("span");
span.style.backgroundColor = "yellow";
range.surroundContents(span);
```

会给范围选区加上一个黄色的背景。得到的HTML代码如下所示：

```html
<p><b><span style="background-color:yellow">Hello</span></b> world!</p>
```

为了插入`<span>`，范围必须包含整个DOM选区（不能仅仅包含选中的DOM节点）。

### 折叠DOM范围

所谓折叠范围，就是指范围中未选择文档的任何部分。可以用文本框来描述折叠范围的过程。假设文本框中有一行文本，你用鼠标选择了其中一个完整的单词。然后，你单击鼠标左键，选区消失，而光标则落在了其中两个字母之间。同样，在折叠范围时，其位置会落在文档中的两个部分之间，可能是范围选区的开始位置，也可能是结束位置。图12-9 展示了折叠范围时发生的情形。

使用collapse()方法来折叠范围，这个方法接受一个参数，一个布尔值，表示要折叠到范围的哪一端。参数true表示折叠到范围的起点，参数false表示折叠到范围的终点。要确定范围已经折叠完毕，可以检查collapsed属性，如下所示：

```javascript
range.collapse(true);    // 折叠到起点
alert(range.collapsed);    // 输出true
```

检测某个范围是否处于折叠状态，可以帮我们确定范围中的两个节点是否紧密相邻。例如，对于下面的HTML代码：

```html
<p id="p1">Paragraph 1</p><p id="p2">Paragrapph 2</p>
```

如果我们不知道其实际构成（比如说，这行代码是动态生成的），那么可以像下面这样创建一个范围。

```javascript
var p1 = document.getElementById("p1"),
    p2 = document.getElementById("p2"),
    range = document.createRange();
range.setStartAfter(p1);
range.setStartBefore(p2);
alert(range.collapsed); //输出true
```

在这个例子中，新创建的范围是折叠的，因为p1的后面和p2的前面什么也没有。

### 比较DOM范围

在有多个范围的情况下，可以使用compareBoundaryPoints()方法来确定这些范围是否有公共的边界（起点或终点）。这个方法接受两个参数：表示比较方式的常量值和要比较的范围。表示比较方式的常量值如下所示。

- Range.START_TO_START(0)：比较第一个范围和第二个范围的起点；
- Range.START_TO_END(2)：比较第一个范围的起点和第二个范围的终点。
- Range.END_TO_END(2)：比较第一个范围和第二个范围的终点；
- Range.END_TO_START(3)：比较第一个范围的终点和第一个范围的起点。

compareBoundaryPoints()方法可能的返回值如下：如果第一个范围中的点位于第二个范围中的点之前，返回-1；如果两个点相等，返回0；如果第一个范围中的点位于第二个范围中的点之后，返回1。来看下面的例子。

```javascript
var range1 = document.createRange();
var range2 = document.createRange();
var p1 = document.getElementById("p1");
range1.selectNodeContents(p1);
range2.selectNodeContents(p1);
range2.setEndBefore(p1.lastChild);
alert(range1.compareBoundaryPoints(Range.START_TO_START, range2)); //0
alert(range1.compareBoundaryPoints(Range.END_TO_END, range2)); //1
```

在这个例子中，两个范围的起点实际上是相同的，因为它们的起点都是由selectNodeContents()方法设置的默认值来指定的。因此，第一次比较返回0。但是，range2的终点由于调用setEndBefore()已经改变了，结果是range1的终点位于range2的终点后面，因此第二次比较返回1。

### 复制DOM范围

可以使用cloneRange()方法复制范围。这个方法会创建调用它的范围的第一个副本。

```javascript
var newRange = range.cloneRange();
```

新创建的范围与原来的范围包含相同的属性，而修改它的端点不会影响原来的范围。

### 清理DOM范围

在使用完范围之后，最好是调用detach()方法，以便从创建范围的文档中分离出该范围。调用detach()之后，就可以放心地解除对范围的引用，从而让垃圾回收其内存了。来看下面的例子。

```javascript
range.detach();    // 从文档中分离
range = null;    // 解除引用
```

在使用范围的最后再执行这两个步骤是我们推荐的方式。一旦分离范围，就不能再恢复使用了。

## IE8及更早版本中的范围

虽然IE9支持DOM范围，但IE8及之前版本不支持DOM范围。不过，IE8及早期版本支持一种类似的概念，即文本范围（text range）。文本范围是IE专有的特性，其它浏览器都不支持。顾名思义，文本范围处理的主要是文本（不一定是DOM节点）。通过`<body>`、`<button>`、`<input>`和`<textarea>`等这几个元素，可以调用createTextRange()方法来创建文本范围。以下是一个例子：

```javascript
var range = document.body.createTextRange();
```

像这样通过document创建的范围可以在页面中的任何地方使用（通过其它元素创建的范围则只能在相应的元素中使用）。与DOM范围类似，使用IE文本范围的方式也有很多种。

### 用IE范围实现简单的选择

选择页面中某一区域的最简单方式，就是使用范围的findText()方法。这个方法会找到第一次出现的给定文本，并将范围移过来以环绕该文本。如果没有找到文本，这个方法返回false；否则返回true。同样，仍然以下面的HTML代码为例。

```html
<p id="p1"><b>Hello</b> world!</p>
```

要选择"Hello"，可以使用下列代码。

```javascript
var range = document.body.createTextRange();
var found = range.findText("Hello");
```

在执行完毕第二行代码之后，文本"Hello"就被包围在范围之内了。为此，可以检查范围的text属性来确认（这个属性返回范围中包含的文本），或者也可以检查findText()的返回值——在找到了文本的情况下返回值为true。例如：

```javascript
alert(found);    // true
alert(range.text);    // "Hello"
```

还可以为findText()传入另一个参数，即一个表示向哪个方向继续搜索的数值。负值表示应该从当前位置向后搜索，而正值表示应该从当前位置向前搜索。因此，要查找文档中前两个"Hello"的实例，应该使用下列代码。

```javascript
var found = range.findText("Hello");
var foundAgain = range.findText("Hello", 1);
```

IE中与DOM中的selectNode()方法最接近的方法是moveToElementText()，这个犯法接受一个DOM元素，并选择该元素的所有文本，包括HTML标签。下面是一个例子。

```javascript
var range = document.body.createTextRange();
var p1 = document.getElementById("p1");
range.moveToElementText(p1);
```

在文本范围中包含HTML的情况下，可以使用htmlText属性取得范围的全部内容，包括HTML和文本，如下面的例子所示。

```javascript
alert(range.htmlText);
```

IE的范围没有任何属性可以随着范围选区的变化而动态更新。不过，其parentElement()方法倒是与DOM的commonAncestorContainer属性类似。

```javascript
var ancestor = range.parentElement();
```

这样得到的父元素始终都可以反映文本选区的父节点。

### 使用IE范围实现复杂的选择

在IE 中创建复杂范围的方法，就是以特定的增量向四周移动范围。为此，IE 提供了4 个方法：move()、moveStart()、moveEnd()和expand()。这些方法都接受两个参数：移动单位和移动单位的数量。其中，移动单位是下列一种字符串值。

- "character"：逐个字符地移动。
- "word"：逐个单词（一系列非空格字符）地移动。
- "sentence"：逐个句子（一系列以句号、问号或叹号结尾的字符）地移动。
- "textedit"：移动到当前范围选区的开始或结束位置。

通过moveStart()方法可以移动范围的起点。通过moveEnd()方法可以移动范围的终点，移动到幅度由单位数量指定，如下面的例子所示。

```javascript
range.moveStart("word", 2);    //起点移动2个单词
range.moveEnd("character", 1); // 终点移动1个字符
```

使用expand()方法可以将范围规范化。话句话说，expand()方法的作用是将任何部分选择的文本全部选中。例如，当前选择的是一个单词中间的两个字符，调用expand("word")可以将整个单词都包含在范围之内。

而move()方法则首先会折叠当前范围（让起点和终点相等），然后再将范围移动指定的单位数量，如下面的例子所示。

```javascript
range.move("character", 5);    // 移动4个字符
```

调用move()之后，范围的起点和终点相同，因此必须再使用moveStart()或moveEnd()创建新的选区。

### 操作IE范围中的内容

在IE中操作范围中的内容可以使用text属性或pasteHTML()方法。如前所述，通过text属性可以取得范围中的内容文本；但是，也可以通过这个属性设置范围中的内容文本。来看一个例子。

```javascript
var range = document.body.createTextRange();
range.findText("Hello");
range.text = "Howdy";
```

如果仍以前面的Hello World代码为例，执行以上代码后的HTML代码如下。

```html
<p id="p1"><b>Howdy</b> world!</p>
```

注意，在设置text属性的情况下，HTML标签保持不变。

要向范围中插入HTML代码，就得使用pasteHTMML()方法，如下面的例子所示。

```javascript
var range = document.body.createTextRange();
range.findText("Hello");
range.pasteHTML("<em>Howdy</em>");
```

执行这些代码后，会得到如下HTML。

```html
<p id="p1"><b><em>Howdy</em></b> world!</p>
```

不过，在范围中包含HTML代码时，不应该使用pasteHTML()，因为这样很容易导致不可预料的结果——很可能是格式不正确的HTML。

### 折叠IE范围

IE为范围提供的collapse()方法与相应的DOM方法用法一样：传入true把范围折叠到起点，传入false把范围折叠到终点。例如：

```javascript
range.collapse(true);    // 折叠到起点
```

可惜的是，没有对应的collapsed属性让我们知道范围是否已经折叠完毕。为此，必须使用boundingWidth属性，该属性返回范围的宽度（以像素为单位）。如果boundingWidth属性等于0，就索命范围已经折叠了：

```javascript
var isCollapsed = (range.boundingWidth == 0);
```

此外，还有boundingHeight、boundingLeft和boundingTop等属性，虽然它们不像boundingWidth那么有用，但也可以提供一些有关范围位置的信息。

### 比较IE范围

IE中的compareEndPoints()方法与DOM范围的compareBoundaryPoints()方法类似。这个方法接受两个参数：比较的类型和要比较的范围。比较类型的取值范围是下列几个字符串值："StartToStart"、"StartToEnd"、"EndToEnd"和"EndToStart"。这几种比较类型与比较DOM范围时使用的几个值是相同的。

同样与DOM类似的是，compareEndPoints()方法也会按照相同的规则返回值，即如果第一个范围的边界位于第二个范围的边界前面，返回-1；如果两者边界先沟通，返回0；如果第一个范围的边界位于第二个范围的边界后面，返回1。仍以前面的Hello World代码为例，下列代码将创建两个范围，一个选择"Hello world!"（包括`<b>`标签），另一个选择"Hello"。

```javascript
var range1 = document.body.createTextRange();
var range2 = document.body.createTextRange();
range1.findText("Hello world!");
range2.findText("Hello");
alert(range1.compareEndPoints("StartToStart", range2)); //0
alert(range1.compareEndPoints("EndToEnd", range2)); //1
```

由于这两个范围共享同一个起点，所以使用compareEndPoints()比较起点返回0。而range1的终点在range2的终点后面，所以compareEndPoints()返回1。

IE中还有两个方法，也是用于比较范围的：isEqual()用于确定两个范围是否相等，inRange()用于确定一个范围是否包含另一个范围。下面是相应的示例。

```javascript
var range1 = document.body.createTextRange();
var range2 = document.body.createTextRange();
range1.findText("Hello World");
range2.findText("Hello");
alert("range1.isEqual(range2): " + range1.isEqual(range2)); //false
alert("range1.inRange(range2):" + range1.inRange(range2)); //true
```

这个例子使用了与前面相同的范围来示范这两个方法。由于这两个范围的终点不同，所以它们不相等，调用isEqual()返回false。由于range2实际位于range1内部，它的终点位于后者的终点之前、起点之后，所以range2被包含在range1内部，调用inRange()返回true。

### 复制IE范围

在IE中使用duplicate()方法可以复制文本范围，结果会创建原范围的一个副本，如下面的例子所示。

```javascript
var newRange = range.duplicate();
```

新创建的范围会带有与原范围完全相同的属性。

# 上述小结

DOM2级规范定义了一些模块，用于增强DOM1级。"DOM2级核心"为不同的DOM类型引入了一些与XML命名空间有关的方法。这些变化只在使用XML或XHTML文档时才有用；对于HTML文档没有实际意义。除了与XML命名空间有关的方法外，"DOM2级核心"还定义了以编程方式创建Document实例的方法，也支持了创建DocumentType对象。

"DOM2级样式"模块主要针对操作元素的样式信息而开发，其特性简要总结如下。

每个元素都有一个关联的style 对象，可以用来确定和修改行内的样式。

- 要确定某个元素的计算样式（包括应用给它的所有CSS 规则），可以使用getComputedStyle()方法。
- IE 不支持getComputedStyle()方法，但为所有元素都提供了能够返回相同信息currentStyle属性。
- 可以通过document.styleSheets 集合访问样式表。
- 除IE 之外的所有浏览器都支持针对样式表的这个接口，IE 也为几乎所有相应的DOM功能提供了自己的一套属性和方法。

“DOM2 级遍历和范围”模块提供了与DOM结构交互的不同方式，简要总结如下。

- 遍历即使用NodeIterator 或TreeWalker 对DOM 执行深度优先的遍历。
- NodeIterator 是一个简单的接口，只允许以一个节点的步幅前后移动。而TreeWalker 在提供相同功能的同时，还支持在DOM 结构的各个方向上移动，包括父节点、同辈节点和子节点等方向。
- 范围是选择DOM结构中特定部分，然后再执行相应操作的一种手段。
- 使用范围选区可以在删除文档中某些部分的同时，保持文档结构的格式良好，或者复制文档中的相应部分。
- IE8 及更早版本不支持“DOM2 级遍历和范围”模块，但它提供了一个专有的文本范围对象，可以用来完成简单的基于文本的范围操作。IE9 完全支持DOM遍历。