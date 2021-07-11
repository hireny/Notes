每个面向国际的语言都需要有自己的国际化解决方案，Java 从一开始就是了 `Unicode` 来处理所有的字符串，这让 Java 具备了国际化的一个重要特性。除了提供 `Unicode` 之外，Java 还需要解决不同语言的表示形式。

## Locale

`java.util` 包中提供了 `Locale` 类用来处理国际化的情况。


`Locale` 中也为各个国家预定义了 `Locale` 对象，如下所示。

```java
Locale.CHINA
LOcale.US
```

也预定义了大量的 `Locale` 语言，他们只设定了语言而没有设定国家，如下所示。

```java
Locale.CHINESE;
Locale.ENGLISH;
```