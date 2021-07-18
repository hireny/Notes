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

语言和国家的代码看起来有点乱，因为它们中的有些是从本地语言导出的。德语在德语中是 Dcutsch，中文在中文里是 zhongwen，因此它们分别时 de 和 zh。瑞士是 CH，这是从瑞士联邦的拉丁语 Confoederatio Helvetica 中导出的。

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

有时，你需要显示星期和月份的名字，例如在日历应用中。此时可以调用 DayOfWeek 和 Month 枚举的 getDisplayName 方法：

```java
for (Month m : Month.values())
    System.out.println(m.getDisplayName(textStyle, locale) + " ");
```

下表展示了文本风格，其中 STANDALONE 版本用于格式化日期之外的显示。例如，在芬兰语中，一月在日期中是 “tammikuuta”，但是单独显示时是 “tammikuu”。

|风格|示例|
|:---:|:---:|
|FULL/FULL_STANDALONE|January|
|SHORT/SHORT_STANDALONE|Jan|
|NARROW/NARROW_STANDALONE|J|

注意：星期的第一天可以是星期六、星期日或星期一，这取决于 Locale。你可以像下面这样获取星期的第一天：

```java
DayOfWeek first = WeekFields.of(locale).getFirstDayOfWeek();
```

## 排序和范化

大多数程序员都知道如何使用 String 类中的 compareTo 方法对字符串进行比较。但是，当与人类用户交互时，这个方法就不是很有用了。compareTo 方法使用的是字符串的 UTF-16 编码值，这会导致很荒唐的结果，即使在英文比较中也是如此。比如，下面的 5 个字符串进行排序的结果为：

```
America
Zulu
able
zebra
Angstrom
```

按照字典中的顺序，你希望将大写和小写看作是等价的。对于一个说英语的读者来说，期望的排序结果应该是：

```java
able
America
Angstrom
zebra
Zulu
```

但是，这种顺序对于瑞典用户是不可接受的。在瑞典语中，字母 和 字母 A 是不同的，它应该排在字母 Z 之后！就是说，瑞典用户希望的排序结果是：

```java
able
America
zebra
Zulu
Angstrom
```

为了获得 Locale 敏感的比较复杂，可以调用静态的 Collator.getInstance 方法：

```java
Collator coll = Collator.getInstance(locale);
mords.sort(coll);    // Collator implements Comparator<Object>
```

因为 Collator 类实现了 Comparator 接口。因此，可以传递一个 Collator 对象给 list.sort(Comparator) 方法来对一组字符串进行排序。

排序器有几个高级设置项。你可以设置排序器的强度以此来选择不同的排序行为。字符间的差别可以被分为首要的 primary、次要的 secondary 和再次的 tertiary。比如，在英语中，“A” 和 “Z” 之间的差别被归为首要的，而 “A” 和 “” 之间的差别是其次的，“A” 和 “a” 之间是再次的。

如果将排序器的强度设置成 Collator.PRIMARY，那么排序器将只关注 primary 级的差别。如果设置成 Collator.SECONDARY，排序器将把 secondary 级的差别也考虑进去。就是说，两个字符串在 “secondary” 或 “tertiary” 强度下更容易被区分开来。如下表所示。

如果强度被设置为 Collator.IDENTICAL，则不允许有任何差异。这种设置在于排序器的第二种具有相当技术性的设置，即分解模式，联合使用时，就会显得非常有用。

## 消息格式化

Java 类库中有一个 MessageFormat 类，它与用 printf 方法进行格式化很类似，但是它支持 Locale，并且会对数字和日期进行格式化。我们将在以下各节中审视这种机制。

### 格式化数字和日期

下面是一个典型的消息格式化字符串：

```
"On {2}, a {0} destroyed {1} houses and caused {3} of damage."
```

括号中的数字是占位符，可以用实际的名字和值来替换它们。使用静态方法 MessageFormat.format 可以用实际的值来替换这些占位符。它是一个 “varargs” 方法，所以你可以通过下面的方法提供参数：

```java
String msg = MessageFormat.format("On {2}, a {0} destroyed {1} houses and caused {3} of damage.", "hurricane", 99, new GregorianCalendar(1999, 0, 1).getTime(), 10.0E8);
```

在这个例子中，占位符 {0} 被 “hurricane” 替换，{1} 被 99 替换，等等。

上述例子的结果是下面的字符串：

On 1/1/99 12:00 AM, a hurricane destroyed 99 houses and caused 100,000,000 of damage.

这只是开始，离完美还有距离。我们不想将时间显示为 “12:00 AM”，而且我们想将造成的损失量打印成货币值。通过为占位符提供可选的格式，就可以做到这一点：

On {2, date, long}, a {0} destroyed {1} houses and caused {3, number, currency} of damage.


这段示例代码将打印出：

On January 1, 1999, a hurricane destroyed 99 houses and caused $100,000,000 of damage.

一般来说，占位符索引后面可以跟一个类型（type）和一个风格（style），它们之间用逗号隔开。类型可以是：

```
number
time
date
choice
```

如果类型是 number，那么风格可以是

```
integer
currency
percent
```

或者可以是数字格式模式，就像 $$,##0。

如果类型是 time 或 date，那么风格可以是

```
short
medium
long
full
```

或者是一个日期格式模式，就像 yyyy-MM-dd。

警告：静态的 MessageFormat.format 方法使用当前的 locale 对值进行格式化。要想用任意的 locale 进行格式化，还有一些工作要做，因为这个类还没有提供任何可以使用的 “varargs” 方法。你需要把将要格式化的值置于 Object[] 数组中，就像下面这样：

```java
MessageFormat mf = new MessageFormat(pattern, loc);
String msg = mf.format(new Object[] {values});
```

### 选择格式

让我们仔细地看看前面一节所提到的模式：

"On {2}, a {0} destroyed {1} houses and caused {3} of damage."

如果我们用 “ earthquake” 来替换代表灾难的占位符 {0}，那么，在英语中，这句话的语法就不正确了。

On January 1, 1999, a earthquake destroyed ...

这说明，我们真正希望的是将冠词 “a” 集成到占位符中去：

"On {2}, {0} destroyed {1} houses and caused {3} of damage."

这样我们就应该用 “ a hurricane” 或 “an earthquake” 来替换 {0}。当消息需要被翻译成某种语言，而该语言中的词会随词性的变化而变化时，这种替换方式就特别适用。比如，在德语中，模式可能会是：

"{0} zerstorte am {2} {1} Hauser und richtete einen Schaden von {3} an."

这样，占位符将被正确地替换成冠词和名词的组合，比如 “Ein Wirbelsturm”, "Eine Naturkatastrophe"。

让我们来看看参数 {1}。如果灾难的后果不严重，{1} 的替换值可能是数字 1，消息就编程：

ON January 1, 1999, a mudslide destroyed 1 houses and ...

我们当然希望消息能够随占位符的值而变化，这样就能根据具体的值形成

no houses
one house
2 houses
...

choice 格式化选项就是为了这个目的而设计的。

一个选择格式是一个序列对构成的，每一个对包括

- 一个下限（lower limit）
- 一个格式字符串（format string）

下限和格式字符串由一个 # 符号分隔，对于对之间由符号 | 分隔。

例如：

{1, choice,0#no houses|1#one house|2#{1} houses}

下表显示了格式字符串对 {1} 的不通知产生的作用。

## 文本文件和字符集

### 文本文件

### 行结束符

### 控制台

### 日志文件

### UTF-8 字符顺序标志

### 源文件的字符编码

## 资源包

当本地化一个应用时，可能会有大量的消息字符串、按钮标签和其他的东西需要被翻译。为了能灵活地完成这项任务，你会希望在外部定义消息字符串，通常称之为资源（resource）。翻译人员不需要解除程序源代码就可以很容易地编辑资源文件。

注意：Java 技术资源和 Windows 和 Macintosh 资源不同。Macintosh 或 Windows 可执行文件在程序代码以外的地方存储类似菜单、对话框、图标和消息这样的资源。资源编辑器能够在不影响程序代码的情况下检查并更新这些资源。

### 定义资源包

当本地化一个应用时，会产生很多资源包（resource bundle）。每一个包都是一个属性文件或者是一个描述了与 locale 相关的项的类（比如消息、标签等）。对于每一个包，都要为所有你想要支持的 locale 提供相应的版本。

需要对这些包使用一种统一的命名规则。例如，为德国定义的资源放在一个名为 “包名_de_DE” 的文件中，而为所有说德语的国家所共享的资源则放在名为 “包名_de” 的文件中。一般来说，使用

包名_语言_国家

来命名所有和国家相关的资源，使用

包名_语言

来命名所有和语言相关的的资源。最后，作为后备，可以把默认资源放在一个没有后缀的文件中。

可以用下面的命令加载一个包

```java
ResourceBundle currentResources = ResourceBundle.getBundle(bundleName, currentLocale);
```

getBundle 方法试图加载匹配当前 locale 定义的语言和国家的包。如果失败，通过依次放弃国家和语言来继续进行查找，然后同样的查找被应用于默认的 locale，最后，如果还不行的话就去查看默认的包文件，如果这也失败了，则抛出一个 MissingResourceException 异常。

这就是说，getBundle 方法会视图加载以下的包。

包名_当前Locale的语言_当前Locale的国家_当前Locale的变量
包名_当前Locale的语言_当前Locale的国家
包名_当前Locale的语言
包名_默认Locale的语言_当前Locale的国家_当前Locale的变量
包名_默认Locale的语言_当前Locale的国家
包名_默认Locale的语言
包名

一旦 getBundle 方法定位了一个包，比如，包名_de_DE，它还会继续查找包名_de 和包名这两个包。如果这些包也存在，它们在资源层次中就成为了包名_de_DE 的父包。以后，当查找一个资源时，如果在当前包中没有找到，就去查找其父包。就是说，如果一个特定的资源在当前包中没有被找到，比如，某个特定资源在包名_de_DE 中没有找到，那么就会去查询包名_de 和包名。

这是一项非常有用的服务，如果手工来编写将会非常麻烦。Java 编程语言的资源包机制会自动定位与给定的 locale 匹配得最好的项。可以很容易地把越来越多的本地化信息加到已有的程序中：你需要做的只是增加额外的资源包。

### 属性文件

对字符串进行国际化是很直接的，你可以把所有字符串放到一个属性文件中，比如 MyProgramStrings.properties，这是一个每行存放一个键-值对的文本文件。典型的属性文件看起来就像这样：

```properties
computeButton=Rechnen
colorName=black
defaultPaperSize=210x297
```

然后你就像上一节描述的那样命名你的属性文件。例如

```
MyProgramStrings.properties
MyProgramStrings_en.properties
MyProgramStrings_de_DE.properties
```

你可以直接加载包，如

```java
ResourceBundle bundle = ResourceBundle.getBundle("MyProgramStrings", locale);
```

要查找一个具体的字符串，可以调用

```java
String computeButtonLable = bundle.getString("computeButton");
```

### 包类

为了提供字符串以外的资源，需要定义类，它必需扩展自 ResourceBundle 类。应该使用标准的命名规则来命名你的类，比如

```
MyProgramResources.java
MyProgramResources_en.java
MyProgramResources_de_DE.java
```

你可以使用与加载属性文件相同的 getBundle 方法来加载这个类：

```java
ResourceBundle bundle = ResourceBundle.getBundle("MyProgramResources", locale);
```

警告：当搜索包时，如果在类中的包和在属性文件中的包中都存在匹配，优先选择类中的包。

每一个资源包类都实现了一个查询表。你需要为每一个你想定位的而设置都提供一个关键字字符串，使用这个字符串来提取相应的设置。例如，

```java
Color backgroundColor = (Color) bundle.getObject("backgroundColor");
double[] paperSize = (double[]) bundle.getObject("defaultPaperSize");
```

实现资源包类的最简单方法就是继承 ListResourceBundle 类。ListResourceBundle 让你把所有资源都放到一个对象数组中并提供查找功能。要遵循以下的代码框架：

```java

```

或者，你的资源包类可以扩展 ResourceBundle 类。然后需要实现两个方法，一是枚举所有键，二是用给定的键查找相应的值：

```java
Enumeration<String> getKeys();
Object handleGetObject(String key);
```

ResourceBundle 类的 getObject 方法会调用你提供的 handleGetObject 方法。

336页