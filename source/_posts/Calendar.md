---
title: Calendar
tags: java
date: 2019-05-06 17:56:57
---

Java 的Calendar，Date和DateFormat的关系图如下：
![](Calendar/1.jpg)

说明：
- milliseconds 表示毫秒。
milliseconds = “实际时间” - “1970-01-01 00:00:00”。Calendar 和 Date依赖于 milliseconds，从而表示时间。

- Calendar表示日期/时间。
它是一个抽象类，依赖于milliseconds。GregorianCalendar是Calendar的子类，通常我们通过Calendar.getInstance() 获取Calendar实例时，实际上返回的是 GregorianCalendar 对象。
Calendar和Locale关联，而Locale代表区域；Locale的值不同时，Calendar的日期/时间也不同。
Calendar和TimeZone关联，而TimeZone代表时区；不同的时区，Calendar的日期/时间也不同。

- Date 表示日期/时间。
它也依赖于 milliseconds实现。
在 JDK 1.1 之前，通常是通过Data操作“年月日时分秒”。不过，由于Date的相关 API 不易于实现国际化。从 JDK 1.1 开始，应该使用 Calendar 类来操作“年月日时分秒”，同时可以通过 DateFormat 类来格式化和解析日期字符串。Date 中的相应方法已废弃。

- DateFormat是格式化/解析“日期/时间”的工具类。
它是Date的格式化工具，它能帮助我们格式化Date，进而将Date转换成我们想要的String字符串供我们使用。
它是一个抽象类。通常，我们通过getInstance(), getDateInstance()和getDateTimeInstance() 等获取DateFormat实例时；实际上是返回的SimpleDateFormat对象。

# Calendar
`public abstract class Calendar implements Serializable, Cloneable, Comparable<Calendar> {}`
Calendar 是一个抽象类。
它的实现，采用了设计模式中的工厂方法。表现在：当我们获取Calendar实例时，Calendar会根据传入的参数来返回相应的Calendar对象。获取Calendar实例，有以下两种方式：
- 当我们通过 Calendar.getInstance() 获取日历时，默认的是返回的一个GregorianCalendar对象。
	GregorianCalendar是Calendar的一个实现类，它提供了世界上大多数国家/地区使用的标准日历系统。
- 当我们通过
Calendar.getInstance(TimeZone timezone, Locale locale)或
Calendar.getInstance(TimeZone timezone)或
Calendar.getInstance(Locale locale)获取日历时，是返回“对应时区(zone) 或 地区(local)等所使用的日历”。例如，若是日本，则返回JapaneseImperialCalendar对象。

源码如下：
```java
public static Calendar getInstance()
{
    // 调用createCalendar()创建日历
    Calendar cal = createCalendar(TimeZone.getDefaultRef(), Locale.getDefault());
    cal.sharedZone = true;
    return cal;
}


public static Calendar getInstance(TimeZone zone)
{
    // 调用createCalendar()创建日历
    return createCalendar(zone, Locale.getDefault());
}


public static Calendar getInstance(Locale aLocale) {
    // 调用createCalendar()创建日历
    Calendar cal = createCalendar(TimeZone.getDefaultRef(), aLocale);
    cal.sharedZone = true;
    return cal;
}

public static Calendar getInstance(TimeZone zone,
                   Locale aLocale)
{
    // 调用createCalendar()创建日历
    return createCalendar(zone, aLocale);
}

private static Calendar createCalendar(TimeZone zone,
                   Locale aLocale)
{
    // (01) 若地区是“th”，则返回BuddhistCalendar对象
    // (02) 若地区是“JP”，则返回JapaneseImperialCalendar对象
    if ("th".equals(aLocale.getLanguage())
        && ("TH".equals(aLocale.getCountry()))) {
        return new sun.util.BuddhistCalendar(zone, aLocale);
    } else if ("JP".equals(aLocale.getVariant())
       && "JP".equals(aLocale.getCountry())
       && "ja".equals(aLocale.getLanguage())) {
        return new JapaneseImperialCalendar(zone, aLocale);
    }        

    // (03) 否则，返回GregorianCalendar对象
    return new GregorianCalendar(zone, aLocale);    
}
```
当我们获取Calendar实例之后，就可以通过Calendar提供的一些列方法来操作日历。

# Calendar 原理和思想
我们使用Calendar，无非是操作Calendar的“年、月、日、星期、时、分、秒”这些字段。

## Calendar 各个字段值的来源
本质上，Calendar就是保存了一个时间。如下定义：
```java
// time 是当前时间，单位是毫秒。
// 它是当前时间距离“January 1, 1970, 0:00:00 GMT”的差值。
protected long time;
```
Calendar就是根据 time 计算出 “Calendar的年、月、日、星期、时、分、秒”等等信息。

## Calendar 各个字段的定义和初始化
Calendar 的“年、月、日、星期、时、分、秒”这些信息，一共是17个字段。
我们使用Calendar，无非是就是使用这17个字段。它们的定义如下：
(字段0) public final static int ERA = 0;
说明：纪元。
取值：只能为0 或 1。0表示BC(“before Christ”,即公元前)，1表示AD(拉丁语“Anno Domini”,即公元)。

(字段1) public final static int YEAR = 1;
说明：年。

(字段2) public final static int MONTH = 2;
说明：月
取值：可以为，JANUARY, FEBRUARY, MARCH, APRIL, MAY, JUNE, JULY, AUGUST, SEPTEMBER, OCTOBER, NOVEMBER, DECEMBER, UNDECIMBER。
         其中第一个月是 JANUARY，它为 0。

(字段3) public final static int WEEK_OF_YEAR = 3;
说明：当前日期在本年中对应第几个星期。一年中第一个星期的值为 1。

(字段4) public final static int WEEK_OF_MONTH = 4;
说明：当前日期在本月中对应第几个星期。一个月中第一个星期的值为 1。

(字段5) public final static int DATE = 5;
说明：日。一个月中第一天的值为 1。

(字段5) public final static int DAY_OF_MONTH = 5;
说明：同“DATE”，表示“日”。

(字段6) public final static int DAY_OF_YEAR = 6;
说明：当前日期在本年中对应第几天。一年中第一天的值为 1。

(字段7) public final static int DAY_OF_WEEK = 7;
说明：星期几。
取值：可以为，SUNDAY、MONDAY、TUESDAY、WEDNESDAY、THURSDAY、FRIDAY 和 SATURDAY。
         其中，SUNDAY为1，MONDAY为2，依次类推。

(字段8) public final static int DAY_OF_WEEK_IN_MONTH = 8;
说明：当前月中的第几个星期。
取值：DAY_OF_MONTH 1 到 7 总是对应于 DAY_OF_WEEK_IN_MONTH 1；8 到 14 总是对应于 DAY_OF_WEEK_IN_MONTH 2，依此类推。

(字段9) public final static int AM_PM = 9;
说明：上午 还是 下午
取值：可以是AM 或 PM。AM为0,表示上午；PM为1,表示下午。

(字段10) public final static int HOUR = 10;
说明：指示一天中的第几小时。
         HOUR 用于 12 小时制时钟 (0 - 11)。中午和午夜用 0 表示，不用 12 表示。

(字段11) public final static int HOUR_OF_DAY = 11;
说明：指示一天中的第几小时。
         HOUR_OF_DAY 用于 24 小时制时钟。例如，在 10:04:15.250 PM 这一时刻，HOUR_OF_DAY 为 22。

(字段12) public final static int MINUTE = 12;
说明：一小时中的第几分钟。
例如，在 10:04:15.250 PM这一时刻，MINUTE 为 4。

(字段13) public final static int SECOND = 13;
说明：一分钟中的第几秒。
例如，在 10:04:15.250 PM 这一时刻，SECOND 为 15。

(字段14) public final static int MILLISECOND = 14;
说明：一秒中的第几毫秒。
例如，在 10:04:15.250 PM 这一时刻，MILLISECOND 为 250。

(字段15) public final static int ZONE_OFFSET = 15;
说明：毫秒为单位指示距 GMT 的大致偏移量。

(字段16) public final static int DST_OFFSET = 16;
说明：毫秒为单位指示夏令时的偏移量。

public final static int FIELD_COUNT = 17;

这17个字段是保存在int数组中。定义如下：
```java
// 保存这17个字段的数组
protected int           fields[];
// 数组的定义函数
protected Calendar(TimeZone zone, Locale aLocale)
{
    // 初始化“fields数组”
    fields = new int[FIELD_COUNT];
    isSet = new boolean[FIELD_COUNT];
    stamp = new int[FIELD_COUNT];

    this.zone = zone;
    setWeekCountData(aLocale);
}
```
protected Calendar(TimeZone zone, Locale aLocale) 这是Calendar的构造函数。它会被它的子类的构造函数调用到，从而新建“保存Calendar的17个字段数据”的数组。

## Calendar各个字段值的计算
下面以get(int field)为例，简要的说明Calendar的17个字段的计算和操作。
get(int field)是获取“field”字段的值。它的定义如下：
```java
public int get(int field) {
    // 计算各个字段的值
    complete();
    // 返回field字段的值
    return internalGet(field);
}
```
说明：
get(int field)的代码很简单。先通过 complete() 计算各个字段的值，然后在通过 internalGet(field) 返回“field字段的值”。

complete() 的作用就是计算Calendar各个字段的值。它定义在Calendar.java中，代码如下：
```java
protected void complete()
{
    if (!isTimeSet)
    updateTime();
    if (!areFieldsSet || !areAllFieldsSet) {
        computeFields(); // fills in unset fields
        areAllFieldsSet = areFieldsSet = true;
    }
}
private void updateTime() {
    computeTime();
    isTimeSet = true;
}
```
updateTime() 调用到的 computeTime() 定义在 Calendar.java的实现类中。下面，我列出GregorianCalendar.java中computeTime()的实现
略...自己参考源码

下面，我们看看internalGet(field)的定义。如下：
```java
protected final int internalGet(int field) {
    return fields[field];
}
```

从中，我们就看出，get(int field) 最终是通过 internalGet(int field)来返回值的。
而 internalGet(int field) ，实际上返回的是field数组中的第field个元素。这就正好和Calendar的17个元素所对应了！

总之，我们需要了解的就是：Calendar就是以一个time(毫秒)为基数，而计算出“年月日时分秒”等，从而方便我们对“年月日时分秒”等进行操作。下面，介绍以下Calendar提供的相关操作函数。

# Calendar函数接口
Calendar的17个字段的公共接口
Calendar的这17个字段，都支持下面的公共函数接口。
这些公共接口的使用示例，请参考下方测试类CalendarTest.java 示例中的 testAllCalendarSections() 函数。

## getMaximum(int field)
作用：获取“字段的最大值”。注意“对比它和 getActualMaximum() 的区别”。
示例：以“MONTH”字段来说。使用方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 获取MONTH的最大值
int max = cal.getMaximum(Calendar.MONTH);
```
若要获取其它字段的最大值，只需要将示例中的MONTH相应的替换成其它字段名即可。


## getActualMaximum(int field)
作用：获取“当前日期下，该字段的最大值”。
示例：以“MONTH”字段来说。使用方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 获取当前MONTH的最大值
int max = cal.getActualMaximum(Calendar.MONTH);
```
若要获取其它字段的最大值，只需要将示例中的MONTH相应的替换成其它字段名即可。

注意：对比getActualMaximum() 和 getMaximum() 的区别。参考下面的对比示例，
- getMaximum() 获取的“字段最大值”，是指在综合所有的日期，在所有这些日期中得出的“字段最大值”。
      例如，getMaximum(Calendar.DATE)的目的是“获取‘日的最大值’”。综合所有的日期，得出一个月最多有31天。因此，getMaximum(Calendar.DATE)的返回值是“31”！
- getActualMaximum() 获取的“当前日期时，该字段的最大值”。
     例如，当日期为2013-09-01时，getActualMaximum(Calendar.DATE)是获取“日的最大值”是“30”。当前日期是9月份，而9月只有30天。因此，getActualMaximum(Calendar.DATE)的返回值是“30”！


## getMinimum(int field)
作用：获取“字段的最小值”。注意“对比它和 getActualMinimum() 的区别”。
示例：以“MONTH”字段来说。使用方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 获取MONTH的最小值
int min = cal.getMinimum(Calendar.MONTH);
```
若要获取其它字段的最小值，只需要将示例中的MONTH相应的替换成其它字段名即可。


## getActualMinimum(int field)
作用：获取“当前日期下，该字段的最小值”。
示例：以“MONTH”字段来说。使用方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 获取MONTH的最小值
int min = cal.getMinimum(Calendar.MONTH);
```
若要获取其它字段的最小值，只需要将示例中的MONTH相应的替换成其它字段名即可。
注意：在Java默认的Calendar中，虽然 getMinimum() 和 getActualMinimum() 的含义不同；但是，它们的返回值是一样的。因为Calendar的默认是返回GregorianCalendar对象，而在GregorianCalendar.java中，getMinimum() 和 getActualMinimum() 返回值一样。


## get(int field)
作用：获取“字段的当前值”。获取field字段的当前值。
示例：以“MONTH”字段来说。“获取MONTH的当前值”的方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 获取“cal日历”的当前MONTH值
int MONTH = cal.get(Calendar.MONTH);
```
若要获取其它字段的当前值，只需要将示例中的MONTH相应的替换成其它字段名即可。


## set(int field, int value)
作用：设置“字段的当前值”。设置field字段的当前值为value
示例：以“MONTH”字段来说。“设置MONTH的当前值”的方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 设置“cal日历”的当前MONTH值为 1988年
cal.set(Calendar.MONTH, 1988);
```
说明：
- 1988是想要设置的MONTH的当前值。这个设置值必须是整数。
- 若要设置其它字段的当前值，只需要将示例中的MONTH相应的替换成其它字段名即可。


## add(int field, int value)
作用：给“字段的当前值”添加值。给field字段的当前值添加value。
示例：以“MONTH”字段来说。方法如下：
```java
// 获取Calendar实例，并设置日期为“2013-09-01”
Calendar cal = Calendar.getInstance();
cal.set(Calendar.YEAR, 2013);
cal.set(Calendar.MONTH, 8);
cal.set(Calendar.DATE, 1);
// 给“cal日历”的当前MONTH值 “添加-10”
cal.add(Calendar.MONTH, -10);
```
说明：
- `-10`是添加值。
	添加值可以为正数，也可以是负数。
	正数表示将日期增加，负数表示将日期减少。

	假设：现在cal的值是“2013-09-01”，现在我们将MONTH字段值增加-10。得到的结果是：“2012-10-01”。
	为什么会这样呢？“2013-09-01”增加-10，也就是将日期向前减少10个月；得到的结果就是“2012-10-01”。
- Calendar的17个字段中：除了回滚Calendar.ZONE_OFFSET时，会抛出IllegalArgumentException异常；其它的字段都支持该操作。
- 若要设置其它字段的当前值，只需要将示例中的MONTH相应的替换成其它字段名即可。

## roll(int field, int value)
作用：回滚“字段的当前值”
示例：以“MONTH”字段来说。“回滚MONTH的当前值”的方法为：
```java
// 获取Calendar实例，并设置日期为“2013-09-01”
Calendar cal = Calendar.getInstance();
cal.set(Calendar.YEAR, 2013);
cal.set(Calendar.MONTH, 8);
cal.set(Calendar.DATE, 1);
// 将“cal日历”的当前MONTH值 “向前滚动10”
cal.roll(Calendar.MONTH, -10);
```
说明：
- `-10`是回滚值。
	当回滚值是负数时，表示将当前字段向前滚；
	当回滚值是正数时，表示将当前字段向后滚。

	回滚Calendar中某一字段时，不更改更大的字段！
	这是roll()与add()的根据区别！add()可能会更改更大字段，比如“使用add()修改‘MONTH’字段，可能会引起‘YEAR’字段的改变”；但是roll()不会更改更大的字段，例如“使用roll()修改‘MONTH’字段，不回引起‘YEAR’字段的改变。”

	假设：现在cal的值是“2013-09-01”，现在我们将MONTH字段值增加-10。得到的结果是：“2013-10-01”。
	为什么会这样呢？这就是因为“回滚”就是“在最小值和最大值之间来回滚动”。本例中，MONTH是9月，前回滚10，得到的值是10月，但是roll()不会改变“比MONTH”更大的字段，所以YEAR字段不会改变。所以结果是“2013-10-01”。
- Calendar的17个字段中：除了回滚Calendar.ZONE_OFFSET时，会抛出IllegalArgumentException异常；其它的字段都支持该操作。
- 若要设置其它字段的当前值，只需要将示例中的MONTH相应的替换成其它字段名即可。

## clear(int field)
作用：清空“字段的当前值”。所谓清空，实际上是将“field”的值设置为0；若field最小值为1,则设置为1。
示例：以“MONTH”字段来说。“清空MONTH”的方法为：
```java
// 获取Calendar实例，并设置日期为“9月”
Calendar cal = Calendar.getInstance();
cal.set(Calendar.MONTH, 9);
// 清空MONTH
cal.clear(Calendar.MONTH);
```
若要清空其它字段，只需要将示例中的MONTH相应的替换成其它字段名即可。

## isSet(int field)
作用：判断“字段field”是否被设置。若调用clear()清空之后，则field变为“没有设置状态”。
示例：以“MONTH”字段来说。“判断MONTH是否被设置”的方法为：
```java
// 获取Calendar实例
Calendar cal = Calendar.getInstance();
// 判断MONTH是否被设置
boolean bset = cal.isSet(Calendar.MONTH);
```
若要判断其它字段，只需要将示例中的MONTH相应的替换成其它字段名即可。
 
## Calendar的其它函数
### 日期比较函数
Calendar的比较函数，主要有以下几个：
```java
// 比较“当前Calendar对象”和“calendar” 的日期、时区等内容是否相等。
boolean equals(Object object)
// 当前Calendar对象 是否 早于calendar
boolean before(Object calendar)
// 当前Calendar对象 是否 晚于calendar
boolean after(Object calendar)
// 比较“当前Calendar对象”和“calendar”。
// 若 早于 “calendar” 则，返回-1
// 若 相等， 则，返回0
// 若 晚于 “calendar” 则，返回1
int compareTo(Calendar anotherCalendar)
```
这些函数的使用示例，请参考CalendarTest.java示例中的 testComparatorAPIs()函数。

示例：假设cal1 和 cal2 都是Calendar的两个对象。
```java
// 它们的使用方法如下
boolean isEqual = cal1.equals(cal2);
boolean isBefore = cal1.before(cal2);
boolean isAfter = cal1.after(cal2);
int icompare = cal1.compareTo(cal2);
```

### “宽容”函数
```java
// 设置“Calendar的宽容度”
void setLenient(boolean value)
// 获取“Calendar的宽容度”
boolean isLenient()
```
这些函数的使用示例，请参考CalendarTest.java示例中的 testLenientAPIs() 函数。
说明： 
Calendar 有两种解释日历字段的模式，即 lenient 和 non-lenient。
- 当 Calendar 处于 lenient 模式时，它可接受比它所生成的日历字段范围更大范围内的值。当 Calendar 重新计算日历字段值，以便由 get() 返回这些值时，所有日历字段都被标准化。
	例如，lenient 模式下的 GregorianCalendar 将 MONTH == JANUARY、DAY_OF_MONTH == 32 解释为 February 1。
- 当 Calendar 处于 non-lenient 模式时，如果其日历字段中存在任何不一致性，它都会抛出一个异常。
	例如，GregorianCalendar 总是在 1 与月份的长度之间生成 DAY_OF_MONTH 值。如果已经设置了任何超出范围的字段值，那么在计算时间或日历字段值时，处于 non-lenient 模式下的 GregorianCalendar 会抛出一个异常。
    注意：在(02)步骤中的异常，在使用set()时不会抛出，而需要在使用get()、getTimeInMillis()、getTime()、add() 和 roll() 等函数中才抛出。因为set()只是设置了一个修改标志，而get()等方法才会引起时间的重新计算，此时才会抛出异常！

### "年月日(时分秒)"、Date、TimeZone、MilliSecond函数
```java
// 设置“年月日”
final void     set(int year, int month, int day)
// 设置“年月日时分”
final void     set(int year, int month, int day, int hourOfDay, int minute, int second)
// 设置“年月日时分秒”
final void     set(int year, int month, int day, int hourOfDay, int minute)
// 获取Calendar对应的日期
final Date     getTime()
// 设置Calendar为date
final void    setTime(Date date)
// 获取Calendar对应的时区
TimeZone     getTimeZone()
// 设置Calendar对应的时区
void     setTimeZone(TimeZone timezone)
// 获取Calendar对应的milliscondes值，就是“Calendar当前日期”距离“1970-01-01 0:00:00 GMT”的毫秒数
long     getTimeInMillis()
// 设置Calendar对应的milliscondes值
void     setTimeInMillis(long milliseconds)
```
这些函数的使用示例，请参考CalendarTest.java示例中的 testTimeAPIs() 函数。

### 其它操作
```java
// 克隆Calendar
Object clone()
// 获取“每周的第一天是星期几”。例如，在美国，这一天是 SUNDAY，而在法国，这一天是 MONDAY。
int getFirstDayOfWeek()
// 设置“每周的第一天是星期几”。例如，在美国，这一天是 SUNDAY，而在法国，这一天是 MONDAY。
void setFirstDayOfWeek(int value)
// 获取一年中第一个星期所需的最少天数，例如，如果定义第一个星期包含一年第一个月的第一天，则此方法将返回 1。如果最少天数必须是一整个星期，则此方法将返回 7。
int getMinimalDaysInFirstWeek()
// 设置一年中第一个星期所需的最少天数，例如，如果定义第一个星期包含一年第一个月的第一天，则使用值 1 调用此方法。如果最少天数必须是一整个星期，则使用值 7 调用此方法。
void setMinimalDaysInFirstWeek(int value)
```
这些函数的使用示例，请参考CalendarTest.java示例中的 testOtherAPIs() 函数。

# Calendar操作示例
下面，我们通过示例学习使用Calendar的API。CalendarTest.java的源码如下：
```java
import java.util.Date;
import java.util.Calendar;
import java.util.TimeZone;
import java.util.Random;

/**
 * Calendar的API测试程序
 *
 * @author skywang 
 * @email kuiwu-wang@163.com
 */
public class CalendarTest {

    public static void main(String[] args) {

        // 测试Calendar的“17个字段的公共函数接口”
        testAllCalendarSections() ;

        // 测试Calendar的“比较接口”
        testComparatorAPIs() ;

        // 测试Calendar的“比较接口”
        testLenientAPIs() ;

        // 测试Calendar的Date、TimeZone、MilliSecond等相关函数
        testTimeAPIs() ;

        // 测试Calendar的clone()，getFirstDayOfWeek()等接口
        testOtherAPIs() ;

    }


    /**
     * 测试“Calendar的字段”
     *
     * @param cal   -- Calendar对象
     * @param field -- 要测试的“Calendar字段”。可以为以下值：
     *   Calendar.YEAR, Calendar.MONTH, Calendar.DATE, ... 等等
     * @param title -- 标题
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testSection(Calendar cal, int field, String title) {
        final Random random = new Random();
        final Date date = cal.getTime();

        final int min = cal.getMinimum(field);    // 获取"字段最小值"
        final int max = cal.getMaximum(field);    // 获取“字段最大值”

        final int actualMin = cal.getActualMinimum(field);    // 获取"当前日期下，该字段最小值"
        final int actualMax = cal.getActualMaximum(field);    // 获取“当前日期下，该字段的最大值”

        // 获取“字段的当前值”
        final int ori = cal.get(field);            

        // 设置“字段的当前值”, 并获取“设置之后的值”
        final int r1 = random.nextInt(max);
        cal.set(field, r1);                
        final int set = cal.get(field);            
        try {
            // 回滚“字段的当前值”：在“字段最小值”和“字段最大值”之间回滚。
            // “回滚值”可以为正，也可以为负。
            cal.roll(field, -max);            
        } catch (IllegalArgumentException e) {
            // 当field == Calendar.ZONE_OFFSET时，会抛出该异常！
            e.printStackTrace();
        }
        final int roll = cal.get(field);            

        // 获取一个随机值
        final int sign = ( random.nextInt(2) == 1) ? 1 : -1;
        final int r2 = sign * random.nextInt(max);
        try {
            // 增加“字段的当前值” ，并获取“新的当前字段值”
            // add的“参数值”可以为正，也可以为负。
            cal.add(field, r2);            
        } catch (IllegalArgumentException e) {
            // 当field == Calendar.ZONE_OFFSET时，会抛出该异常！
            e.printStackTrace();
        }
        final int add = cal.get(field);



        // 打印字段信息
        System.out.printf("%s:\n\trange is [%d - %d] actualRange is [%d - %d].  original=%d, set(%d)=%d, roll(%d)=%d, add(%d)=%d\n",
               title, min, max, actualMin, actualMax, ori, r1, set, -max, roll, r2, add);
    }

    /**
     * 测试Calendar的“17个字段的公共函数接口”
     *
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testAllCalendarSections() {
        // 00. ERA 字段
        testSection(Calendar.getInstance(), Calendar.ERA, "Calendar.ERA");
        // 01. YEAR 字段
        testSection(Calendar.getInstance(), Calendar.YEAR, "Calendar.YEAR");
        // 02. MONTH 字段
        testSection(Calendar.getInstance(), Calendar.MONTH, "Calendar.MONTH");
        // 03. WEEK_OF_YEAR 字段
        testSection(Calendar.getInstance(), Calendar.WEEK_OF_YEAR, "Calendar.WEEK_OF_YEAR");
        // 04. WEEK_OF_MONTH 字段
        testSection(Calendar.getInstance(), Calendar.WEEK_OF_MONTH, "Calendar.WEEK_OF_MONTH");
        // 05. DATE 字段
        testSection(Calendar.getInstance(), Calendar.DATE, "Calendar.DATE");
        // 06. DAY_OF_MONTH 字段
        testSection(Calendar.getInstance(), Calendar.DAY_OF_MONTH, "Calendar.DAY_OF_MONTH");
        // 07. DAY_OF_YEAR 字段
        testSection(Calendar.getInstance(), Calendar.DAY_OF_YEAR, "Calendar.DAY_OF_YEAR");
        // 08. DAY_OF_WEEK 字段
        testSection(Calendar.getInstance(), Calendar.DAY_OF_WEEK, "Calendar.DAY_OF_WEEK");
        // 09. DAY_OF_WEEK_IN_MONTH 字段
        testSection(Calendar.getInstance(), Calendar.DAY_OF_WEEK_IN_MONTH, "Calendar.DAY_OF_WEEK_IN_MONTH");
        // 10. AM_PM 字段
        testSection(Calendar.getInstance(), Calendar.AM_PM, "Calendar.AM_PM");
        // 11. HOUR 字段
        testSection(Calendar.getInstance(), Calendar.HOUR, "Calendar.HOUR");
        // 12. HOUR_OF_DAY 字段
        testSection(Calendar.getInstance(), Calendar.HOUR_OF_DAY, "Calendar.HOUR_OF_DAY");
        // 13. MINUTE 字段
        testSection(Calendar.getInstance(), Calendar.MINUTE, "Calendar.MINUTE");
        // 14. SECOND 字段
        testSection(Calendar.getInstance(), Calendar.SECOND, "Calendar.SECOND");
        // 15. MILLISECOND 字段
        testSection(Calendar.getInstance(), Calendar.MILLISECOND, "Calendar.MILLISECOND");
        // 16. ZONE_OFFSET 字段
        testSection(Calendar.getInstance(), Calendar.ZONE_OFFSET, "Calendar.ZONE_OFFSET");
    }

    /**
     * 测试Calendar的“比较接口”
     *
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testComparatorAPIs() {
        // 新建cal1 ，且时间为1988年
        Calendar cal1 = Calendar.getInstance();
        cal1.set(Calendar.YEAR, 1988);
        // 新建cal2 ，且时间为2000年
        Calendar cal2 = Calendar.getInstance();
        cal2.set(Calendar.YEAR, 2000);
        // 新建cal3, 为cal1的克隆对象
        Calendar cal3 = (Calendar)cal1.clone();

        // equals 判断 cal1和cal2的“时间、时区等”内容是否相等
        boolean isEqual12 = cal1.equals(cal2);
        // equals 判断 cal1和cal3的“时间、时区等”内容是否相等
        boolean isEqual13 = cal1.equals(cal3);
        // cal1是否比cal2早
        boolean isBefore = cal1.before(cal2);
        // cal1是否比cal2晚
        boolean isAfter = cal1.after(cal2);
        // 比较cal1和cal2
        // (01) 若cal1 早于 cal2，返回-1
        // (02) 若cal1 等于 cal2，返回0
        // (03) 若cal1 晚于 cal2，返回1
        int icompare = cal1.compareTo(cal2);
        
        System.out.printf("\ntestComparatorAPIs: isEuqal12=%s, isEqual13=%s, isBefore=%s, isAfter=%s, icompare=%s\n",
               isEqual12, isEqual13, isBefore, isAfter, icompare);
    }

    /**
     * 测试Calendar的“比较接口”
     *
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testLenientAPIs() {
            Calendar cal = Calendar.getInstance();

            // 获取默认的“宽容度”。返回true
            boolean oriLenient = cal.isLenient();
            // MONTH值只能是“0-11”，这里越界。但是由于当前cal是宽容的，所以不会抛出异常
            cal.set(Calendar.MONTH, 50);    

            // 设置“宽容度”为false。
            cal.setLenient(false);
            // 获取设置后的“宽容度”
            boolean curLenient = cal.isLenient();
            try {
            // MONTH值只能是“0-11”，这里越界。而且当前cal是不宽容的，所以会产生异常。
            // 但是，异常到下次计算日期时才会抛出。即，set()中不回抛出异常，而要等到get()中才会抛出异常
            cal.set(Calendar.MONTH, 50);
            // 此时，对cal进行读取。读取会导致重新计算cal的值，所以此时抛出异常！
            int m2 = cal.get(Calendar.MONTH);    
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        }

        System.out.printf("\ntestLenientAPIs: oriLenient=%s, curLenient=%s\n",
               oriLenient, curLenient);
    }

    /**
     * 测试Calendar的Date、TimeZone、MilliSecond等相关函数
     *
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testTimeAPIs() {
        Calendar cal = Calendar.getInstance();

        // 设置cal的时区为“GMT+8”
        cal.setTimeZone(TimeZone.getTimeZone("GMT+8"));
        // 获取当前的cal时区
        TimeZone timezone = cal.getTimeZone();

        // 设置 milliseconds
        cal.setTimeInMillis(1279419645742l);
        // 获取 milliseconds
        long millis = cal.getTimeInMillis();
        // 设置 milliseconds之后，时间也改变了。
        // 获取cal对应的日期
        Date date = cal.getTime();

        // 设置时间为“1988-08-08”
        cal.set(1988, 08, 08);
        // 设置时间为“1999-09-09 09:09”
        cal.set(1999, 09, 09, 9, 9);
        // 设置时间为“2000-10-10 10:10:10”
        cal.set(2000, 10, 10, 10, 10, 10);

        System.out.printf("\ntestTimeAPIs: date=%s, timezone=%s, millis=%s\n",
               date, timezone, millis);
    }

    /**
     * 测试Calendar的clone()，getFirstDayOfWeek()等接口
     *
     * @author skywang (kuiwu-wang@163.com)
     */
    private static void testOtherAPIs() {
        Calendar cal = Calendar.getInstance();
        // 克隆cal
        Calendar clone = (Calendar)cal.clone();

        // 设置 为 2013-01-10。 
        // 注：2013-01-01 为“星期二”，2013-01-06为“星期天”，
        clone.set(Calendar.YEAR, 2013);
        clone.set(Calendar.MONTH, 0);
        clone.set(Calendar.DATE, 10);
        // 设置“本年的第一个星期最少包含1天”。
        // 则2013-01-10属于第2个星期
        clone.setMinimalDaysInFirstWeek(1);
        int m1 = clone.getMinimalDaysInFirstWeek();
        int index1 = clone.get(Calendar.WEEK_OF_YEAR);

        // 设置“本年的第一个星期最少包含7天”。
        // 则2013-01-10属于第1个星期
        clone.setMinimalDaysInFirstWeek(7);
        int m2 = clone.getMinimalDaysInFirstWeek();
        int index2 = clone.get(Calendar.WEEK_OF_YEAR);

        // 设置“每周的第一天是星期几”。
        clone.setFirstDayOfWeek(Calendar.WEDNESDAY);
        // 获取“每周的第一天是星期几”。
        int firstdayOfWeek = clone.getFirstDayOfWeek();

        System.out.printf("\ntestOtherAPIs: firstdayOfWeek=%s, [minimalDay, WeekOfYear]={(%s, %s), (%s, %s)} %s\n",
               firstdayOfWeek, m1, index1, m2, index2, clone.getTime());
    }
}
```
