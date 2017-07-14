# java-
参考书目： Core Java 10th Edition







>今天我们来专门说说Java中的时间表示


### 4.2.2 Java标准库中的LocalDate类
Date类的一个实例表示一个特定状态，也就是某一特定时刻。它封装了从 00:00:00 UTC, January 1, 1970 到某一特定时刻的毫秒数。UTC指的是Coordinate Universal Time, 除此之外还有一个同样用于科学测量的时间：GMT(Greenwich Mean Time)。

可是在实际应用中，Date类在用来表示日历信息时不是很方便。于是标准库的设计者又设计出一个LocalDate用常用日历表示法来表示日期。（Java SE 8 又引进了不少类来表示时间与日期的不同方面，在本书的第二卷第六章有详细介绍）

LocalDate使用工厂方法来创建实例，比如我们要创建这样一些实例：  
`LocalDate.now();    //表示这个对象被创建时的日期`  
`LocalDate newYearsEve = LocalDate.of(1999, 12, 31);    //一个特定的日期` 

当你获得了一个LocalDate对象的引用时，你就可以调用它的getYear, getMonthValue, getDayOfMonth方法：  
```java
int year = newYearsEve.getYear(); //1999
int month = newYearsEve.getMonthValue(); //12
int day = newYearsEve.getDayOfMonth(); //31
```

你还可以在一个实例日期的基础上做运算：  
```java
LocalDate aThousandDaysLater = newYearsEve.plusDays(1000);
int year = aThousandDaysLater.getYear(); //2002
int month = aThousandDaysLater.getMonthValue(); //09
int day = aThousandDaysLater.getDayOfMonth(); //26
```
### 4.2.3 LocalDate类中的修改器与访问器
这一节没讲啥有用的，不过有一个小程序很有趣：
```java
public class CalendarTest{
    public static void main(String... args){
        LocalDate date = LocalDate.now();
        int month = date.getMonthValue();
        int today = date.getDayOfMonth();
        
        date = date.minusDays(today - 1); //Set to start of month
        DayOfWeek weekday = date.getDayOfWeek();
        int value = weekday.getValue(); //1 = Monday, ...... 7 = Sunday
        
        System.out.println("Mon Tue Wed Thu Fri Sat Sun");
        for(int i = 1; i < value; i++)
            System.out.print("   ");
        while(date.getMonthValue() == month){
            System.out.printf("%3d", date.getDayOfMonth());
            if(date.getDayOfMonth() == today)
                System.out.print("*");
            else
                System.out.print(" ");
            date = date.plusDays(1);
            if(date.getDayOfWeek().getValue() == 1) System.out.println();
        }
        if(date.getDayOfWeek().getValue() != 1) System.out.println();
    }
}
```

## 第6章 The Date and Time API
> Time feels like an arrow, and we can easily set a starting point count forward and backward in seconds. So why is it so hard to deal with time? The problem is humans. All would be easy if we could just tell each other:“Meet me at 1253793600, and dot't be late!” But we want time to relate to daylight and the seasons. That's where things get complicated. Java 1.0 had a Date class that was, in hindsight, naive, and had most of its methods deprecated in Java 1.1 when a calendar class was introduced. Its API wasn't stellar, its instances were mutable, and it didn't deal with issues such as leap seconds. The third time is a charm, and the `java.time` API introduced in **Java SE 8** has remedied the flaws of the past and should serve us for quite some time.
### 6.1 时间线
The Java Date and Time API 要求Java使用这样的定义：
+ 一天有86400秒
+ 如果有网的话，每天中午跟铯原子钟对一下时间
+ 没条件的话，就用别的方法对一下时间

本节介绍了一个`Instant`类，它可以将时间精确到纳秒：
一个`Instant`的实例的值可以回溯到十亿年以前（`Instant.MIN`）。而最大的值`Instant.MAX`可以达到1,000,000,000年的12月31号。

静态方法`Instance.now()`将产出一个当前时刻的实例，你可以使用`equals`或`compareTo`方法来比较两个`Instance`实例，你可以将`Instance`实例当成时间戳来使用。

为了找出两个`Instance`实例的差别大小，我们应使用静态方法`Duration.between`。下面是一个例子：
```java
Instant start = Instant.now();
runAlgorithm();
Instant end = Instant.now();
Duration timeElapsed = Duration.bewteen(start, end);
long millis = timeElapsed.toMillis();
```
一个`Duration`表示两个`Instance`实例间差距的量。你可以将这个`Duration`换算成各种单位：`toNanos`，`toMillis`，`getSeconds`，`toMinutes`，`toHours`，`toDays`。

一个`Duration`会需要一个超出`long`能承受范围的值来做存储，单位为秒的值会存进一个`long`中，而单位为纳秒的值会额外需要一个`int`来做存储，如果你想以纳秒为单位来做计算，那就需要查看`Duration`提供的方法了。
