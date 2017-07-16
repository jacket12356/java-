# java-
参考书目： Core Java 10th Edition







>今天我们来专门说说Java中的时间表示
### <a name="cate">目录</href>
+ <a href="#4.2.2">4.2.2</href>
+ <a href="#4.2.3">4.2.3</href>
+ <a href="#6.1">6.1</href>
+ <a href="#6.2">6.2</href>
+ <a href="#6.3">6.3</href>
+ <a href="#6.4">6.4</href>
+ <a href="#5.7.3">5.7.3 反射分析</href>



<a name="4.2.2"> </href>
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
##### <a href="#cate">回目录</href>
<a name="4.2.3"> </href>
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
##### <a href="#cate">回目录</href>

## 第6章 The Date and Time API
> Time feels like an arrow, and we can easily set a starting point count forward and backward in seconds. So why is it so hard to deal with time? The problem is humans. All would be easy if we could just tell each other:“Meet me at 1253793600, and dot't be late!” But we want time to relate to daylight and the seasons. That's where things get complicated. Java 1.0 had a Date class that was, in hindsight, naive, and had most of its methods deprecated in Java 1.1 when a calendar class was introduced. Its API wasn't stellar, its instances were mutable, and it didn't deal with issues such as leap seconds. The third time is a charm, and the `java.time` API introduced in **Java SE 8** has remedied the flaws of the past and should serve us for quite some time.
<a name="6.1"> </href>
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


下面的例子利用我们刚刚学到的知识，以时间为指标衡量两个不同的算法：
```java
package timeline;

import java.time.*;
import java.util.*;
import java.util.stream.*;

public class Timeline {
    public static void main(String[] args) {
        Instant start = Instant.now();
        runAlgorithm();
        Instant end = Instant.now();
        Duration timeElapsed = Duration.between(start, end);
        long millis = timeElapsed.toMillis();
        System.out.printf("%d milliseconds\n", millis);

        Instant start2 = Instant.now();
        runAlgorithm2();
        Instant end2 = Instant.now();
        Duration timeElapsed2 = Duration.between(start2, end2);
        System.out.printf("%d milliseconds\n", timeElapsed2.toMillis());
        boolean overTenTimesFaster = timeElapsed.multipliedBy(10)
        .minus(timeElapsed2).isNegative();
        System.out.printf("The first algorithm is %smore than ten times
        faster",
        overTenTimesFaster ? "" : "not ");
    }

    public static void runAlgorithm() {
        int size = 10;
        List<Integer> list = new Random().ints().map(i -> i % 100).limit(size)
        .boxed().collect(Collectors.toList());
        Collections.sort(list);
        System.out.println(list);
    }


    public static void runAlgorithm2() {
        int size = 10;
        List<Integer> list = new Random().ints().map(i -> i % 100).limit(size)
        .boxed().collect(Collectors.toList());
        while (!IntStream.range(1, list.size()).allMatch(
        i -> list.get(i - 1).compareTo(list.get(i)) <= 0))
        Collections.shuffle(list);
        System.out.println(list);
    }
}
```
##### <a href="#cate">回目录</href>
<a name="6.2"> </href>
### 6.2 Local Dates
这一节涉及到了Java用于人类交流的时间类：LocalDate/Time和TimeZone。

LocalDate涉及到的方法概览：
![md](https://github.com/jacket12356/java-/blob/master/1.PNG)
![md](https://github.com/jacket12356/java-/blob/master/2.jpg)


举例来说，程序员日是一年中的第256天，我们可以用下面的方法算出它：  
`LocalDate programmersDay = LocalDate.of(2014, 1, 1).plusDays(255);`

让我们回想一下，用来表示两个`Instance`实例间差距的是`Duration`。而与之对应作用在`LocalDate`上的是`Period`，它表示所经过的一段时间：一天、一个月或者是一年。你可以调用`birthday.plus(Period.ofYears(1))`来获得明年的生日。当然，直接调用`birthday.plusYears(1)`更简单。

`Until`方法得出两个`LocalDate`实例间相差时间段`Period`实例：  
`independenceDay.until(christmas)`  
那将是一个大小为5个月零21天的`Period`实例，这个结果有点模糊，因为一个月有多少天并不确定，你还可以用下面的方式直接得出天数：  
`independenceDay.until(christmas, ChronoUnit.DAYS)  //174 days` 


除了`LocalDate`,标准库还提供了`MonthDay`，`YearMonth`，`Year`来描述完整日期的某一部分，例如，"December 25"可被描述为一个`MonthDay`实例。

下面的例子描述了`LocalDate`的具体使用方法：
```java
package localdates;

import java.time.*;
import java.time.temporal.*;

public class LocalDates {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now(); // Today's date
        System.out.println("today: " + today);

        LocalDate alonzosBirthday = LocalDate.of(1903, 6, 14);
        alonzosBirthday = LocalDate.of(1903, Month.JUNE, 14);
        // Uses the Month enumeration
        System.out.println("alonzosBirthday: " + alonzosBirthday);

        LocalDate programmersDay = LocalDate.of(2018, 1, 1).plusDays(255);
        // September 13, but in a leap year it would be September 12
        System.out.println("programmersDay: " + programmersDay);

        LocalDate independenceDay = LocalDate.of(2018, Month.JULY, 4);
        LocalDate christmas = LocalDate.of(2018, Month.DECEMBER, 25);

        System.out.println("Until christmas: " +
        independenceDay.until(christmas));
        System.out.println("Until christmas: "
        + independenceDay.until(christmas, ChronoUnit.DAYS));

        System.out.println(LocalDate.of(2016, 1, 31).plusMonths(1));
        System.out.println(LocalDate.of(2016, 3, 31).minusMonths(1));

        DayOfWeek startOfLastMillennium = LocalDate.of(1900, 1,
        1).getDayOfWeek();
        System.out.println("startOfLastMillennium: " + startOfLastMillennium);
        System.out.println(startOfLastMillennium.getValue());
        System.out.println(DayOfWeek.SATURDAY.plus(3));
    }
 }
```
##### <a href="#cate">回目录</href>
<a name="6.3"> </href>
### 6.3 日期调整
为实现计划应用，你会经常需要计算日期，比如，“每月的第一个星期二”。标准库中的`TemporalAdjusters`提供了一些这样的静态方法，可以让你做一些你需要的日期计算。你可以这样算：  
`LocalDate firstTuesday = LocalDate.of(year, month, 1).with(TemporalAdjusters.nextOrSame(DayOfWeek.TUESDAY));`  
*with*方法会返回一个新的`LocalDate`实例。  
![md](https://github.com/jacket12356/java-/blob/master/3.png);

你还可以通过实现`TemporalAdjuster`接口，来自定义调节器。
```java
TemporalAdjuster NEXT_WORKDAY = w -> {
    LocalDate result = (LocalDate) w;
    do{
        result = result.plusDays(1);
    }while(result.getDayOfWeek().getValue() >= 6);
    return result;
}; //这个lambda表达式的参数类型是‘Temporal’,它必须被转为‘LocalDate’
```

测试代码：
```java
import java.time.*;
import java.time.temporal.TemporalAdjuster;
import java.time.temporal.TemporalAdjusters;

public class TimeTester {
	public static void main(String... args){
		//LocalDate firstTuesday = LocalDate.of(2017, 6, 1).with(TemporalAdjusters.nextOrSame(DayOfWeek.TUESDAY));
		//System.out.println(firstTuesday);
		
		TemporalAdjuster NEXT_WORKDAY = w -> {
		    LocalDate result = (LocalDate) w;
		    do{
		        result = result.plusDays(1);
		    }while(result.getDayOfWeek().getValue() >= 6);
		    return result;
		};  //这个调节器实现了寻找下一个工作日的功能,遇到周末会跳过（do-while用在这儿很合适）
		
		LocalDate firstTuesday = LocalDate.of(2017, 6, 1).with(NEXT_WORKDAY);
		System.out.println(firstTuesday);
		
	}
}
```
##### <a href="#cate">回目录</href>
<a name="6.4"> </href>
### 6.4 LocalTime
`LocalTime`代表了一天中的某一时刻，你可以用下面两个方法创建它的实例：
```java
LocalTime rightNow = LocalTime.now();
LocalTime bedTime = LocalTime.of(22, 30); //或者LocalTime.of(22, 30, 0)
```

下面的表格介绍了关于该类的一些操作：  
![md](https://github.com/jacket12356/java-/blob/master/4.png)

我们学了这些新的Java时间类，为了实现向后兼容，处理老代码，标准库还提供了这样一些方法：
![md](https://github.com/jacket12356/java-/blob/master/5.jpeg)
##### <a href="#cate">回目录</href>

<a name="5.7.3"> </href>
### 5.7.3 反射分析
反射功能很强大，之前看书上总是弄不通（主要还是太详细了），直到上课时老师讲了一遍才茅塞顿开，语法已经很清楚了，书上专门开了一小节讲了一个关于如何利用反射分析任意一个类，我试着弄了一下，真还挺好的，配合着API文档效果一定很棒吧。
```java
package ex1;

import java.util.*;
import java.lang.reflect.*;

//为使类的代码更为易读，改写了原来书中的版本，保不齐有bug
public class ReflecTest {
	public static void main(String... args){
		String name;
		
		Scanner in = new Scanner(System.in);
		System.out.println("Enter class name(e.g. java.util.Date):");
		name = in.next();
		
		try{
			Class c1 = Class.forName(name);
			Class superc1 = c1.getSuperclass();
			String modifiers = Modifier.toString(c1.getModifiers());
			if(modifiers.length()>0)	System.out.print(modifiers + " ");
			System.out.print("class " + name);
			if(superc1 != null && superc1 != Object.class) System.out.print("extends " + superc1.getName());
			System.out.print(" {\n");
			printConstructors(c1);
			System.out.println();
			printMethods(c1);
			System.out.println();
			printFields(c1);
			System.out.println("}");
		}catch(ClassNotFoundException e){
			e.printStackTrace();
		}
	}
	
	public static void printConstructors(Class c1){
		String name = c1.getSimpleName();
		Constructor[] constructors = c1.getDeclaredConstructors();
		
		for(Constructor c : constructors){
			System.out.print("	");
			String modifiers = Modifier.toString(c.getModifiers());
			if(modifiers.length()>0)	System.out.print(modifiers + " ");
			System.out.print(name + "(");
			
			//输出形参
			Class[] paramTypes = c.getParameterTypes();
			for(int j = 0;j < paramTypes.length;j++){
				if(j > 0)	System.out.print(", ");
				System.out.print(paramTypes[j].getSimpleName());
			}
			System.out.println(");");
		}
	}
	
	public static void printMethods(Class c1){
		Method[] methods = c1.getDeclaredMethods();
		
		for (Method m : methods){
			Class retType = m.getReturnType();
			String name = m.getName();
			
			System.out.print("	");
			String modifiers = Modifier.toString(m.getModifiers());
			if(modifiers.length()>0)	System.out.print(modifiers + " ");
			
			if(retType.getName().equals("void") || !retType.getName().contains("."))
				System.out.print(retType.getName()+ " " + name + "(");
			else{
				try {
					System.out.print(Class.forName(retType.getName()).getSimpleName()+ " " + name + "(");
				} catch (ClassNotFoundException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
				
			
			
			//输出形参
			Class[] paramTypes = m.getParameterTypes();
			for(int j = 0;j < paramTypes.length;j++){
				if(j > 0)	System.out.print(", ");
				System.out.print(paramTypes[j].getSimpleName());
			}
			System.out.println(");");
		}
	}
	
	public static void printFields(Class c1){
		Field[] fields = c1.getDeclaredFields();
		
		for (Field f : fields){
			Class type = f.getType();
			String name = f.getName();
			System.out.print("	");
			String modifiers = Modifier.toString(f.getModifiers());
			if(modifiers.length()>0)	System.out.print(modifiers + " ");
			
			if(!type.getName().contains("."))
				System.out.println(type.getName()+ " " + name + ";");
			else{
				try {
					System.out.println(Class.forName(type.getName()).getSimpleName()+ " " + name + ";");
				} catch (ClassNotFoundException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
	}
}
```
![md](https://github.com/jacket12356/java-/blob/master/6.png)  
![md](https://github.com/jacket12356/java-/blob/master/7.png)  
```java
public static void show(Object obj){
	try{
		Class cls = obj.getClass();
		Field fs[] = cls.getDeclaredFields();
		for(Field f: fs){
			System.out.println(f.getName());
			f.setAccessible(true);
			System.out.println(f.get(obj));
		}
		//cls.getDeclaredField("name");
	}catch(Exception e){
		e.printStackTrace();
	}
}
```
![md](https://github.com/jacket12356/java-/blob/master/8.png)  
![md](https://github.com/jacket12356/java-/blob/master/9.png)  
![md](https://github.com/jacket12356/java-/blob/master/10.png)  
```java
public static void show(Object obj){
	try{
		Class cls = obj.getClass();
		Method ms[] = cls.getDeclaredMethods();
		for(Method m : ms){
			System.out.println(m.getName());
		}
		Method m = cls.getDeclaredMethod("setName", String.class);
		m.invoke(obj, "java");
		//构造函数也大致相同
	}catch(Exception e){
		e.printStackTrace();
	}
}
```
##### <a href="#cate">回目录</href>

