---
title: Date
tags: java
date: 2019-05-07 10:42:11
---
# Date介绍
Date 定义
`public class Date implements java.io.Serializable, Cloneable, Comparable<Date> {}`

Date 是表示时间的类。
一个Date对象表示一个特定的瞬间，能精确到毫秒。我们可以通过这个特定的瞬间，来获取到Date对应的“年、月、日、时、分、秒”。反之亦然，我们也可以通过设置Date的“年、月、日、时、分、秒”等信息，来改变Date所指定的特定瞬间。
除了“年月日时分秒”等信息之外，Data也允许格式化和解析日期字符串。即，我们可以定义一个字符串，这个字符串包含时间信息，然后将字符串通过Date来解析，从而得到相应的Date对象。
在 JDK 1.1 之前，通常是通过Data操作“年月日时分秒”。不过，由于Date的相关 API 不易于实现国际化。从 JDK 1.1 开始，应该使用 Calendar 类来操作“年月日时分秒”，同时可以通过 DateFormat 类来格式化和解析日期字符串。Date 中的相应方法已废弃。

# Date和Calendar相互转换
- Date转换为Calendar
```java
// 新建date，且日期/时间为2013-09-19 14:22:30
Date date = new Date(113, 8, 19, 14, 22, 30);
// 新建Calendar对象，并设置日期为date
Calendar cal = Calendar.getInstance();
cal.setTime(date);
```

- Calendar换为Date
```java
// 新建Calendar对象
Calendar cal = Calendar.getInstance();
// 获取Calendar对应的Date
Date date = cal.getTime();
```

# Date 函数列表
Date 共包含下面5个构造函数
```java
// Date构造函数一：传入“年、月、日”
// (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
// (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
// (03) 日 -- 1-31 之间的某一天。
Date(int year, int month, int day)

// Date构造函数二：传入“年、月、日、时、分”
// (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
// (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
// (03) 日 -- 1-31 之间的某一天。
// (04) 时 -- 0-23 之间的小时数。
// (05) 分 -- 0-59 之间的分钟数。
Date(int year, int month, int day, int hour, int minute)

// Date构造函数三：传入“年、月、日、时、分、秒”
// (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
// (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
// (03) 日 -- 1-31 之间的某一天。
// (04) 时 -- 0-23 之间的小时数。
// (05) 分 -- 0-59 之间的分钟数。
// (06) 秒 -- 0-59 之间的秒钟数。
Date(int year, int month, int day, int hour, int minute, int second)

// Date构造函数四：传入“毫秒”。 毫秒 = “目标时间” - “1970-01-01 00:00:00 GMT”
Date(long milliseconds)

// Date构造函数五：传入“字符串”。
Date(String string)
```

# Date的操作API
```java
// 根据参数确定日期和时间。这些参数被解释为年份、月份、月中某一天、一天中的某一小时、小时中某一分钟和分钟中的某一秒。
static long     UTC(int year, int month, int day, int hour, int minute, int second)
// 此日期是否在指定日期之后。
boolean     after(Date date)
// 此日期是否在指定日期之前
boolean     before(Date date)
// 返回此对象的副本。
Object     clone()
// 比较两个日期的顺序。
int     compareTo(Date date)
// 比较两个日期是否相等。
boolean     equals(Object object)
// 返回此 Date 对象表示的月份中的某一天。返回的值在 1 和 31 之间，表示包含或开始于此 Date 对象表示的时间的月份中的某一天（用本地时区进行解释）。
int     getDate()
// 返回此日期表示的周中的某一天。返回值 (0 = Sunday, 1 = Monday, 2 = Tuesday, 3 = Wednesday, 4 = Thursday, 5 = Friday, 6 = Saturday) 表示一周中的某一天
int     getDay()
// 返回此 Date 对象表示的小时。返回值是一个数字（0 至 23）
int     getHours()
// 返回此日期所表示的小时已经过去的分钟数（用本地时区进行解释）。返回值在 0 和 59 之间。
int     getMinutes()
// 返回表示月份的数字，该月份包含或开始于此 Date 对象所表示的瞬间。返回的值在 0 和 11 之间。0为一月，1为二月，依次类推。
int     getMonth()
// 返回此日期所表示的分钟已经过去的秒数。返回的值在 0 和 61 之间。值 60 和 61 只可能发生在考虑了闰秒的 Java 虚拟机上。
int     getSeconds()
// 返回自 1970 年 1 月 1 日 00:00:00 GMT 以来此 Date 对象表示的毫秒数。
long     getTime()
// 返回相对于 UTC（相应于此 Date 对象表示的时间）的本地时区的偏移量（以分钟为单位）。
int     getTimezoneOffset()
// 返回一个值，此值是从包含或开始于此 Date 对象表示的瞬间的年份减去 1900 的结果（用本地时区进行解释）。
int     getYear()
// 哈希值
int     hashCode()
// 把字符串 s 解释为日期和时间的表示形式。
static long     parse(String string)
// 把此 Date 对象的月份中的某一天设置为指定值。
void     setDate(int day)
// 把此 Date 对象的小时设置为指定值。
void     setHours(int hour)
// 把此 Date 对象的分钟数设置为指定值。
void     setMinutes(int minute)
// 把此日期的月份设置为指定值。0为一月，1为二月，依次类推。
void     setMonth(int month)
// 把此 Date 的秒数设置为指定值。
void     setSeconds(int second)
// 设置此 Date 对象，以表示 1970 年 1 月 1 日 00:00:00 GMT 以后 time 毫秒的时间点。
void     setTime(long milliseconds)
// 把此 Date 对象的年份设置为指定的值加 1900。
void     setYear(int year)
// 返回GMT字符串
String     toGMTString()
// 返回本地字符串
String     toLocaleString()
// 返回字符串
String     toString()
```

# Date操作示例
下面我们通过示例学习使用Date的API。
源码如下(DateTest.java)：
```java
import java.util.Date;
import java.util.Calendar;

/**
 * java Date类的测试程序
 * 
 * 注意几点：
 * (01) Date中的“年”      -- 读取/设置 到的年份值=“‘时间年份’ - 1900年”
 * (02) Date中的“月”      -- 0是一月,1是二月,2是三月,依次类推。
 * (03) Date中的“星期几”  -- 1是周日,2是周一,3是周二,依次类推。
 *
 * @author skywang
 */
public class DateTest {
    
    public static void main(String[] args) {

        // 测试Date的构造函数：Date共有5类构造函数
        testDateConstructor();

        // 测试Date类的“设置”、“读取”函数
        testDateSet();

        // 测试Date类的before(), after(), compareTo(), equals(), clone(), parse()等接口
        testOtherDateAPIs();
    }

    /**
     * 测试Date的构造函数：Date共有5类构造函数
     */
    private static void testDateConstructor() {

        Date date;

        // Date构造函数一：传入“年、月、日”。
        // 参数说明
        // (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
        // (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
        // (03) 日 -- 1-31 之间的某一天。
        // 设置时间为“1988-08-08”
        date = new Date(88,7,8);
        System.out.printf("Constructor-1  : %s\n", tostring(date));

        // Date构造函数二：传入“年、月、日、时、分”
        // (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
        // (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
        // (03) 日 -- 1-31 之间的某一天。
        // (04) 时 -- 0-23 之间的小时数。
        // (05) 分 -- 0-59 之间的分钟数。
        // 设置时间为“1999-09-09 19:19”
        date = new Date(99,8,9,19,19);
        System.out.printf("Constructor-2  : %s\n", tostring(date));

        // Date构造函数三：传入“年、月、日、时、分、秒”
        // (01) 年 -- 减 1900 的年份。若要设为1988，则“年”应该是88。
        // (02) 月 -- 0-11 的月份。0是一月,1是二月,依次类推。
        // (03) 日 -- 1-31 之间的某一天。
        // (04) 时 -- 0-23 之间的小时数。
        // (05) 分 -- 0-59 之间的分钟数。
        // (06) 秒 -- 0-59 之间的秒钟数。
        date = new Date(100,10,10,20,10,10);
        System.out.printf("Constructor-3  : %s\n", tostring(date));

        // Date构造函数四：传入“毫秒”。 毫秒 = “目标时间” - “1970-01-01 00:00:00 GMT”
        // 973858210000(ms) 对应时间 2000-10-10 8:10:10
        date = new Date(973858210000l);
        System.out.printf("Constructor-4  : %s\n", tostring(date));

        // Date构造函数五：传入“字符串”。可以为以下几种格式：
        // (注意，year值 = “实际年份-1900”)
        // 1955-08-12 13:30:00
        date = new Date("Sat, 12 Aug 95 13:30:00 GMT");
        System.out.printf("Constructor-5.1: %s\n", tostring(date));
        // 1955-08-12 13:30:00
        date = new Date("12 Aug 95 13:30:00");
        System.out.printf("Constructor-5.2: %s\n", tostring(date));
        // 1955-08-12
        date = new Date("12 Aug 95");
        System.out.printf("Constructor-5.3: %s\n", tostring(date));
    }

    /**
     * 测试Date类的“读取”函数
     */
    private static void testGet(Date date) {
        // “年”。减 1900 的年份。若为1988，则“年”是88。
        int year = date.getYear();
        // “月”。 0-11 的月份。0是一月,1是二月,依次类推。
        int month = date.getMonth();
        // “日”
        int day = date.getDate();
        // “时”
        int hour = date.getHours();
        // “分”
        int minute = date.getMinutes();
        // “秒”
        int second = date.getSeconds();
        // “星期(几)”。 周日是1,周一是2,周二是3,依次类推。
        int weekday = date.getDay();
        // “毫秒”。毫秒 = “目标时间” - “1970-01-01 00:00:00 GMT”
        long millis = date.getTime();
        // “时区偏移”。相对于 UTC 的本地时区的偏移量（以分钟为单位）。
        int timezoneoffset = date.getTimezoneOffset();

        //System.out.printf("\t!!!date get is: %s\n", tostring(date));
        System.out.println("\t!!!get date: "+year+"-"+month+"-"+day+" "+hour+":"+minute+":"+second+"\t"+millis+"(ms)");
    }


    /**
     * 测试Date类的“设置”函数
     */
    private static void testDateSet() {
        // 新建date
        Date date = new Date(99,8,9);

        // 设置Date为“2013-09-19 15:28:30”
        // 设为“2013年”。传入值是“‘目标年份’ - ‘1900 的年份’”
        date.setYear(113);
        // 设为“8月”，传入的参数应该是8。因为，一月是0,二月是1,依次类推。
        date.setMonth(8);
        // 设为“19日”
        date.setDate(19);
        // 设为“15”(上午)。采用的24时制；因此，若要设为上午3点，应该传入参数3。
        date.setHours(11);
        // 设为“28分”
        date.setMinutes(28);
        // 设为“30秒”
        date.setSeconds(30);
        System.out.printf("new date-01 is: %s\n", tostring(date));

        // 测试Date的获取函数
        testGet(date);

        // 设为“毫秒”，1379561310000(ms) 对应的时间是“2013-09-19 15:28:30”
        date.setTime(1379561310000l);
        System.out.printf("new date-02 is: %s\n", tostring(date));
    }

    /**
     * 测试Date类的before(), after(), compareTo(), equals(), clone(), parse()等接口
     */
    private static void testOtherDateAPIs() {
        // 初始化d1=2008-05-12, d2=2009-03-15。
        Date d1 = new Date(108,4,12);
        System.out.printf("\nd1 is: %s\n", tostring(d1));
        Date d2 = new Date(109,2,15);
        System.out.printf("d2 is: %s\n", tostring(d2));

        // 克隆
        Date d3 = (Date) d1.clone();
        System.out.printf("clone of d1 is: %s\n", tostring(d3));

        // d1 是否早于 d2
        boolean isBefore = d1.before(d2);
        // d1 是否晚于 d2
        boolean isAfter = d1.after(d2);
        // d1 是否等于 d2
        boolean isEquals = d1.after(d2);
        // d1 和 d2 比较。
        // 若d1 早于 d2，返回 -1
        // 若d1 晚于 d2，返回 1
        // 若d1 等于 d2，返回 0
        int comp = d1.compareTo(d2);
        System.out.printf("isBefore=%s, isAfter=%s, isEquals=%s, comp=%s\n", 
                isBefore, isAfter, isEquals, comp);

        // parse接口
        long millis = Date.parse("13 Mar 2009");
        // (注意，通过这种方式设置Date，获取的Year值是“实际年份-1900”)
        Date d4 = new Date(millis);
        System.out.printf("millis=%s, d4=%s\n", millis, tostring(d4));
        System.out.printf("d1.toGMTString()%s\n", d1.toGMTString());
        System.out.printf("d1.toLocaleString()%s\n", d1.toLocaleString());
        System.out.printf("d1.toString()%s\n", d1.toString());
    }

    /**
     * 将date转换Calendar对象，并返回实际的年月日。
     */
    private static String tostring(Date date) {
        // 获取Date对应的Calendar
        Calendar cal = Calendar.getInstance();
        cal.setTime(date);
        int year = cal.get(Calendar.YEAR);
        int month = cal.get(Calendar.MONTH)+1;
        int day = cal.get(Calendar.DATE);
        int hour = cal.get(Calendar.HOUR);
        int minute = cal.get(Calendar.MINUTE);
        int second = cal.get(Calendar.SECOND);
        long millis = cal.getTimeInMillis();

        return year+"-"+month+"-"+day+" "+hour+":"+minute+":"+second+"\t"+millis+"(ms)";
    }
}
```

# GMT、UTC、时区的关系
许多人都知道两地时间表简称为GMT或UTC，而世界时区表则通称为World Time，那么GMT与UTC的实质原意又是为何？世界时区又是怎么区分的？面盘上密密麻麻的英文单字代表着什么意义与作用呢？这些都是新手在接触两地时间表或世界时区表时，脑海中所不断浮现的种种疑问，以下将带您一探时区奥妙的究竟。
## 世界时区及国际换日线示意图
图挂了...


## 全球24个时区的划分 
相较于两地时间表，可以显示世界各时区时间和地名的世界时区表（World Time），就显得精密与复杂多了，通常世界时区表的表盘上会标示着全球24个时区的城市名称，但究竟这24个时区是如何产生的？过去世界各地原本各自订定当地时间，但随着交通和电讯的发达，各地交流日益频繁，不同的地方时间，造成许多困扰，于是在西元1884年的国际会议上制定了全球性的标准时，明定以英国伦敦格林威治这个地方为零度经线的起点（亦称为本初子午线），并以地球由西向东每24小时自转一周360°，订定每隔经度15°，时差1小时。而每15°的经线则称为该时区的中央经线，将全球划分为24个时区，其中包含23个整时区及180°经线左右两侧的2个半时区。就全球的时间来看，东经的时间比西经要早，也就是如果格林威治时间是中午12时，则中央经线15°E的时区为下午1时，中央经线30°E时区的时间为下午2时；反之，中央经线15°W的时区时间为上午11时，中央经线30°W时区的时间为上午10时。以台湾为例，台湾位于东经121°，换算后与格林威治就有8小时的时差。如果两人同时从格林威治的0°各往东、西方前进，当他们在经线180°时，就会相差24小时，所以经线180°被定为国际换日线，由西向东通过此线时日期要减去一日，反之，若由东向西则要增加一日。

## 格林威治标准时间GMT
十七世纪，格林威治皇家天文台为了海上霸权的扩张计画而进行天体观测。1675年旧皇家观测所(Old Royal Observatory) 正式成立，到了1884年决定以通过格林威治的子午线作为划分地球东西两半球的经度零度。观测所门口墙上有一个标志24小时的时钟，显示当下的时间，对全球而言，这里所设定的时间是世界时间参考点，全球都以格林威治的时间作为标准来设定时间，这就是我们耳熟能详的「格林威治标准时间」(Greenwich Mean Time，简称G.M.T.)的由来，标示在手表上，则代表此表具有两地时间功能，也就是同时可以显示原居地和另一个国度的时间。

## 世界协调时间UTC
多数的两地时间表都以GMT来表示，但也有些两地时间表上看不到GMT字样，出现的反而是UTC这3个英文字母，究竟何谓UTC？事实上，UTC指的是Coordinated Universal Time－ 世界协调时间（又称世界标准时间、世界统一时间），是经过平均太阳时(以格林威治时间GMT为准)、地轴运动修正后的新时标以及以「秒」为单位的国际原子时所综合精算而成的时间，计算过程相当严谨精密，因此若以「世界标准时间」的角度来说，UTC比GMT来得更加精准。其误差值必须保持在0.9秒以内，若大于0.9秒则由位于巴黎的国际地球自转事务中央局发布闰秒，使UTC与地球自转周期一致。所以基本上UTC的本质强调的是比GMT更为精确的世界时间标准，不过对于现行表款来说，GMT与UTC的功能与精确度是没有差别的。

## 夏日节约时间DST
所谓「夏日节约时间」Daylight Saving Time（简称D.S.T.），是指在夏天太阳升起的比较早时，将时钟拨快一小时，以提早日光的使用，在英国则称为夏令时间(Summer Time)。这个构想于1784年由美国班杰明·富兰克林提出来，1915年德国成为第一个正式实施夏令日光节约时间的国家，以削减灯光照明和耗电开支。自此以后，全球以欧洲和北美为主的约70个国家都引用这个做法。目前被划分成两个时区的印度也正在商讨是否全国该统一实行夏令日光节约时间。欧洲手机上也有很多GSM系统的基地台，除了会传送当地时间外也包括夏令日光节约时间，做为手机的时间标准，使用者可以自行决定要开启或关闭。值得注意的是，某些国家有实施「夏日节约时间」的制度，出国时别忘了跟随当地习惯在表上调整一下，这可是机械表没有的功能设计哦！