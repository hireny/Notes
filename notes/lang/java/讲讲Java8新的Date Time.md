# 前言

在Java 8中， 整合了许多 `Joda-Time` 的特性而开发的 `java.time` 支持全新的日期和时间API。Date-Time API 由主包 `java.time` 和四个子包组成：

|包名|描述|
|:---:|:---:|
|java.time|日期，时间，瞬间和持续时间的主要API。基于ISO-8601中定义的日历系统，并且不可变且线程安全|
|java.time.chrono|除默认ISO之外的日历系统的API|
|java.time.format|格式化和解析日期和时间的类|
|java.time.temporal|扩展API，允许日期和时间类之间互操作，查询和调整|
|java.time.zone|支持时区、时区的偏移以及时区规则|

下面我们一起探索新的日期和时间API所提供的新特性。

# 日期时间类

日期时间API提供四个专门处理日期信息的类，不考虑时间或时区。

## LocalDate

从 `LocalDate` 类的声明中可以看出：

```java
public final class LocalDate
        implements Temporal, TemporalAdjuster, ChronoLocalDate, Serializable
```

`LocalDate` 被 `final` 修饰，是一个不可变的日期对象。它是 `ISO-8601` 日历系统的实现。`LocalDate`类提供简单的日期，并不包含时区与时间信息。

`LocalDate` 可以使用 `now()`、`now(ZoneId zone)` 和 `now(Clock clock)` 三个静态方法来获取当前日期：

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
/**
 * 输出结果：
 * 2020-07-15
 * 2020-07-15
 * 2020-07-14
 */
```

由此可见，我们设置了时区后的输出是当前时区的日期，但是不显示时区等信息。除了当前日期，还可以使用静态方法 `of()` 创建`LocalDate`实例：

```java
public static void main(String[] args) {
	// 根据传入的年月日获取LocalDate实例（月份是Java封装的枚举类）
	LocalDate date1 = LocalDate.of(1997, Month.MAY, 31);
	System.out.println(date1);
	// 根据传入的年月日获取LocalDate实例（月份是自己输入的数字）
	LocalDate date2 = LocalDate.of(1997, 5, 31);
	System.out.println(date2);
}
/**
 * 输出结果：
 * 1997-05-31
 * 1997-05-31
 */
```

输出结果一样，但是参数一个是枚举，一个是数字，这是因为 `of(int year, Month month, int dayOfMonth)` 实现中，还是会把 `Month` 枚举转换为月份的数字。

创建了 `LocalDate` 实例后，我们可以根据该实例获取年份、月份、星期等日期信息。

```java
// 根据传入的时间字段获取年月日等信息
public int get(TemporalField field); -> 例子： date.get(ChronoField.YEAR);
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

也可以看出，`LocalTime` 也是被 `final` 修饰的不可变的时间对象，`LocalTime` 可以表示 `hh:mm:ss.SSSZ`。时间精度可以表示到纳秒。

`LocalTime` 也有获取当前时间的静态方法：

```java
public static void main(String[] args) {
	// 使用时钟的默认时区获取当前LocalTime实例
	LocalTime timeOfTime = LocalTime.now();
	System.out.println(timeOfTime);
	// 获取指定时区的当前LocalTime实例
	LocalTime timeOfZone = LocalTime.now(ZoneId.of("Asia/Shanghai"));
	System.out.println(timeOfZone);
	// 获取指定时钟的当前LocalTime实例
	LocalTime timeOfClock = LocalTime.now(Clock.systemUTC());
	System.out.println(timeOfClock);
}
/**
 * 输出结果：
 * 23:52:00.842133400
 * 23:52:00.843155500
 * 15:52:00.843155500
 */
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

创建了 `LocalTime` 实例后，我们也可以获取该实例小时、分钟、描述和纳秒等信息。

```java
// 根据传入的时间字段获取小时、分钟、秒等信息
public int get(ChronoField field); -> 例子;time.get(ChronoField.HOUR_OF_DAY)
// 获取小时
public int getHour();
// 获取分钟
public int getMinute();
// 获取秒数
public int getSecond();
// 获取纳秒数
public int getNano();
```

`LocalTime` 类还有很多方法，就不一一列举了。

## LocalDateTime

查看 `LocalDateTime` 的声明：

```java
public final class LocalDateTime
        implements Temporal, TemporalAdjuster, ChronoLocalDateTime<LocalDate>, Serializable
```

就知道 `LocalDateTime` 以上两个表示日期与时间的类都是被 `final` 修饰的不可变的时间对象。它同时表示日期和时间，但不带有时区信息，你可以通过 `now()` 与指定时区、时钟来创建当前日期时间：

```java
public static void main(String[] args) {
	// 使用时钟的默认时区获取LocalDateTime实例
	LocalDateTime dateTimeOfNow = LocalDateTime.now();
	System.out.println(dateTimeOfNow);
	// 获取指定时区的当前LocalDateTime实例
	LocalDateTime dateTimeOfZone = LocalDateTime.now(ZoneId.of("Asia/Shanghai"));
	System.out.println(dateTimeOfZone);
	// 获取指定时钟的当前LocalDateTime实例
	LocalDateTime dateTimeOfClock = LocalDateTime.now(Clock.systemUTC());
	System.out.println(dateTimeOfClock);
}
/**
 * 输出结果：
 * 2020-07-16T18:16:13.773716400
 * 2020-07-16T18:16:13.774713600
 * 2020-07-16T10:16:13.774713600
 */
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

<br/>

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

<br/>

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

<br/>

## TemporalAdjuster

当需要进行更加复杂的操作的时候，我们可以使用 `with` 方法，向其传递一个提供了更多定制化选择的 `TemporalAdjuster` 对象，更加灵活地处理日期。对于最常见的用例，Date-Time API已经提供了大量预定义的 `TemporalAdjuster`。你可以通过 `TemporalAdjusters` 类的静态工厂方法访问它们。

```java
public static void main(String[] args) {
        LocalDate date = LocalDate.of(2019, 03, 05);
        DayOfWeek dotw = date.getDayOfWeek();   // 获取当天是周几
        System.out.println("当前年月日：" + date + "，周：" + dotw);

        // 获取当月第一天
        LocalDate date1 = date.with(TemporalAdjusters.firstDayOfMonth());
        System.out.println("当月第一天：" + date1);
        // 获取当月最后一天
        LocalDate date2 = date.with(TemporalAdjusters.lastDayOfMonth());
        System.out.println("当月最后一天：" + date2);
        // 获取下个月第一天
        LocalDate date3 = date.with(TemporalAdjusters.firstDayOfNextMonth());
        System.out.println("下个月第一天：" + date3);
        // 获取当前年份第一天
        LocalDate date4 = date.with(TemporalAdjusters.firstDayOfYear());
        System.out.println("当前年份第一天：" + date4);
        // 获取当前年份最后一天
        LocalDate date5 = date.with(TemporalAdjusters.lastDayOfYear());
        System.out.println("当前年份最后一天：" + date5);
        // 获取下一年第一天
        LocalDate date6 = date.with(TemporalAdjusters.firstDayOfNextYear());
        System.out.println("下一年第一天：" + date6);
        // 获取当月的第一个星期一（DayOfWeek.MONDAY 枚举是星期一）
        LocalDate date7 = date.with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY));
        System.out.println("当月第一个星期一：" + date7);
        // 获取当月的最后一个星期一
        LocalDate date8 = date.with(TemporalAdjusters.lastInMonth(DayOfWeek.MONDAY));
        System.out.println("当月最后一个星期一：" + date8);
        // 对应当月的第 ordinal 个星期一
        LocalDate date9 = date.with(TemporalAdjusters.dayOfWeekInMonth(2, DayOfWeek.MONDAY));
        System.out.println("当月第二个星期一：" + date9);
        // 下一个星期一（相对一当前时间的下一个星期一）
        LocalDate date10 = date.with(TemporalAdjusters.next(DayOfWeek.MONDAY));
        System.out.println("下一个星期一：" + date10);
        // 对应下一个星期二，如果当前日期时间对象满足dayOfWeek，则返回自身
        LocalDate date11 = date.with(TemporalAdjusters.nextOrSame(DayOfWeek.TUESDAY));
        System.out.println("下一个星期二，如果当前日期满足，则返回自身：" + date11);
        // 对应上一个星期一
        LocalDate date12 = date.with(TemporalAdjusters.previous(DayOfWeek.MONDAY));
        System.out.println("上一个星期一：" + date12);
        // 对应上一个星期一，如果当前日期时间满足对象dayOfWeek，则返回自身
        LocalDate date13 = date.with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY));
        System.out.println("上一个星期一，如果当前日期满足，则返回自身：" + date13);
}
/**
 * 输出结果：
 * 当前年月日：2019-03-05，周：TUESDAY
 * 当月第一天：2019-03-01
 * 当月最后一天：2019-03-31
 * 下个月第一天：2019-04-01
 * 当前年份第一天：2019-01-01
 * 当前年份最后一天：2019-12-31
 * 下一年第一天：2020-01-01
 * 当月第一个星期一：2019-03-04
 * 当月最后一个星期一：2019-03-25
 * 当月第二个星期一：2019-03-11
 * 下一个星期一：2019-03-11
 * 下一个星期二，如果当前日期满足，则返回自身：2019-03-05
 * 上一个星期一：2019-03-04
 * 上一个星期一，如果当前日期满足，则返回自身：2019-03-04
 */
```

方法非常直观。如果没有符合你要求的预定义的 `TemporalAdjuster`，则自己实现 `TemporalAdjuster` 接口。实际上，`TemporalAdjuster` 接口只声明了单一的一个方法，定义如下：

```java
@FunctionalInterface
public interface TemporalAdjuster {
	Temporal adjustInto(Temporal temporal);
}
```

因此，我们实现一个计算下一个工作日（不包括周末）的 `TemporalAdjuster`类：

```java
public class NextWorkingDay implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal temporal) {
        DayOfWeek dow = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK));
        int dayToAdd = 1;
        if (dow == DayOfWeek.FRIDAY) {
            // 如果当天是周五，则增加三天
            dayToAdd = 3;
        } else if (dow == DayOfWeek.SATURDAY) {
            // 如果当天是周六，则增加两天
            dayToAdd = 2;
        }
        // 增加恰当的天数后，返回修改的日期
        return temporal.plus(dayToAdd, ChronoUnit.DAYS);
    }
}
// 方法调用
public static void main(String[] args) {
	LocalDate date = LocalDate.of(2019, 03, 05);
	DayOfWeek dotw = date.getDayOfWeek();   // 获取当天是周几
	System.out.println("当前年月日：" + date + "，周：" + dotw);
	LocalDate date14 = date.with(new NextWorkingDay());
	System.out.println("下一个工作日：" + date14);
}
/**
 * 输出结果：
 * 当前年月日：2019-03-05，周：TUESDAY
 * 下一个工作日：2020-07-20
 */
```

你也可以使用Lambda表达式定义 `TemporalAdjuster` 对象：

```java
date.with(TemporalAdjusters.ofDateAdjuster(temporal -> {
		DayOfWeek dow = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK));
		int dayToAdd = 1;
		if (dow == DayOfWeek.FRIDAY) {
			dayToAdd = 3;
		} else if (dow == DayOfWeek.SATURDAY) {
			dayToAdd = 2;
		}
		return temporal.plus(dayToAdd, ChronoUnit.DAYS);
}));
```

<br/>


# 解析与格式化

新的日期API提供了 `java.time.format` 包来进行格式化以及解析日期时间对象。包中最常用的就是 `DateTimeFormatter`。该类是不可变了：

```java
public final class DateTimeFormatter
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

<br/>

# 时区

时区的处理是新版日期和时间API新增加的重要功能，每个时区都有一个标识符来描述，并通常具有地区/城市（亚洲/上海）和格林尼治/UTC时间的偏移量。

在Date-Time API中处理时区和偏移量的是 `Zoneid` 和 `ZoneOffset`。

## ZoneId

`java.time.ZoneId` 是Java8新增的处理时区的类，是老版 `java.util.TimeZone` 的替代品。

时区是按照一定的规则将区域划分成的标准时间相同的区间。

使用 `ZoneId` 的静态方法 `getAvailableZoneIds()` 可以获取所有可用的时区。也可以使用 `of()` 指定一个时区：

```java
ZoneId romeZone = ZoneId.of("Asia/Shanghai");
```

也可以通过Java8在 `TimeZone` 重新加入的方法 `toZoneId` 来将老的时区对象转换为 `ZoneId`：

```java
ZoneId zoneId = TimeZone.getDefault().toZoneId();
```

<br/>

## ZoneOffset

另一种比较通用的表示时区的方式是使用 `ZoneOffset`，它是指定UTC/格林尼治时间的时区偏移量。

```java
// 设置偏移量
ZoneOffset offset = ZoneOffset.of("+08:00");
System.out.println(offset);
/**
 * 输出结果：
 * +08:00
 */
```

<br/>

## 日期-时间类

Date-Time API 也提供了带有时区或偏移量的日期时间类来配合 `ZoneId` 与 `ZoneOffset` 工作：

- `ZoneDateTime`：带有UTC的时区偏移量与相应时区的日期和时间。
- `OffsetDateTime`：带有UTC的时区偏移量的日期和时间，不包含时区ID。
- `OffsetTime`：带有UTC的时区偏移量的时间，不包含时区ID。

### ZonedDateTime

`ZonedDateTime` 可以理解为是 `LocalDateTime`、`ZoneOffset`和 `ZoneId` 的结合。用于表示具体时区的完整日期和时间。

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

### OffsetDateTime

`OffsetDateTime` 可以理解为是 `LocalDateTime`、`ZoneOffset` 的结合。用于表示UTC时间的偏移量的整个日期和时间。

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

### OffsetTime

`OffsetTime` 可以理解为是 `LocalTime`、`ZoneOffset` 的结合。用于表示UTC时间的偏移量的整个时间。

```java
OffsetTime offsetTime = OffsetTime.of(LocalTime.now(), ZoneOffset.of("+08:00"));
System.out.println(offsetTime);
offsetTime = OffsetTime.ofInstant(Instant.now(), ZoneOffset.UTC);
System.out.println(offsetTime);
/**
 * 输出结果：
 * 02:14:59.432481800+08:00
 * 18:14:59.433480700Z
 */
```

学习完时区后，我们再返回去看 Date-Time API，可以理解它们之间的关系：

![关系图](./images/Date-Time API 关系图.png)

<br/>

# 总结

Java8的新日期时间类库 `java.time` 的设计相比旧的日期时间类库而言，有很大的好处：

1. 新版日期类库都是不可变类且是线程安全的；
2. 新的API提供了两种不同的时间表示方式，有效地区分了运行时人和机器的不同需求； 
3. 类的职责更加明确且易扩展；

其它非 ISO 日历系统可以使用 `java.time.chrono` 包来表示。Java8给提供了几套历法如 `MinguoDate` 等，如有兴趣可以访问官方文档。