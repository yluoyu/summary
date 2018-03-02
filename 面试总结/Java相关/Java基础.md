

#### 一、遍历中修改List问题
```java
Collection<String> list = new ArrayList<String>();
        list.add("Android");
        list.add("iPhone");
        list.add("Windows Mobile");

        // example 0
        Iterator<String> itr0 = list.iterator();
        while(itr0.hasNext()){
            String lang = itr0.next();
            itr0.remove();
        }

        // example 1
        Iterator<String> itr1 = list.iterator();
        while(itr1.hasNext()){
            String lang = itr1.next();
            list.remove(lang);
        }

        // example 2
        for(int i = 0; i < list.size(); i++){
            list.remove(i);
        }

        // example 3
        for(String language: list){
            list.remove(language);
        }
```
- example0,example2不会抛出ConcurrentModificationException异常;
- example1,example3则会抛出ConcurrentModificationException异常;

原因分析:
example1没调用Iterator的remove方法进行删除;在itr1.next()中会检查
```java
ourList.modCount != expectedModCount
```
example3的for-each循环内部其实也是使用了Iterator来遍历Collection，它也调用了Iterator.next()，这同样也会检查(元素的)变化并抛出ConcurrentModificationException!

#### 二、java8 forEach 在Map和List中的使用
```java
for (Map.Entry<String,Integer> entry : items.entrySet()){
    System.out.println("key:"+entry.getKey()+";value:"+entry.getValue());
}

items.forEach((k,v)->System.out.println("key : " + k + "; value : " + v));
```

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
