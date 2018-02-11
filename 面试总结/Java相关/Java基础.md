

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

#### 既然反射可以访问private方法 那private是否还有意义
```java
Method method2 =e.getClass().getDeclaredMethod("fun2");
//这个是必须的
method2.setAccessible(true);
method2.invoke(e);
```
反射主要是为了灵活，而且使用Java Security Manager可以关掉反射访问private

#### 四、面向对象的三大特性
继承、封装、多态

#### 数据库的三范式
- 字段不可分
- 有主键，非主键字段依赖主键
- 非主键字段不能互相依赖

#### java产生随机数的几种方式

- 通过System.currentTimeMillis（）来获取一个当前时间毫秒数的long型数字
- 通过Math.random（）返回一个0到1之间的double值
- 通过Random类来产生一个随机数，这个是专业的Random工具类，功能强大
