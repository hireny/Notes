# 前言

在Java 8中， 整合了许多 `Joda-Time` 的特性，而开发的 `java.time` 支持全新的日期和时间API。下面我们一起探索新的日期和时间API所提供的新特性。

Date-Time API 由主包 `java.time` 和四个子包组成：

|包名|描述|
|:---:|:---:|
|java.time|日期，时间，瞬间和持续时间的主要API。基于ISO-8601中定义的日历系统，并且不可变且线程安全|
|java.time.chrono|除默认ISO之外的日历系统的API|
|java.time.format|格式化和解析日期和时间的类|
|java.time.temporal|扩展API，允许日期和时间类之间互操作，查询和调整|
|java.time.zone|支持时区、时区的偏移以及时区规则|

# 日期时间类

日期时间API提供四个专门处理日期信息的类，不考虑时间或时区。

## Clock

Java 8新增 `Clock` 时钟类，是对当前时区敏感的。它提供了根据指定时区获取当前时刻，或当前日期和时间。它可以替代 `System.currentTimeMillis()` 和 `TimeZone.getDefault()`。

这里提供的时钟实现基于 `System.currentTimeMillis()`。该方法几乎不保证时钟的准确性。需要更精确时钟的应用程序必须使用不同的外部时钟（如NTP服务器）实现此抽象类。

下面看一下 `Clock` 时钟类支持的方法：

```java
public abstract class Clock {
	// 返回表示格林尼治/UTC时区的时钟
    public static Clock systemUTC() {...}
    // 返回表示系统默认时区的时钟
    public static Clock systemDefaultZone() {...}
    // 返回表示传入的时区的时钟
    public static Clock system(ZoneId zone) {...}
    // 获取时钟，使用最佳可用系统时钟在一秒钟内返回当前瞬间滴答作响。
    public static Clock tickSeconds(ZoneId zone) {...}
    // 获取时钟，使用最佳可用系统时钟在一分钟内返回当前瞬间滴答作响。
    public static Clock tickMinutes(ZoneId zone) {...}
    // 获取时钟，将时钟从指定的时钟截断返回到指定持续时间的最近出现。 
    public static Clock tick(Clock baseClock, Duration tickDuration) {...}
    // 获得始终返回相同时刻的时钟。
    public static Clock fixed(Instant fixedInstant, ZoneId zone) {...}
    // 获取一个时钟，从指定的时钟周期返回时钟，并添加指定的时间
    public static Clock offset(Clock baseClock, Duration offsetDuration) {...}
    protected Clock() {}
    // 获取用于创建日期和时间的时区
    public abstract ZoneId getZone();
    // 以不同的时区返回此时钟的副本。
    public abstract Clock withZone(ZoneId zone);
    // 获取时钟的当前毫秒时间
    public long millis() {...}
    // 获取当前的时钟瞬间
    public abstract Instant instant();
    @Override
    public boolean equals(Object obj) {...}
    @Override
    public  int hashCode() {...}
}
```

```java
// 根据系统时间返回并设置时区为UTC
Clock clockUTC = Clock.systemUTC();
// 根据系统时间返回并设置时区为默认时区
Clock clockDefalutZone = Clock.systemDefaultZone();
// 根据系统时间返回并设置时区为 Asia/Shanghai 亚洲/上海 的时区
Clock clockZone = Clock.system(ZoneId.of("Asia/Shanghai"));
```

上述三个静态方法主要是对


`Clock` 时钟类是一个抽象类，它的实现该类有 `SystemClock`

```java
public static Clock offset(Clock baseClock, Duration offsetDuration)
参数 baseClock - 添加持续时间的基本时钟，而不是 null。
参数 offsetDuration - 要添加的持续时间，而不是 null。
```

## LocalDate

从 `LocalDate` 类的声明中可以看出：

```java
public final class LocalDate
        implements Temporal, TemporalAdjuster, ChronoLocalDate, Serializable
```

`LocalDate` 被 `final` 修饰，是一个不可变的日期时间对象。它是 `ISO-8601` 日历系统的实现。`LocalDate`类表示一个具体的日期，但不包含具体时间，也不包含时区信息。

`LocalDate` 获取获取当前日期的静态方法有三种：

```java
// 在默认时区中从系统时钟获取当前LocalDate。
public static LocalDate now() {...}
// 获取指定时区的当前LocalDate
public static LocalDate now(ZoneId zone) {...}
// 获取指定时钟的当前LocalDate
public static LocalDate now(Clock clock) {...}
```

查看获取当前日期的例子：

```java
public static void main(String[] args) {
	// 获取的是系统默认时区的日期
	LocalDate now = LocalDate.now();
	System.out.println(now);
	// 传入的是亚洲/上海的时区
	LocalDate nowWithZoneId = LocalDate.now(ZoneId.of("Asia/Shanghai"));
	System.out.println(nowWithZoneId);
	// 传入的时钟对象的时区为UTC
	LocalDate nowWithClock = LocalDate.now(Clock.systemUTC());
	System.out.println(nowWithClock);
}
```

输出结果：

```shell
2020-07-15
2020-07-15
2020-07-14
```

除了当前日期，还可以使用 `of()` 方法进行实例化：

```java
// 根据传入的年月日获取LocalDate实例（月份是Java封装的枚举类）
public static LocalDate of(int year, Month month, int dayOfMonth) {...}
// 根据传入的年月日获取LocalDate实例（月份是自己输入的数字）
public static LocalDate of(int year, int month, int dayOfMonth) {...}
// 根据传入的年份与天数来获取LocalDate实例
public static LocalDate ofYearDay(int year, int dayOfYear) {...}
// 根据传入天数（基于1970-01-01）来获取LocalDate实例
public static LocalDate ofEpochDay(long epochDay) {...}
```

查看使用 `of()` 获取日期的例子：

```java
public static void main(String[] args) {
	LocalDate dateOfYearMonthDay1 = LocalDate.of(1997, Month.MAY, 31);
	System.out.println(dateOfYearMonthDay1);
	LocalDate dateOfYearMonthDay2 = LocalDate.of(1997, 5, 31);
	System.out.println(dateOfYearMonthDay2);
	LocalDate dateOfYearDay = LocalDate.ofYearDay(1997, 362);
	System.out.println(dateOfYearDay);
	LocalDate dateOfEpochDay = LocalDate.ofEpochDay(365);
	System.out.println(dateOfEpochDay);
}
```

输出结果：

```shell
1997-05-31
1997-05-31
1997-12-28
1971-01-01
2004-08-10
```

上面的方法都是指定年月日或者根据指定年数或新纪元日开始计算天数的方法。除了这些，我们还可以使用下面的方法来进行实例化：


```java
// 根据传入的时间访问器获取LocalDate实例
public static LocalDate from(TemporalAccessor temporal) {...}
// 根据传入文本解析，获取LocalDate实例
public static LocalDate parse(CharSequence text) {...}
// 根据传入文本与格式化进行解析，获取LocalDate实例
public static LocalDate parse(CharSequence text, DateTimeFormatter formatter) {...}
```

查看一下上述实例化例子：

```java
public static void main(String[] args) {
	LocalDate dateOfFrom = LocalDate.from(ZonedDateTime.now());
	System.out.println(dateOfFrom);

	LocalDate dateOfParse = LocalDate.parse("2017-03-22");
	System.out.println(dateOfParse);

	DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("dd MMM yyyy", Locale.ENGLISH);
	LocalDate dateOfFormatParse = LocalDate.parse("03 Aug 2019", dateTimeFormatter);
	System.out.println(dateOfFormatParse);
}
```

输出结果：

```shell
2020-07-15
2017-03-22
2019-08-03
```

创建了 `LocalDate` 实例后，我们可以根据该实例获取年份、月份、星期等日期信息。

```java
// 获取年份
public int getYear();
// 获取月份
public int getMonthValue();
// 获取当前月份的第几天
public int getDayOfMonth();
// 获取星期
public DayOfWeek getDayOfWeek();
// 判断是否为闰年
public boolean isLeapYear();
```

`LocalDate` 类还有很多方法，就不一一列举了。


## LocalTIme

从 `LocalTime` 的声明：

```java
public final class LocalTime
        implements Temporal, TemporalAdjuster, Comparable<LocalTime>, Serializable
```

也可以看出，`LocalTime` 也是被 `final` 修饰的不可变类，但与 `LocalDate` 不同的是，`LocalDate` 是日期类，而 `LocalTime` 是时间类，可以表示 `hh:ss`。时间精度可以表示到纳秒。

`LocalTime` 也有获取当前时间的静态方法：

```java
// 使用时钟的默认时区获取当前LocalTime实例
public static LocalTime now() {...};
// 获取指定时区的当前LocalTime实例
public static LocalTime now(ZoneId zone) {...}
// 获取指定时钟的当前LocalTime实例
public static LocalTime now(Clock clock) {...}
```

查看获取当前时间的例子：

```java
public static void main(String[] args) {
	LocalTime timeOfTime = LocalTime.now();
	System.out.println(timeOfTime);
	LocalTime timeOfZone = LocalTime.now(ZoneId.of("Asia/Shanghai"));
	System.out.println(timeOfZone);
	LocalTime timeOfClock = LocalTime.now(Clock.systemUTC());
	System.out.println(timeOfClock);
}
```

输出结果：

```shell
23:52:00.842133400
23:52:00.843155500
15:52:00.843155500
```

除了指定当前时间，还可以使用 `of()` 指定当前小时、分钟、秒、纳秒来实例化：

```java
public static LocalTime of(int hour, int minute) {...}
public static LocalTime of(int hour, int minute, int second) {...}
public static LocalTime of(int hour, int minute, int second, int nanoOfSecond) {...}
```

还有指定秒数与纳秒数来实例化的静态方法：

```java
public static LocalTime ofSecondOfDay(long secondOfDay) {...}
public static LocalTime ofNanoOfDay(long nanoOfDay) {...}
```

`LocalTime` 也有根据传入的时间对象 或 字符串+格式化 来获取LocalTime实例：

```java
public static LocalTime from(TemporalAccessor temporal) {...}
public static LocalTime parse(CharSequence text) {...}
public static LocalTime parse(CharSequence text, DateTimeFormatter formatter) {...}
```

创建了 `LocalTime` 实例后，我们也可以获取该实例小时、分钟、描述和纳秒等信息。

```java
// 获取小时
public int getHour() {...}
// 获取分钟
public int getMinute() {...}
// 获取秒数
public int getSecond() {...}
// 获取纳秒数
public int getNano() {...}
```

## LocalDateTime

查看 `LocalDateTime` 的声明：

```java
public final class LocalDateTime
        implements Temporal, TemporalAdjuster, ChronoLocalDateTime<LocalDate>, Serializable
```

就知道 `LocalDateTime` 与上两个表示日期与时间的类都是被 `final` 修饰的不可变的时间对象。它同时表示日期和时间，但不带有时区信息，你可以通过 `now()` 与指定时区、时钟来创建当前日期时间。

```java
// 使用时钟的默认时区获取LocalDateTime实例
public static LocalDateTime now() {...}
// 获取指定时区的当前LocalDateTime实例
public static LocalDateTime now(ZoneId zone) {...}
// 获取指定时钟的当前LocalDateTime实例
public static LocalDateTime now(Clock clock) {...}
```

查看获取当前日期时间的例子：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfNow = LocalDateTime.now();
	System.out.println(dateTimeOfNow);
	LocalDateTime dateTimeOfZone = LocalDateTime.now(ZoneId.of("Asia/Shanghai"));
	System.out.println(dateTimeOfZone);
	LocalDateTime dateTimeOfClock = LocalDateTime.now(Clock.systemUTC());
	System.out.println(dateTimeOfClock);
}
```

输出结果：

```shell
2020-07-16T18:16:13.773716400
2020-07-16T18:16:13.774713600
2020-07-16T10:16:13.774713600
```

`LocalDateTime` 也可以指定年月日时分秒来实现实例化：

```java
public static LocalDateTime of(int year, Month month, int dayOfMonth, int hour, int minute) {...}
public static LocalDateTime of(int year, Month month, int dayOfMonth, int hour, int minute, int second) {...}
public static LocalDateTime of(int year, Month month, int dayOfMonth, int hour, int minute, int second, int nanoOfSecond) {...}
public static LocalDateTime of(int year, int month, int dayOfMonth, int hour, int minute) {...}
public static LocalDateTime of(int year, int month, int dayOfMonth, int hour, int minute, int second) {...}
public static LocalDateTime of(int year, int month, int dayOfMonth, int hour, int minute, int second, int nanoOfSecond) {...}
```

例子：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfYearMonthDay = LocalDateTime.of(2000, Month.AUGUST, 27, 18, 50, 52, 999999999);
	System.out.println(dateTimeOfYearMonthDay);
}
/**
 * 输出结果：
 * 2000-08-27T18:50:52.999999999
 */
```

`LocalDateTime` 也可以使用 `Instant` 或 `Epoch` 新纪元进行实例化：

```java
// 基于Instant瞬时与时区实例化
public static LocalDateTime ofInstant(Instant instant, ZoneId zone) {...}
// 基于新纪元偏移秒数、纳秒数和时间偏移量获取LocalDateTime实例
public static LocalDateTime ofEpochSecond(long epochSecond, int nanoOfSecond, ZoneOffset offset) {...}
```

例子：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfInstant = LocalDateTime.ofInstant(Instant.now(), ZoneId.of("UTC"));
	System.out.println(dateTimeOfInstant);
	LocalDateTime dateTimeOfEpoch = LocalDateTime.ofEpochSecond(2000, 999999, ZoneOffset.UTC);
	System.out.println(dateTimeOfEpoch);
}
/**
 * 输出结果：
 * 2020-07-16T11:08:56.669023800
 * 1970-01-01T00:33:20.000999999
 */
```

`LocalDateTime` 也有根据字符串与格式化来进行实例化：

```java
public static LocalDateTime parse(CharSequence text) {...}
public static LocalDateTime parse(CharSequence text, DateTimeFormatter formatter) {...}
```

解析的方式与 `LocalDate` 和 `LocalTime` 一样：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfParse = LocalDateTime.parse("2017-03-22T23:52:10.999999999");
	System.out.println(dateTimeOfParse);
	DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("dd MMM yyyy HH:mm:ss.SSSSSSSSS", Locale.ENGLISH);
	LocalDateTime dateTimeOfFormatParse = LocalDateTime.parse("03 Aug 2019 23:21:42.999213988", dateTimeFormatter);
	System.out.println(dateTimeOfFormatParse);
}
/**
 * 输出结果：
 * 2017-03-22T23:52:10.999999999
 * 2019-08-03T23:21:42.999213988
 */
```

同时，`LocalDateTime` 也可以接收一个实现了 `TemporalAccessor`  接口的时间访问器：

```java
public static LocalDateTime from(TemporalAccessor temporal) {...}
```

例子：

```java
public static void main(String[] args) {
	LocalDateTime dateTime = LocalDateTime.from(ZonedDateTime.now());
	System.out.println(dateTime);
}
/**
 * 输出结果：
 * 2020-07-16T20:37:41.897717700
 */
```

上述的实例化与 `LocalDate` 和 `LocalTime` 没有什么不同，但是 `LocalDateTime` 算是 `LocalDate` 与 `LocalTime` 的合体，同时表示日期时间，因此可以使用 `LocalDate` 和 `LocalTime` 参数来获取 `LocalDateTime` 实例：

```java
public static LocalDateTime of(LocalDate date, LocalTime time) {...}
```

例子：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfDateAndTime = LocalDateTime.of(LocalDate.of(2012, 10, 1), LocalTime.of(10, 10, 10, 921710211));
	System.out.println(dateTimeOfDateAndTime);
}
/**
 * 输出结果：
 * 2012-10-01T10:10:10.921710211
 */
```

也可以通过合并日期和时间获取 `LocalDateTime` 实例：

```java
public static void main(String[] args) {
	LocalDate date = LocalDate.of(2019, 11, 2);
	LocalTime time = LocalTime.of(20, 10, 41, 123984321);
	LocalDateTime dateTimeOfMerge = date.atTime(21, 55);
	System.out.println(dateTimeOfMerge);
	dateTimeOfMerge = time.atDate(date);
	System.out.println(dateTimeOfMerge);
}
/**
 * 输出结果：
 * 2019-11-02T21:55
 * 2019-11-02T20:10:41.123984321
 */
```

## Instant

`java.time.Instant` 类是对计算机时间建模的方式，以 `Unix` 元年时间（传统的设定为UTC时区`1970-01-01T00:00:00Z`）开始所经历的秒数进行计算。它可测量的时间线被限制在 `long` 类型的存储大小中。它存储的单位为纳秒（nanos，nanos的取值范围是[0,999_999_999]）。如果早于该时间，秒数为负值，晚于该时间，秒数为正数。

`Instant` 可以通过静态方法 `ofEpochSecond` 创建该类的实例：

```java
// 创建基于新纪元 epochSecond 秒后的瞬时时间点
public static Instant ofEpochSecond(long epochSecond) {...}
// 创建基于新纪元 epochSecond 秒加上 nanoAdjustment 纳秒之后的瞬时时间点
public static Instant ofEpochSecond(long epochSecond, long nanoAdjustment) {...}
// 创建基于新纪元 epochMilli 毫秒后的瞬时时间点
public static Instant ofEpochMilli(long epochMilli) {...}
```

例子：

```java
public static void main(String[] args) {
	// 从第三秒开始的时间点
	Instant instant1 = Instant.ofEpochSecond(3);
	System.out.println(instant1);
	// 从第三秒100211321纳秒开始的时间点
	Instant instant2 = Instant.ofEpochSecond(3, 100_211_321);
	System.out.println(instant2);
	// 从第三毫秒开始的时间点
	Instant instant3 = Instant.ofEpochMilli(3);
	System.out.println(instant3);
}
/**
 * 输出结果：
 * 1970-01-01T00:00:03Z
 * 1970-01-01T00:00:03.100211321Z
 * 1970-01-01T00:00:00.003Z
 */
```

`Instant` 也支持使用跟 `LocalDateTime` 等相似的静态方法创建实例：

```java
public static Instant now() {...}
public static Instant now(Clock clock) {...}
public static Instant from(TemporalAccessor temporal) {...}
public static Instant parse(CharSequence text) {...}
```

## Duration

上述的类都实现了 `Temporal` 接口，`Temporal` 接口定义了如何读取和操纵为时间建模的对象的值。如果我们需要两个 `Temporal` 对象之间的时间段，我们就需要使用 `Duration` 来表示。`Duration` 类中有 `seconds` 与 `nanos` 两个字段，因此表示秒或纳秒的时间间隔。

```java
// 获取两个时间对象之间的持续时间
public static Duration between(Temporal startInclusive, Temporal endExclusive) {...}
```

例子：

```java
public static void main(String[] args) {
	Duration duration1 = Duration.between(LocalDateTime.of(1992, 1, 1, 21, 21, 21), LocalDateTime.of(1992, 1, 2, 21, 21, 21));
	System.out.println("两个日期时间相差的持续时间（单位：秒）：" + duration1.toSeconds());
	Duration duration2 = Duration.between(LocalTime.of(2, 2, 2, 2), LocalTime.of(2, 2, 2, 543));
	System.out.println("两个时间相差的持续时间（单位：纳秒）：" + duration2.toNanos());
	Duration duration3 = Duration.between(Instant.ofEpochSecond(2, 333), Instant.ofEpochSecond(2, 336));
	System.out.println("两个瞬时相差的持续时间（单位：纳秒）：" + duration3.toNanos());
}
/**
 * 输出结果：
 * 两个日期时间相差的持续时间（单位：秒）：86400
 * 两个时间相差的持续时间（单位：纳秒）：541
 * 两个瞬时相差的持续时间（单位：纳秒）：3
 */
```

但因为 `LocalDateTime` 和 `Instant` 是为不同的目的而设计的，所以不能混用，否则会触发 `DateTimeException` 异常：

```java
// 该例子会报异常 DateTimeException
Duration duration = Duration.between(LocalDateTime.of(1992, 1, 1, 21, 21, 21), Instant.ofEpochSecond(333));
```

也不能使用 `LocalDate` 对象做参数，因为 `Duration` 主要用于秒和纳秒的时间段间隔：

```java
Duration duration = Duration.between(LocalDate.of(1992, 1, 1), LocalDate.of(1992, 1, 1));
```

上述例子会报异常 `java.time.temporal.UnsupportedTemporalTypeException: Unsupported unit: Seconds` 支持单位为秒。

## Period

`Period` 在概念上和 `Duration` 类似，区别在于 `Period` 是以年、月或者日的方式对多个时间单位建模。

```java
// 获取两个 LocalDate 日期之间的时长
public static Period between(LocalDate startDateInclusive, LocalDate endDateExclusive) {...}
```

例子：

```java
Period period1 = Period.between(LocalDate.of(1990, 3, 5), LocalDate.of(1990, 5, 2));
```

截止目前，我们介绍的这些日期——时间对象都是不可修改的，这是为了更好地支持函数式编程。确保线程安全，保持领域模式一致性二做出的重大设计决定。

# 日期操作

Java8中的日期时间类都是不可变的，因此我们创建的 `LocalDate` 对象想要修改，就得使用 `withAttribute` 方法：

```java
public static void main(String[] args) {
        LocalDate date1 = LocalDate.of(2000, 12, 12);
        System.out.println(date1);
        LocalDate date2 = date1.withYear(2002);
        System.out.println(date2);
        LocalDate date3 = date1.withMonth(5);
        System.out.println(date3);
        LocalDate date4 = date1.withDayOfMonth(25);
        System.out.println(date4);
        LocalDate date5 = date1.with(LocalDate.now());
        System.out.println(date5);
        LocalDate date6 = date1.with(ChronoField.MONTH_OF_YEAR, 9);
        System.out.println(date6);
}
/**
 * 输出结果：
 * 2000-12-12
 * 2002-12-12
 * 2000-05-12
 * 2000-12-25
 * 2020-07-17
 * 2000-09-12
 */
```

除了修改原始日期，还可以对原有日期进行增加或者修改：

```java
public static void main(String[] args) {
	// 实例化一个LocalDate
	LocalDate date1 = LocalDate.of(2010, 6, 1);
	System.out.println(date1);
	// 对LocalDate日期添加两周并返回新副本
	LocalDate date2 = date1.plusWeeks(2);
	System.out.println(date2);
	// 对LocalDate日期减少2个月并返回新副本
	LocalDate date3 = date1.minus(Period.ofMonths(2));
	System.out.println(date3);
	// 对LocalDate日期添加6个月并返回新副本
	LocalDate date10 = date1.plus(6, ChronoUnit.MONTHS);
	System.out.println(date10);
}
/**
 * 输出结果：
 * 2010-06-01
 * 2010-06-15
 * 2010-04-01
 * 2010-12-01
 */
```

# 格式化

新的日期API提供了 `java.time.format` 包来进行格式化以及解析日期时间对象。包中最常用的就是 `DateTimeFormatter`。该类是不可变了：

```java
public final class DateTimeFormatter {...}
```

构建日期解析可以使用该类提供的静态方法与常量：

```java
public static void main(String[] args) {
	LocalDateTime dateTime = LocalDateTime.of(2000, 10, 1, 05, 30, 30, 345_431_555);
	String s1 = dateTime.format(DateTimeFormatter.BASIC_ISO_DATE);
	System.out.println(s1);
	String s2 = dateTime.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME);
	System.out.println(s2);
}
/**
 * 输出结果：
 * 20001001
 * 2000-10-01T05:30:30.345431555
 */
```

也可以通过解析日期或时间字符串重新创建日期对象。所有的日期和时间API都提供了表示时间点或者时间段的工厂方法，你可以使用 `parse` 达到重新创建该日期对象的目的：

```java
public static void main(String[] args) {
	LocalDateTime dateTimeOfParse = LocalDateTime.parse("2017-03-22T23:52:10.999999999");
	System.out.println(dateTimeOfParse);
}
/**
 * 输出结果：
 * 2017-03-22T23:52:10.999999999
 */
```

如果上述静态方法不能满足，可以考虑使用 `DateTimeFormatterBuilder` 构造 `DateTimeFormatter` 实例。如下：

```java
public static DateFormatter ofPattern(String pattern) {...}
public static DateFormatter ofPattern(String pattern, Locale locale) {...}
```

例子：

```java
public static void main(String[] args) {
	DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("dd MMM yyyy HH:mm:ss.SSSSSSSSS", Locale.ENGLISH);
	LocalDateTime dateTimeOfFormatParse = LocalDateTime.parse("03 Aug 2019 23:21:42.999213988", dateTimeFormatter);
	System.out.println(dateTimeOfFormatParse);
}
/**
 * 输出结果：
 * 2019-08-03T23:21:42.999213988
 */
```


# 时区

时区的处理是新版日期和时间API新增加的重要功能，使用新版日期和时间API时区的处理被极大地简化了。

## ZoneId

`java.time.ZoneId` 是Java8新增的处理时区的类，是老版 `java.util.TimeZone` 的替代品。

时区是按照一定的规则将区域划分成的标准时间相同的区间。在 `ZoneRules` 这个类中包含了40个这样的实例。你可以简单地通过调用 `ZoneId` 的 `getRules()` 得到指定时区的规则。每个特定的 `ZoneId` 对象都由一个地区ID标识，比如：

```java
ZoneId romeZone = ZoneId.of("Europe/Rome");
```

地区ID都为 "{区域}/{城市}" 的格式，这些地方集合的设定都由英特网编号分配机构（IANA）的时间数据库提供。你可以通过Java8的新方法 `toZoneId` 将一个老的时区对象转换为 `ZoneId`：

```java
ZoneId zoneId = TimeZone.getDefault().toZoneId();
```

一旦得到一个 `ZoneId` 对象，你就可以将它与 `LocalDate`、`LocalDateTime` 或者是 `Instant` 对象整合起来，构造为一个 `ZonedDateTime` 实例，它代表了相对于指定时区的时间点：

```java
public static void main(String[] args) {

        LocalDate date = LocalDate.of(2012, 12, 15);
        ZonedDateTime zdt1 = date.atStartOfDay(ZoneId.of("Asia/Shanghai"));
        System.out.println("LocalDate:" + date + ", 对比 ZoneDateTime:" + zdt1);

        LocalDateTime dateTime = LocalDateTime.now();
        ZonedDateTime zdt2 = dateTime.atZone(ZoneId.of("Asia/Shanghai"));
        System.out.println("LocalDateTime:" + dateTime + " ,对比 ZoneDateTime:" + zdt2);

        Instant instant = Instant.now();
        ZonedDateTime zdt3 = instant.atZone(ZoneId.of("Asia/Shanghai"));
        System.out.println("Instant:" + instant + " ,对比 ZoneDateTime:" + zdt3);
    }
/**
 * 输出结果：
 * LocalDate:2012-12-15, 对比 ZoneDateTime:2012-12-15T00:00+08:00[Asia/Shanghai]
 * LocalDateTime:2020-07-17T15:44:32.554980500 ,对比 ZoneDateTime:2020-07-17T15:44:32.554980500+08:00[Asia/Shanghai]
 * Instant:2020-07-17T07:44:32.555978200Z ,对比 ZoneDateTime:2020-07-17T15:44:32.555978200+08:00[Asia/Shanghai]
 */
```

从上面的结果可以看出：`ZonedDateTime` 由 `LocalDate`/`LocalDateTime`/`Instant` 和 `ZoneId` 两部分组成。

## ZoneOffset

另一种比较通用的表示时区的方式是使用 `ZoneOffset`，它是指定UTC/格林尼治时间的时区偏移量。

```java
// 设置偏移量
ZoneOffset offset = ZoneOffset.of("+08:00");
// 获取本地当前日期时间
LocalDateTime dateTime = LocalDateTime.now();
// LocalDateTime类中存在 `atOffset` 设置偏移量，可以返回一个 OffsetDateTime 带偏移量的日期时间
OffsetDateTime offsetDateTime = dateTime.atOffset(offset);
// 也可以使用 OffsetDateTime的静态方法 of() 来实例化
OffsetDateTime offsetDateTime1 = OffsetDateTime.of(dateTime, offset);
/**
 * LocalDateTime的格式：2020-07-17T16:59:37.209263500
 * 当设置偏移量后
 * OffsetDateTime的格式：2020-07-17T16:59:37.209263500+08:00
 */
```

# 其它日历

Java中使用的历法是 ISO 8601 日历系统，它是世界民用历法，也就是我们所说的公历。平年有365天，闰年有366天。闰年的定义是：非世纪年，能被4整除；世纪年能被400整除。为了计算的一致性，公公元1年的前一年被当做公元0年，依次类推。

此外Java8还提供了4套其它历法，每套历法都包含一个日期类，分别是：

- ThaiBuddhistDate：泰国佛教历
- MinguoDate：中华民国历
- JapaneseDate：日本历
- HijrshDate：伊斯兰历

每个日期类都继承 `ChronoLocalDate` 类，所以可以在不知道具体历法的情况下也可以操作。不过这些历法一般不常用，除非是有某些特殊需求情况下才会使用。

在实际开发过程中建议使用 `LocalDate`，包括存储、操作、业务规则的解读；除非需要将程序的输入或者输出本地化，这时可以使用 `ChronoLocalDate` 类。