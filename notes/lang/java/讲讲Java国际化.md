世界丰富多彩，我们希望大部分居民都能对你的软件感兴趣。一方面，因特网早已成为我们打破了国家之间的界限。另一方面，如果你不去关注国际用户，你的产品的应用情况就会受到限制。

Java 编程语言是第一种设计成为全面支持国际化的语言。从一开始，它就具备了进行有效的国际化所必须的一个重要特性：使用 Unicode 来处理所有的字符串。支持 Unicode 使得在 Java 编程语言中，编写程序来操作多种语言的字符串变得异常方便。

多数程序员相信他们的程序进行国际化需要做的所有事情就是支持 Unicode 并在用户接口中对消息进行翻译。但是，在本章你将会看到，国际化一个程序所要做的事情绝不仅仅是提供 Unicode 支持。在世界的不同地方，日期、时间、货币甚至数字的格式都不相同。你需要用一种简单的方法来为不同的语言配置菜单与按钮的名字，消息字符串和快捷键。

在本重中，我们将演示如何编写国际化的 Java 应用程序以及如何将日期、时间、数字、文本和图形用户界面本地化，还将演示 Java 提供的编写国际化程序的工具。最后以一个完整的例子来作为本章的结束，它是一个退休金计算器、带有英语、德语和中文用户界面。

## Locale 对象

当你看到一个面向国际市场的应用软件时，它与其他软件最明显的区别就是语言。其实如果以这种外在的不同来判断是不是真正的国际化就太片面了：不同的国家可以使用相同的语言，但是为了使两个国家的用户都满意，你还有很多工作要做。就像 Oscar Wilde 所说的那样，“我们现在真的是每件东西都和美国一样，当然，语言除外”。

不过怎样，菜单、按钮标签和程序的消息需要转换成本地语言：有时候还需要用不同的脚本来润色。这种差别很细微；比如，数字在英语和德语中格式很不相同。对于德国用户，数字 123,456.78 应该显示为 123.456,78

小数点和十进制数的逗号分隔符的角色是相反的！在日期的显示上也有相似的变化。在美国，日期显示为

有若干个专门负责格式处理的类。为了对格式化进行控制，可以使用 Locale 类。locale 由多达 5 个部分构成：

1. 一种语言，由 2 个或 3 个小写字母表示，例如 en（英语）、de（德语）和zh（中文）。表 7-1 展示了常见的 ISO-639-1 语言代码。

|语言|代码|语言|代码|
|:---:|:---:|:---:|:---:|
|Chinese|zh|Italian|it|
|Danish|da|Japanese|ja|
|Dutch|nl|Korean|ko|
|English|en|Norwegian|no|
|French|fr|Portuguese|pt|
|Finnish|fi|Spanish|es|
|German|de|Swedish|sv|
|Greek|el|Turkish|tr|

2. 可选的一段脚本，由首字母大写的四个字母表示，例如 Latn（拉丁文）、Cyrl（西里尔文）和 Hant（繁体中文字符）。这个部分很有用，因为有些语言，例如塞尔维亚语，可以用拉丁文或西里尔文书写，而有些中文读者更喜欢阅读繁体中文而不是简体中文。
3. 可选的一个国家或地区，由 2 个大写字母或 3 个数字表示，比如 US（美国）和 CH（瑞士）。下表展示了常用的代码。
4. 可选的一个变体，用于指定各种杂项特性，例如方言和拼写规则。变体现在已经很少使用了。过去曾经有一种挪威语的变体 “尼诺斯克语”，但是它现在已经用另一种不同的代码 nn 来表示了。过去曾经用于日本帝国历和泰语数字的变体现在也都被表示成了扩展（请参见下一条）。
5. 可选的一个扩展。扩展描述了日历（例如日本历）和数字（代替西方数字的泰语数字）等内容的本地偏好。Unicode 标准规范了其中的某些扩展，这些扩展应该以 u- 和两个字母代码开头，这两个字母的代码指定了该扩展处理的是日历（ea）还是数字（nu），或者是其他内容。例如，扩展 u-nu-thai 表示使用泰语数字。其他扩展是完全任意的，并且以 x- 开头，例如 x-java。

|语言|代码|语言|代码|
|:---:|:---:|:---:|:---:|
|Austria|AT|Japan|JP|
|Beigium|BE|Korea|KR|
|Canada|CA|The Netherlands|NL|
|China|CN|Norway|NO|
|Denmark|DK|Portugal|PT|
|Finland|FI|Spain|ES|
|Germany|DE|Sweden|SE|
|Great Britain|GB|Switzerland|CH|
|Greece|GR|Taiwan|TW|
|Ireland|IE|Turkey|TR|
|Italy|IT|United States|US|

locale 的规则是在 Internet Engineering Task Force 的 “Best Current Practices” 备忘录 BCP47 进行了明确阐述。

语言和国家的代码看起来有点乱，因为它们中的有些是从本地语言导出的。德语在德语中是 Dcutsch，中文在中文里时 zhongwen，因此它们分别时 de 和 zh。瑞士是 CH，这是从瑞士联邦的拉丁语 Confoederatio Helvetica 中导出的。

locale 是用标签描述的，标签是由 locale 的各个元素通过连字符连接起来的字符串，例如 en-US。

在德国，你可以使用 de-DE。瑞士有 4 种官方语言（德语、法语、意大利语和里托罗曼斯语）。在瑞士将德语的人希望使用的 locale 是 de-CH。这个 locale 会使用德语的规则、但是货币值会表示成瑞士法郎而不是欧元。

如果只指定了语言，例如 de，那么该 locale 就不能用于与国家相关的场景，比如货币。

我们可以像下面这样用标签字符串来构建 Locale 对象：

```java
Locale usEnglish = Locale.forLanguageTag("en-US");
```

`toLanguageTag` 方法可以生成给定 Locale 的语言标签。例如，Local.US.toLanguageTag() 生成的字符串是 “en-US”。

为方便起见，Java SE 为各个国家预定义了 Locale 对象：

```java
Locale.CANADA
Locale.CANADA_FRENCH
...
```

Java SE 还预定义了大量的语言 Locale，它们只设定了语言而没有设定位置：

```java
Locale.CHINESE
Locale.ENGLISH
...
```

最后，静态的 `getAvailableLocale` 方法会返回由 Java 虚拟机所能够识别的所有 Locale 构成的数组。

除了构建一个 Locale 或使用预定义的 Locale 外，还可以有两种方法来获得一个 Locale 对象。

Locale 类的静态 getdefault 方法可以获得作为本地操作系统的一部分而存放的默认 Locale。可以调用 setDefault 来改变默认的 Java Locale；但是，这种改变只对你的程序有效，不会对操作系统产生影响。

对于所有与 Locale 相关的工作类，可以返回一个它们所支持的 Locale 数组。比如，

```java
Locale[] supportedLocales = NumberFormat.getAvailableLocales();
```

将返回所有 DateFormat 类所能够处理的 Locale。

提示：为了测试，你也许希望改变你的程序的默认 Locale，可以在启动程序时提供语言和地域特性。比如，下面的语句将默认的 Locale 设为 de-CH：

```shell
java -Duser.language=de -Duser.region=CH MyProgram
```

一旦有了一个 Locale，你能用它做什么呢？答案是它所能做的事情很有限。Locale 类中唯一有用的是那些识别语言和国家代码的方法，其中最重要的一个是 getdisplayName，它返回一个描述 Locale 的字符串。这个字符串并不包含前面所说的由两个字母组成的代码，而是以一种面向用户的形式来表现，比如

```java
German(Switzerland)
```

事实上，这里有一个问题，显示的名字是以默认的 Locale 来表示的，这可能不太恰当。如果你的用户已经选择了德语作为首选的语言，那么你可能希望将字符串显示成德语。通过将 German Locale 作为参数传递就可以做到这一点：代码

```java
Locale loc = new Locale("de", "CH");
System.out.println(loc.getDisplayName(Locale.GERMAN));
```

将打印出

```shell
Deutsch(Schweiz)
```

这个例子说明了为什么需要 Locale 对象。你把它传给 Locale 感知的哪些方法，这些方法将根据不同的地域产生不同形式的文本。在下一节中你可以见到大量的例子。


## 数字格式

我们已经提出了数字和货币的格式是高度依赖于 locale 的。Java 类库提供了一个格式器（formatter）对象的集合，可以对 java.text 包中的数字值进行格式化和解析。你可以通过下面的步骤对特定 Locale 的数字进行格式化：

1. 使用上一节的方法，得到 Locale 对象。
2. 使用一个 “工厂方法” 得到一个格式器对象。
3. 使用这个格式器对象来完成格式化和解析工作。

工厂方法是 NumberFormat 类的静态方法，它们接受一个 Locale 类型的参数。总共有 3 个工厂方法：getNumberInstance、getCurrencyInstance 和 getPercentInstance，这些方法返回的对象可以分别对数字、货币量和百分比进行格式化和解析。例如，下面显示了如何对德语中的货币值进行格式化。

```java
Locale loc = Locale.GERMAN;
NumberFormat curFmt = NumberFormat.getCurrencyInstance(loc);
double amt = 123456.78;
String result = currFmt.format(amt);
```

结果是

123.456,78 

请注意，货币符号是，而且位于字符串的最后。同时还要注意到小数点和十进制分隔符与其他语言中的情况是相反的。

相反地，如果要想读取一个按照某个 Locale 的惯用法而输入或存储的数字，那么就需要使用 parse 方法。比如，下面的代码解析了用户输入到文本框中的值。parse 方法能够处理小数点和分隔符以及其他语言中的数字。

```java
TextField inputField;
···
NumberFormat fmt = NumberFomat.getNumberInstance();
// get the number formatter for default locale
Number input = fmt.parse(inputField.getText().trim());
double x = input.doubleValue();
```

parse 的返回类型是抽象类型的 Number。返回的对象是一个 Double 或 Long 的包装器类对象，这取决于被解析的数字是否是浮点数。如果不关心两者的差异，可以直接使用 Number 类中的 doubleValue 方法来读取被包装的数字。

警告：Number 类型的对象并不能自动转换成相关的基本类型，因此，不能直接将一个 Number 对象赋给一个基本类型，而应该使用 doubleValue 或 intValue 方法。

如果数字文本的格式不正确，该方法会抛出一个 ParseException 异常。例如，字符串以空白字符开头是不允许的（可以调用 trim 方法来去掉它）。但是，任何跟在数字之后的字符都将被忽略，所以这些跟在后面的字符是不会引起异常的。

请注意，由 getXxxInstance 工厂方法的类并非是 NumberFormat 类型的。NumberFormat 类型是一个抽象类，而我们实际上得到的格式器是它的一个子类。工厂方法只知道如何定位属于特定 locale 的对象。

可以用静态的 getAvailableLocales 方法得到一个当前所支持的 Locale 对象列表。这个方法返回一个 Locale 对象数组，从中可以获得针对它们的数字格式器对象。

## 货币

为了格式化货币值，可以使用 NumberFormat.getCurrencyInstance 方法。但是，这个方法的灵活性不好，它返回的是一个只针对一种货币的格式器。假设你为一个美国客户准备了一张货物单，货物中有些货物的金额是用美元表示的，有些是用欧元表示的，此时，你不能只是使用两种格式器：

```java
NumberFormat dollarFormatter = NumberFormat.getCurrencyInstance(Locale.US);
NumberFormat euroFormatter = NumberFormat.getCurrencyInstance(Locale.GERMANY);
```

因为，这样一来，你的发票看起来非常奇怪，有些金额的格式像  100 000，另一些则像 100.000（注意，有缘值使用小数点而不是逗号作为分隔符）。

处理这样的情况，应该使用 Currency 类来控制被格式器所处理的货币。可以通过将一个货币标识符传给静态的 Currency.getInstance 方法来得到一个 Currency 对象，然后对每一个格式器都调用 setCurrency 方法。下面展示如何为你的美国客户设置欧元的格式：

```java
NumberFormat euroFormatter = NumberFormat.getCurrencyInstance(Locale.US);
euroFormatter.setCurrency(Currency.getInstance("EUR"));
```

货币标识符由 ISO 4217 定义。

|货币值|标识符|货币值|标识符|
|:---:|:---:|:---:|:---:|
|U.S.Dollar|USD|Chinese Renminbi(Yuan)|CNY|
|Euro|EUR|Indian Rupee|INR|
|British Pound|GBP|Russian Ruble|RUB|
|Japanese Yen|JPY|||

## 日期和时间

当格式化日期和时间时，需要考虑 4 个与 Locale 相关的问题：

- 月份和星期应该用本地语言来表示。
- 年月日的顺序要符合本地习惯。
- 公历可能不是本地首选的日期表示方法。
- 必须要考虑本地的时区。

`java.time` 包中的 DateTimeFormatter 类可以处理这些问题。首先挑选表 7-4 中所示的一种格式风格，然后获取一个格式器：

这些格式器都会使用当前的 Locale。为了使用不同的 Locale，需要使用 withLocale 方法：

```java
DateTimeFormatter dateFormatter = DateTimeFormatter.ofLocalizedDate(style).withLocale(locale);
```

现在你可以格式化 LocaleDate、LocaleDateTime、LocaleTime 和 ZonedDateTime 了：

```java
ZonedDateTime appointment = ...;
String formatted = formatter.format(appointment);
```

可以使用 LocaleDate、LocaleDateTime、LocalTime 和 ZonedDateTime 的静态的 parse 方法之一来解析字符串中的日期和时间：

```java
LocalTime time = LocalTime.parse("9:32 AM", formatter);
```

这些方法不适合解析人类的输入，至少是不适合解析未做预处理的人类输入。例如，用于美国的短时间格式器可以解析 “9:32 AM”，但是解析不了 "9:32AM" 和 "9:32 am"。

警告：日期格式器可以解析不存在的日期，例如 November 31，它会将这种日期调整为给定月份的最后一天。

316页

