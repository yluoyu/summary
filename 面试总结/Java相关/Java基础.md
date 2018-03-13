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
