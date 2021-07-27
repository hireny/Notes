每个面向国际的语言都需要有自己的国际化解决方案，Java 从一开始就是了 `Unicode` 来处理所有的字符串，这让 Java 具备了国际化的一个重要特性。除了提供 `Unicode` 之外，Java 还需要解决不同语言的表示形式。

## Locale

`java.util` 包中提供了 `Locale` 类用来处理不同的语言和区域上的国际化问题。使用 `Locale` 类可以定制一个区域的信息，而 `Locale` 类提供了如下构造方法来构造 `Locale` 对象。

```java
public final class Locale implements Cloneable, Serializable {
    // 从语言，国家和变体构造语言环境
    public Locale(String language, String country, String variant) {
        ···
    }
    // 从语言和国家构造语言环境
    public Locale(String language, String country) {
        ···
    }
    // 从语言代码构造语言环境
    public Locale(String language) {
        ···
    }
}
```

预定义了大量的 `Locale` 语言，他们只设定了语言而没有设定国家，如下所示。

```java
// 英语
public static final Locale ENGLISH = createConstant("en", "");
// 法语
public static final Locale FRENCH = createConstant("fr", "");
// 德语
public static final Locale GERMAN = createConstant("de", "");
// 意大利语
public static final Locale ITALIAN = createConstant("it", "");
// 日语
public static final Locale JAPANESE = createConstant("ja", "");
// 韩语
public static final Locale KOREAN = createConstant("ko", "");
// 汉语
public static final Locale CHINESE = createConstant("zh", "");
// 简体中文
public static final Locale SIMPLIFIED_CHINESE = createConstant("zh", "CN");
// 繁体中文
public static final Locale TRADITIONAL_CHINESE = createConstant("zh", "TW");
```

Locale 中也为各个国家预定义了 Locale 对象，如下所示。

```java
// 法国
public static final Locale FRANCE = createConstant("fr", "FR");
// 德国
public static final Locale GERMANY = createConstant("de", "DE");
// 意大利
public static final Locale ITALY = createConstant("it", "IT");
// 日本
public static final Locale JAPAN = createConstant("ja", "JP");
// 韩国
public static final Locale KOREA = createConstant("ko", "KR");
// 中国
    public static final Locale CHINA = SIMPLIFIED_CHINESE;
// 中华人名共和国
public static final Locale PRC = SIMPLIFIED_CHINESE;
// 台湾
public static final Locale TAIWAN = TRADITIONAL_CHINESE;
// 英国
public static final Locale UK = createConstant("en", "GB");
// 美国
public static final Locale US = createConstant("en", "US");
// 加拿大
public static final Locale CANADA = createConstant("en", "CA");
// 法国裔加拿大
public static final Locale CANADA_FRENCH = createConstant("fr", "CA");
// 表示根语言环境的常量
public static final Locale ROOT = createConstant("", "");
```

```java
// 此方法返回所有已安装的语言环境的数组
static Locale[] getAvailableLocales() {}
// 此方法回去默认语言环境的当前值的 Java 虚拟机实例
staitc Locale getDefault();
// 此方法返回适合显示给用户的名称语言环境的国家
String getDisplayCountry();
// 此方法返回适合显示给用户的名称语言环境的国家
String getDisplayCountry(Locale inLocale);
// 此方法返回的语言环境语言适合于显示给用户的名称
String getDisplayLanguage();
// 此方法返回的语言环境语言适合于显示给用户的名称
String getDisplayLanguage(Locale inLocale);
// 此方法返回的语言环境，是否适合显示给用户的名称。
String getDisplayName();
// 此方法返回的语言环境，是否适合显示给用户的名称。
String getDisplayName(Locale inLocale);
// 此方法返回的语言环境变量代码，适合显示给用户的名称。
String getDisplayVariant();
// 此方法返回的语言环境变量代码，适合显示给用户的名称。
String getDisplayVariant(Locale inLocale)
// 此方法返回一个三个字母的缩写本地区的国家。
String getISO3Country() 
// 此方法返回返回一个三字母缩写在这个地方的语言。
String getISO3Language() 
// 这个方法返回ISO 3166中定义的所有2个字母的国家代码的列表。
static String[] getISOCountries() 
// 这个方法返回ISO 639中定义的所有两字母语言代码的列表。
static String[] getISOLanguages();
// 此方法返回语言代码为这个区域设置，它要么是空字符串或小写的ISO639代码
String getLanguage();
// 此方法返回的变量代码为这个区域设置。
String getVariant();
// 此方法设置的默认语言环境的Java虚拟机实例
static void setDefault(Locale newLocale);
```

Java 7 提供了下面这样使用标签字符串构建 `Locale` 对象的方法：

```java
Locale usEnglish = Locale.forLanguageTag("en-US");
```

`getAvailableLocale` 方法会返回由 Java VM 所能够识别的所有 `Locale` 构成的数组。

`Locale` 中有一个方法 `setDefault()`，用于指定地区为默认地区，如下所示。

```java
static void setDefault(Locale localeObj);
```

可以使用 `getDefault()` 方法获取当前的 `Locale` 对象，如下所示。

```java
static Locale getDefault();
```

## ResourceBundle

Java 中本地化一个应用，会产生很多资源包，每一个包都是一个属性文件或者是一个描述了与 `locale` 相关的项的类。对于每一个包，都要为所有你想要支持的 `locale` 提供相应的版本。

Java 可以在 `resources` 资源包中为不同的 `locale` 设置对应的资源文件，然后使用 `ResourceBundle` 类进行加载。如下所示，通过 `ResourceBundle` 类提供的 `getBundle` 静态方法，来根据 `locale` 自动绑定对应的资源文件。

```java
ResourceBundle bundle = ResourceBundle.getBundle("message", locale);
```

查找一个具体的字符串，可以调用

```java
String language = bundle.getString("language");
```

定义相应的资源文件，我们将资源文件放在一个资源包中，一个资源包中每个资源文件必须拥有共同的基名。除了基名，每个文件的名称中还必须有标识其本地信息的附加部分。例如，一个资源包的基名是 `message`，则与中文、英文环境相对应的资源文件名分别为：

```java
message_zh_CN.properties
message_en_US.properties
```

需要对这些包使用一种统一的命名规则。

可以把默认资源放在一个没有后缀的文件中。

一旦 getBundle 方法定位了一个包，比如，包名de_DE，它还会继续查找包名de 和包名这两个包。如果这些包也存在，它们在资源层次中就成为了包名de_DE 的父包。以后，当查找一个资源时，如果在当前包中没有找到，就去查找其父包。就是说，如果一个特定的资源在当前包中没有被找到，比如，某个特定资源在包名de_DE 中没有找到，那么就会去查询包名_de 和包名。

### 属性文件

后缀名为 `.properties` 的文件中，属性是采用键值对的形式，如下所示：

```properties
language=zh
color=black
size=210x297
```

然后可以将属性文件根据不同情景命名，如下所示。

```shell
message.properties
message_en.properties
message_zh_CN.properties
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

`ResourceBundle` 类的 `getObject` 方法会调用你提供的 `handleGetObject` 方法。

----------------------------------------------------------

### 扩展

java.util 包提供了 3 个用来帮助国际化程序的类。第 1 个是抽象类 ResourceBundle。该类定义的方法用于管理地区敏感资源的集合，例如用于显示程序中用户界面元素的字符串。可以定义两套或更多套用于支持各种语言的翻译过的字符串，比如英语、德语或汉语，每套翻译过的字符串都在自己的资源包中。然后可以加在适用于当前地区的资源包，并使用其中的字符串构造程序的用户界面。

资源包通过它们的家族名称进行标识。对于家族名称，可以添加两字符的小写语言代码，语言代码用于指定语言。对于这种情况，如果需要的地区与语言代码匹配，就使用该版本的资源包。例如，家族名称为 SampleRB 的资源包，可能有一个称为 SampleRB_de 的德语版、一个称为 SampleRB_ru 的俄语版。所以，如果地区是 Locale.GERMAN，就会使用 SampleRB_de。

通过在语言代码之后指定国家代码，还可以标示与特定国家有关的语言的特定版本。国家代码是两字符的大写标识符，例如安达利亚的国家代码是 AU、印度的国家代码是 IN。将国家代码链接到资源包名称时，在国家代码的前面也有一个下划线。只包含家族名称的资源包是默认包。当没有特定语言的包能够使用时，使用默认包。

```java
public abstract class ResourceBundle {

}
```

抽象类 ResourceBundle 中的 Control 类用于控制资源包的加载过程。

ResourceBundle 有两个子类。第一个是 `PropertyResourceBundle`，该类使用属性文件管理资源，`PropertyResourceBundle` 没有添加自己的方法。第二个是抽象类 `ListResourceBundle`，该类使用键值对数组管理资源。ListResourceBundle 添加了 getContents() 方法，该类的所有子类都必须实现这个方法，这个方法如下所示：

```java
protected abstract Object[][] getContents();
```

该方法返回一个二维数组，包含表示资源的键值对。键必须是字符串，值通常时字符串，不过也可以是其他类型。

下面是一个演示未命名模块中资源包用法的例子。资源包的家族名称为 SampleRB。通过扩展 ListResourceBundle，创建了这个家族的两个资源包类。第一个名为 SampleRB，是默认包，该类如下所示：

