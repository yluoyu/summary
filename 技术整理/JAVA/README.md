### 简要说明

**仅供个人梳理知识所用**

- `Java 8 日期时间api`优先使用

- `Collections.frequency(list, temp)` 查询指定元素的出现次数

- isAssignableFrom() 是用来判断一个类Class1和另一个类Class2是否相同或是另一个类的超类或接口 `Class1.isAssignableFrom (Class2) `
- instanceof 判断一个对象实例是否是一个类或接口的或其子类子接口的实例 `oo instanceof TypeName`

**Collection集合的三种初始化方法**
- `Arrays.asList()`方法：接受一个数组或一个逗号分隔的元素列表，并将其转化为Lists对象
- `Collections.addAll()`方法接受一个Collection对象，以及一个数组或是一个用逗号分割的列表，将其添加到Collection中
- `collection.addAll(Arrays.asList(moreInts))`;


### 拼接字符
```java
//使用commons-lang库写法, 其实这个已经够简单了，就这个功能而言，我很喜欢，而且最最常用：
System.out.println(StringUtils.join(list.toArray(), ","));

//进入jdk8时代：
System.out.println(list.stream().collect(Collectors.joining()));
//jdk8时代，加个分隔符：
System.out.println(list.stream().collect(Collectors.joining(",")));
```

### 常见Jar库
- commons-lang3
- Json相关
  - fastJson
  - gson
  - jackson

### 引用说明
