参考学习：
[Java面试题全集（上）](http://www.importnew.com/22083.html)
[Java面试题全集（下）](http://www.importnew.com/22087.html)
[最近5年133个Java面试问题列表](http://www.importnew.com/17232.html)

#### 三、Java8 时间/日期API
**LocalDate**
```java
LocalDate date = LocalDate.of(2014, Month.JUNE, 10);
int year = date.getYear(); // 2014
Month month = date.getMonth(); // 6月
int dom = date.getDayOfMonth(); // 10
DayOfWeek dow = date.getDayOfWeek(); // 星期二
int len = date.lengthOfMonth(); // 30 （6月份的天数）
boolean leap = date.isLeapYear(); // false （不是闰年）
```
**LocalTime**
```java
LocalTime time = LocalTime.of(20, 30);
int hour = date.getHour(); // 20
int minute = date.getMinute(); // 30
time = time.withSecond(6); // 20:30:06
time = time.plusMinutes(3); // 20:33:06
```

**LocalDateTime**
```java
LocalDateTime dt1 = LocalDateTime.of(2014, Month.JUNE, 10, 20, 30);
LocalDateTime dt2 = LocalDateTime.of(date, time);
LocalDateTime dt3 = date.atTime(20, 30);
LocalDateTime dt4 = date.atTime(time);
```
**DateTimeFormatter**
```java
DateTimeFormatter f = DateTimeFormatter.ofPattern("dd/MM/uuuu");
LocalDate date = LocalDate.parse("24/06/2014", f);
String str = date.format(f);
```

#### 四、既然反射可以访问private方法 那private是否还有意义
```java
Method method2 =e.getClass().getDeclaredMethod("fun2");
//这个是必须的
method2.setAccessible(true);
method2.invoke(e);
```
反射主要是为了灵活，而且使用Java Security Manager可以关掉反射访问private

#### 五、面向对象的三大特性
继承、封装、多态

#### 六、数据库的三范式
- 字段不可分
- 有主键，非主键字段依赖主键
- 非主键字段不能互相依赖

#### 七、java产生随机数的几种方式

- 通过System.currentTimeMillis（）来获取一个当前时间毫秒数的long型数字
- 通过Math.random（）返回一个0到1之间的double值
- 通过Random类来产生一个随机数，这个是专业的Random工具类，功能强大

#### 八、AccessController.doPrivileged
是一个在AccessController类中的静态方法，允许在一个类实例中的代码通知这个AccessController：它的代码主体是享受”privileged(特权的)”，它单独负责对它的可得的资源的访问请求，而不管这个请求是由什么代码所引发的
这就是说，一个调用者在调用doPrivileged方法时，可被标识为 “特权”。在做访问控制决策时，如果checkPermission方法遇到一个通过doPrivileged调用而被表示为 “特权”的调用者，并且没有上下文自变量，checkPermission方法则将终止检查。如果那个调用者的域具有特定的许可，则不做进一步检查，checkPermission安静地返回，表示那个访问请求是被允许的；如果那个域没有特定的许可，则象通常一样，一个异常被抛出

#### 九、Map中的NUll值
- HashMap可以存储一个Key为null，多个value为null的元素
- Hashtable key和value却不可以存储null 会有`NullPointerException`
- ConcurrentHashMap 和Hashtable相同

#### 十、访问权限
修饰词  |本类   |同一个包的类   |继承类   |  其他类
--|---|---|---|--
private  |√   |X   | X  |  X
无(默认)  |√   | √  | X  |  X
protected  |√   |√   |√   |  X
public  |√   |√   |√   |  √

访问权限控制的作用:
- 让客户端程序员无法触及他们不应该触及的一部分数据——这些数据对于数据类型的内部操作是必须的，但并不是解决特定问题所需接口的一部分。隐藏一些实现的细节对于保护数据类型内部脆弱的部分，提高程序的安全性和可用性也是必须的
- 允许类库的设计者改变其内部的工作方式而不影响客户端程序员。在设计者有更加优化的代码设计方式的时候可以随时改变类的内部结构，而这些对于客户端程序员都是不可见的，他们也无需关心类的实现细节

#### 十一、定时任务
**Timer**
```java
/**
 * 周期性调度任务，在delay（ms）后开始调度。
 * 并且任务开始时间的间隔为period（ms），即“固定间隔”执行
 */
public void schedule(TimerTask task, long delay, long period)

public void scheduleAtFixedRate(TimerTask task, long delay, long period)
```
- schedule “fixed-delay” 随后的执行时间按照 上一次 实际执行完成的时间点 进行计算
- scheduleAtFixedRate方法 “fixed-rate” 随后的执行时间按照上一次开始的时间点进行计算

**Timer的缺陷**
Timer被设计成支持多个定时任务，通过源码发现它有一个任务队列用来存放这些定时任务，并且启动了一个线程来处理，如下部分源码所示:
通过这种单线程的方式实现，在存在多个定时任务的时候便会存在问题：若任务B执行时间过长，将导致任务A延迟了启动时间！
还存在另外一个问题，应该是属于设计的问题：若任务线程在执行队列中某个任务时，该任务抛出异常，将导致线程因跳出循环体而终止，即Timer停止了工作！

Timer基本处理模型是单线程调度的任务队列模型，Timer不停地接受调度任务，所有任务接受Timer调度后加入TaskQueue,TimerThread不停地去TaskQueue中取任务来执行.此种方式的不足之处为当某个任务执行时间较长，以致于超过了TaskQueue中下一个任务开始执行的时间，会影响整个任务执行的实时性

```java
long currentTime, executionTime;
task = queue.getMin();
synchronized(task.lock) {
    if (task.state == TimerTask.CANCELLED) {
        queue.removeMin();
        continue;  // No action required, poll queue again
    }
    currentTime = System.currentTimeMillis();
    executionTime = task.nextExecutionTime;
    if (taskFired = (executionTime<=currentTime)) {
        if (task.period == 0) { // Non-repeating, remove
            queue.removeMin();
            task.state = TimerTask.EXECUTED;
        } else { // Repeating task, reschedule
             // 关键点 schedule方法的时候 这里的task.period 是负数。
            queue.rescheduleMin(
              task.period<0 ? currentTime   - task.period
                            : executionTime + task.period);
        }
    }
  }
```

Timer原理：最小堆 + 单线程

**替代方案：ScheduledThreadPoolExecutor**
```java
ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);

        Task1 t1 = new Task1();
        //立即执行t1，3s后任务结束，再等待5s（间隔时间-消耗时间），如果有空余线程时，再次执行该任务
        pool.scheduleAtFixedRate(t1, 0, 5, TimeUnit.SECONDS);
```
```java
// 强调固定频率 指的是两次任务执行开始之间的间隔 不考虑任务执行时间的影响
public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,long initialDelay,long period,TimeUnit unit);
// 强调间隔 从上一次任务执行结束后开始计算间隔
public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,long initialDelay,long period,TimeUnit unit);
```

#### 十二、内部类

**普通类**
在一个Java源码文件中可以有多个普通的类；如Main和Base这种最外层定义的并列的列，这种普通的Java类在Java文件里面必须有一个是与Java文件的名字相同，只有与文件名字相同的这个普通类才可以用public来修饰。如果尝试将Base类改为 public 编译器会报错
这种普通类在编译之后会生成一个与类名对应的.class文件，如截图中出现的Main.class ,Base.class
区别 无法使用权限修饰符修饰 默认的包权限

**内部类**
内部类是指在一个外部类的内部再定义一个类.
内部类和外部类的区别：
- 内部类可以使用private关键字修饰，外部类不可以
- 内部类可以使用static 关键字修饰，外部类不可以
- 内部类在编译之后生成的class文件类名规则不同，普通的类是和类名一直，而内部类的名字则是外部类的名字+\$+内部类的名字；如截图Main\$AA.class（匿名内部类是外部类名+\$+数字）
- 如果一个内部类比较特殊没有名字，那么他就叫做匿名内部类。匿名内部类的基本特征： 一般来说，new 一个对象时小括号后应该是分号，也就是new出对象该语句就结束了。但是出现匿名内部类就不一样，小括号后跟的是大括号，大括号中是该new 出对象的具体的实现方法。

**1.静态内部类**
- 必须以static关键字标注
- 只能访问外部类中的静态的成员变量或者是静态的方法
- 访问一个内部类使应该这样`outerClass.innerClass inter = new outerClass.innerClass();`不能直接实例化内部类

**2.成员内部类**
- 定义在一个类的内部，但是没有static关键字修饰
- 生成示例的方法`outerClass.innerClass inter = (new outerClass()).new innerClass()`
- 对外部类变量的引用outClass.this.variale
- 可以访问外部类的静态与非静态方法

**3.局部内部类**
- 局部内部类指的是定义在一个方法中的类
- 只有在当前方法中才能对局部内部类里面的方法以及变量进行访问
- 局部内部类只能访问其所在方法的final类型变量

**4.匿名内部类**
- 隐式的继承一个父类或者是实现某个接口

`注意点：`
```
1. 注意在创建非静态内部类对象时，一定要先创建起相应的外部类对象。原因，上面也有提到 非静态内部类对象有着指向其外部类对象的引用
2. 非静态内部类可以定义实例变量，但静态变量一定要是常量(static final)
3. 非静态内部类一定不能定义静态方法
4. 静态内部类可以定义实例变量，也可以定义静态变量
5. 当然也可以定义静态方法和实例方法，同时可以肯定的是静态方法不能访问类中的非静态变量和方法
6. 局部内部类只能访问final变量
```
java中绝大多数情况下，类的访问修饰符只有public和默认这两种，但也有例外。静态内部类和成员内部类还可以有protected和private两种


#### HashMap
HashMap中key为null的元素 放在table[0]中。
- HashMap的源码，实现原理，JDK8中对HashMap做了怎样的优化
  - 红黑树
- HaspMap扩容是怎样扩容的，为什么都是2的N次幂的大小
  -扩容的时候 桶链内容的重分配，要么在当前位置，要么在下标为2n+1的位置
- HashMap，HashTable，ConcurrentHashMap的区别
  - HashTable不允许有null值的存在
  - HashMap是非线程安全的，HashTable是线程安全的，内部的方法基本都是synchronized
- 极高并发下HashTable和ConcurrentHashMap哪个性能更好，为什么，如何实现的
  - synchronized关键字加锁的原理，其实是对对象加锁，不论你是在方法前加synchronized还是语句块前加，锁住的都是对象整体，但是ConcurrentHashMap的同步机制和这个不同，它不是加synchronized关键字，而是基于lock操作的，这样的目的是保证同步的时候，锁住的不是整个对象。事实上，ConcurrentHashMap可以满足concurrentLevel个线程并发无阻塞的操作集合对象。关于concurrentLevel稍后介绍
  - ConcurrentHashMap引入了分割(Segment)，上面代码中的最后一行其实就可以理解为把一个大的Map拆分成N个小的HashTable，在put方法中，会根据hash(paramK.hashCode())来决定具体存放进哪个Segment，如果查看Segment的put操作，我们会发现内部使用的同步机制是基于lock操作的，这样就可以对Map的一部分（Segment）进行上锁，这样影响的只是将要放入同一个Segment的元素的put操作，保证同步的时候，锁住的不是整个Map（HashTable就是这么做的），相对于HashTable提高了多线程环境下的性能，因此HashTable已经被淘汰了
- HashMap在高并发下如果没有处理线程安全会有怎样的安全隐患，具体表现是什么
  - 高并发的情况下，链有可能形成循环链，这样get时会发生死循环的，由此造成CPU 100%,

#### 动态代理的两种方式，以及区别
- jdk动态代理 实现InvocationHandler。
- cglib动态代理
- 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP
- 如果目标对象实现了接口，可以强制使用CGLIB实现AOP
- 如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换
- CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法.因为是继承，所以该类或方法最好不要声明成final

#### Java序列化的方式
- 实现Serializable接口
- 把对象包装成JSON字符串
- 采用谷歌的ProtoBuf

#### 传值和传引用
- java中方法参数传递方式是按值传递
- 如果参数是基本类型，传递的是基本类型的字面量值的拷贝
- 如果参数是引用类型，传递的是该参量所引用的对象在堆中地址值的拷贝

#### 一个ArrayList在循环过程中删除，会不会出问题
- Iterator迭代器删除的问题。

#### @transactional注解在什么情况下会失效
- 同一个类中的没有@transactional注解的方法，通过this调用包含@transactional注解的方法时。因为this调用的是没经过增强的类。

#### a = a + b 与 a += b 的区别
+= 隐式的将加操作的结果类型强制转换为持有结果的类型。如果两这个整型相加，如 byte、short 或者 int，首先会将它们提升到 int 类型，然后在执行加法操作。如果加法操作的结果比 a 的最大值要大，则 a+b 会出现编译错误，但是 a += b 没问题，如下：
byte a = 127;
byte b = 127;
b = a + b; // error : cannot convert from int to byte
b += a; // ok
（译者注：这个地方应该表述的有误，其实无论 a+b 的值为多少，编译器都会报错，因为 a+b 操作会将 a、b 提升为 int 类型，所以将 int 类型赋值给 byte 就会编译出错）

### 3*0.1 == 0.3 将会返回什么？true 还是 false？
false，因为有些浮点数不能完全精确的表示出来。
```
System.out.println(3*0.1);
//0.30000000000000004
```
#### int 和 Integer 哪个会占用更多的内存？(答案)
Integer 对象会占用更多的内存。Integer 是一个对象，需要存储对象的元数据。但是 int 是一个原始类型的数据，所以占用的空间更少

#### java中String类为什么要设计成不可变的
**字符串池**
字符串池是方法区中的一部分特殊存储。当一个字符串被被创建的时候，首先会去这个字符串池中查找，如果找到，直接返回对该字符串的引用
下面的代码只会在堆中创建一个字符串
```
String string1 = "abcd";
String string2 = "abcd";
```

如果字符串可变的话，当两个引用指向指向同一个字符串时，对其中一个做修改就会影响另外一个。（请记住该影响，有助于理解后面的内容）

首先String类是用final关键字修饰，这说明String不可继承。再看下面，String类的主力成员字段value是个char[]数组，而且是用final修饰的。final修饰的字段创建以后就不可改变

因为虽然value是不可变的，也只是value这个引用地址不可变。挡不住Array数组是可变的事实。
虽然正常时拿不到value元素的，但是可以通过反射取到。

String是不可变，关键是因为SUN公司的工程师，在后面所有String的方法里很小心地没有去动Array里的元素，没有暴露内部成员字段。private final char value[]这一句里，private的私有访问权限的作用都比final大。而且设计师还很小心地反整个String设计成final禁止继承，避免被其他人继承后破坏。所以String是不可变的关键在于底层的实现，而不是一个final。考验的是工程师构造数据类型，封装数据的功力

**缓存Hashcode**
Java中经常会用到字符串的哈希码（hashcode）。例如，在HashMap中，字符串的不可变能保证其hashcode永远保持一致，这样就可以避免一些不必要的麻烦。这也就意味着每次在使用一个字符串的hashcode的时候不用重新计算一次，这样更加高效

好处：
安全性：
String被广泛的使用在其他Java类中充当参数。比如网络连接、打开文件等操作。如果字符串可变，那么类似操作可能导致安全问题。因为某个方法在调用连接操作的时候，他认为会连接到某台机器，但是实际上并没有（其他引用同一String对象的值修改会导致该连接中的字符串内容被修改）。可变的字符串也可能导致反射的安全问题，因为他的参数也是字符串
```java
boolean connect(string s){
    if (!isSecure(s)) {
throw new SecurityException();
}
    //如果s在该操作之前被其他的引用所改变，那么就可能导致问题。
    causeProblem(s);
}
```
`不可变对象天生就是线程安全的`
因为不可变对象不能被改变，所以他们可以自由地在多个线程之间共享。不需要任何同步处。
总之，String被设计成不可变的主要目的是为了安全和高效

```
//创建字符串"Hello World"， 并赋给引用s
String s = "Hello World";
System.out.println("s = " + s); //Hello World
System.out.println(s.hashCode());
//获取String类中的value字段
Field valueFieldOfString = String.class.getDeclaredField("value");

//改变value属性的访问权限
valueFieldOfString.setAccessible(true);

//获取s对象上的value属性的值
char[] value = (char[]) valueFieldOfString.get(s);

//改变value所引用的数组中的第5个字符
value[5] = '_';

System.out.println("s = " + s);  //Hello_World
System.out.println(s.hashCode());

String s2 = "Hello World";
System.out.println("s2 = "+s2.hashCode());
System.out.println(s == s2); // true
```

#### Serial 与 Parallel GC之间的不同之处
Serial 与 Parallel 在GC执行的时候都会引起 stop-the-world。它们之间主要不同 serial 收集器是默认的复制收集器，执行 GC 的时候只有一个线程，而 parallel 收集器使用多个 GC 线程来执行

#### Java 中 WeakReference 与 SoftReference的区别
虽然 WeakReference 与 SoftReference 都有利于提高 GC 和 内存的效率，但是 WeakReference ，一旦失去最后一个强引用，就会被 GC 回收，而软引用虽然不能阻止被回收，但是可以延迟到 JVM 内存不足的时候

#### WeakHashMap 是怎么工作的
WeakHashMap 的工作与正常的 HashMap 类似，但是使用弱引用作为 key，意思就是当 key 对象没有任何引用时，key/value 将会被回收

#### 你能保证 GC 执行吗
不能，虽然你可以调用 System.gc() 或者 Runtime.gc()，但是没有办法保证 GC 的执行

#### 怎么获取 Java 程序使用的内存？堆使用的百分比
可以通过 java.lang.Runtime 类中与内存相关方法来获取剩余的内存，总内存及最大堆内存。通过这些方法你也可以获取到堆使用的百分比及堆内存的剩余空间。`Runtime.freeMemory()` 方法返回剩余空间的字节数，`Runtime.totalMemory()` 方法总内存的字节数，`Runtime.maxMemory()` 返回最大内存的字节数

#### Java 中堆和栈有什么区别
JVM 中堆和栈属于不同的内存区域，使用目的也不同。栈常用于保存方法帧和局部变量，而对象总是在堆上分配。栈通常都比堆小，也不会在多个线程之间共享，而堆被整个 JVM 的所有线程共享

#### Java 中怎么打印数组
你可以使用 `Arrays.toString()` 和 `Arrays.deepToString()` 方法来打印数组。由于数组没有实现 toString() 方法，所以如果将数组传递给 System.out.println() 方法，将无法打印出数组的内容，但是 Arrays.toString() 可以打印每个元素

#### Java 中的 HashSet，内部是如何工作的
HashSet 的内部采用 HashMap来实现。由于 Map 需要 key 和 value，所以所有 key 的都有一个默认 value。类似于 HashMap，HashSet 不允许重复的 key，只允许有一个null key，意思就是 HashSet 中只允许存储一个 null 对象

#### 写一段代码在遍历 ArrayList 时移除一个元素
该问题的关键在于面试者使用的是 ArrayList 的 remove() 还是 Iterator 的 remove()方法。这有一段示例代码，是使用正确的方式来实现在遍历的过程中移除元素，而不会出现 ConcurrentModificationException 异常的示例代码

#### 我们能自己写一个容器类，然后使用 for-each 循环码
可以，你可以写一个自己的容器类。如果你想使用 Java 中增强的循环来遍历，你只需要实现 Iterable 接口。如果你实现 Collection 接口，默认就具有该属性

#### ArrayList 和 HashMap 的默认大小是多数
在 Java 7 中，ArrayList 的默认大小是 10 个元素，HashMap 的默认大小是16个元素（必须是2的幂）

#### 有没有可能两个不相等的对象有有相同的 hashcode
有可能，两个不相等的对象可能会有相同的 hashcode 值，这就是为什么在 hashmap 中会有冲突。相等 hashcode 值的规定只是说如果两个对象相等，必须有相同的hashcode 值，但是没有关于不相等对象的任何规定

#### Java 中，编写多线程程序的时候你会遵循哪些最佳实践
- 给线程命名，这样可以帮助调试
- 最小化同步的范围，而不是将整个方法同步，只对关键部分做同步
- 如果可以，更偏向于使用 volatile 而不是 synchronized
- 使用更高层次的并发工具，而不是使用 wait() 和 notify() 来实现线程间通信，如 BlockingQueue，CountDownLatch 及 Semeaphore
- 优先使用并发集合，而不是对集合进行同步。并发集合提供更好的可扩展性
- 使用线程池

#### 说出 5 条 IO 的最佳实践
- 使用有缓冲区的 IO 类，而不要单独读取字节或字符
- 使用 NIO 和 NIO2
- 在 finally 块中关闭流，或者使用 try-with-resource 语句
- 使用内存映射文件获取更快的 IO

#### 基本数据库连接操作
```
//加载数据库驱动
Class.forName("com.mysql.jdbc.Driver");

//获取连接//http://baidu.com
Connection connection =DriverManager.getConnection("jdbc:mysql://localhost:3306/shopping?user=root&password=&char       acterEncoding=utf-8");

//通过连接创建statement
Statement statement =connection.createStatement();
//定义sql
//执行sql语句，得到结果resultset
ResultSet rs=statement.executeQuery("select * from allshop where shopname='"+name+"'")
```

#### 接口是什么？为什么要使用接口而不是直接使用具体类
接口用于定义 API。它定义了类必须得遵循的规则。同时，它提供了一种抽象，因为客户端只使用接口，这样可以有多重实现，如 List 接口，你可以使用可随机访问的 ArrayList，也可以使用方便插入和删除的 LinkedList。接口中不允许写代码，以此来保证抽象，但是 Java 8 中你可以在接口声明静态的默认方法，这种方法是具体的

#### Java 中，抽象类与接口之间有什么不同
Java 中，抽象类和接口有很多不同之处，但是最重要的一个是 Java 中限制一个类只能继承一个类，但是可以实现多个接口。抽象类可以很好的定义一个家族类的默认行为，而接口能更好的定义类型，有助于后面实现多态机制

#### 除了单例模式，你在生产环境中还用过什么设计模式
这需要根据你的经验来回答。一般情况下，你可以说依赖注入，工厂模式，装饰模式或者观察者模式，随意选择你使用过的一种即可。不过你要准备回答接下的基于你选择的模式的问题

#### 描述 Java 中的重载和重写
重载和重写都允许你用相同的名称来实现不同的功能，但是重载是编译时活动，而重写是运行时活动。你可以在同一个类中重载方法，但是只能在子类中重写方法。重写必须要有继承

#### Java 中，Serializable 与 Externalizable 的区别
Serializable 接口是一个序列化 Java 类的接口，以便于它们可以在网络上传输或者可以将它们的状态保存在磁盘上，是 JVM 内嵌的默认序列化方式，成本高、脆弱而且不安全。Externalizable 允许你控制整个序列化过程，指定特定的二进制格式，增加安全机制

#### 说出 5 个 JDK 1.8 引入的新特性
- Lambda 表达式，允许像对象一样传递匿名函数
- Stream API，充分利用现代多核 CPU，可以写出很简洁的代码
- Date 与 Time API，最终，有一个稳定、简单的日期和时间库可供你使用
- 扩展方法，现在，接口中可以有静态、默认方法
- 重复注解，现在你可以将相同的注解在同一类型上使用多次
