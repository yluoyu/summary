### 流是什么
流是Java API的新成员，它允许你以声明性方式处理数据集合（通过查询语句来表达，而不是临时编写一个实现）。就现在来说，你可以把它们看成遍历数据集的高级迭代器。此外，流还可以透明地并行处理，你无需写任何多线程代码了！我们简单看看使用流的好处吧。下面两端代码都是返回低热量的菜肴名称的，并且按照卡路里排序，一个使用Java7,写的，另一个是用Java8的流写的。比较一下。不用担心Java8代码怎么写的：

```java
List<Dish> lowCaloricDishes = new ArrayList<>();
        List<Dish> menu = Arrays.asList(new Dish("1", 100), new Dish("1", 300), new Dish("1", 500),
                new Dish("1", 1000));
        for (Dish d : menu) {  //使用迭代器筛选元素
            if(d.getCalories() < 400){
                lowCaloricDishes.add(d);
            }
        }
        Collections.sort(lowCaloricDishes, new Comparator<Dish>() {  // 使用匿名类菜肴排序

            @Override
            public int compare(Dish o1, Dish o2) {
                return Integer.compare(o1.getCalories(), o2.getCalories());
            }
        });
        List<String> lowCaloricDishesName = new ArrayList<>();
        for(Dish d: lowCaloricDishes){
            lowCaloricDishesName.add(d.getName()); //处理排序后的菜名列表
        }
```

Java8流方式
```java
List<String> lowCaloricDishesName = new ArrayList<>();
        lowCaloricDishesName =
                menu.stream()
                .filter(d -> d.getCalories() < 400)  //选出400卡路里以下的菜肴
                .sorted(Comparator.comparing(Dish::getCalories)) //按照卡路里排序
                .map(Dish::getName)  //提取菜肴的名称
                .collect(Collectors.toList()); //将所有的名称保存在List中
```

好处：
- 代码是以声明性方法来写的：说明了想要完成什么(筛选热量低的菜肴)而不是说明了如何实现一个操作(利用循环和if条件等控制流语句)。你在之前也看到了，这种方法加上行为参数化让你可以轻松应对变化的需求：你很容易再创建一个代码版本，利用Lamba表达式来筛选高卡路里的菜肴，而不是去复制粘贴代码
- 你可以把几个基础操作筛选链接起来，来表达复杂的数据处理流水线(在filter后面接上sorted、map和collect操作， 如下图所示)，同时保持代码清晰可读。filter的结果被传给了sorted方法，再传给map方法，最后传给collect方法

![流处理](https://i.loli.net/2018/02/11/5a7fb3bf3a497.png)


### 流简介
要讨论流，我们先来谈谈集合，这是最容易上手的方法。Java8中的集合支持一个新的stream方法，它会返回一个流(接口定义在java.util.stream.Stream里)。你在后面会看到，还有很多的方式来可以得到流，比如利用数值范围或从I/O资源生成的流元素

那么，流到底是什么呢？简短的定义就是"从支持数据处理操作的源生成的元素序列"。让我们一步步剖析这个定义

- **元素序列** 就像集合一样，流也提供了一个接口，可以访问特定元素类型的一组有序值。因为集合是数据结构，所以它的主要目的是以特定的时间/空间复杂度存储和访元素(ArrayList和LinkedList)，但是流的目的在于表达计算，比如你前面简单的filter、sorted和map。集合讲的是数据，流讲得是计算。我们在后面几节详细解释这个思想。
- **源** 流会使用一个提供数据的源，如集合、数组或者输入/输出资源。请注意，从有序集合生成流时会保留原有的顺序。由列表生成的流，其元素顺序与列表一致
- **数据处理操作** 流的数据处理功能支持类似于数据库的操作，以及函数式编程语言中的常用操作，如filter、map、reduce、find、match、sort等等。流操作可以顺序执行，也可以并行执行

流操作有两个重要的特点：
- 流水线 很多流操作本身会返回一个流，这样多个操作就可以链接起来，形成一个大的很大的流水线。这让我们以后的一些优化成为了可能，如延迟和短路。流水线的操作可以可以看做对数据源进行数据库式查询
- 内部迭代 与使用迭代器显示迭代的集合不同，流的迭代操作是在背后进行的

### 流与集合
粗略的说，集合与流之间的差异就在于什么时候进行计算。集合是一格内存中的数据结构，它包含数据结构中目前所有的值--集合中的每个元素都得先计算出来才能添加到集合中。(你可以往集合里面加东西或者删东西，但是不管什么时候，集合中的每个元素都是放在内存里的，元素都得先算出来才能成为集合的一部分)

**1.只能遍历一次**
请注意，和迭代器类似，流只能遍历一次。遍历完成之后，我们就说这个流已经被消费掉了。你可以从原始数据源那里再获得一个新的流来遍历一遍，就像迭代器一样(这里假设它是集合之类可重复的源，如果是I/O通道就没戏了)。例如以下代码会抛出一个异常，说流已经被消费掉了：

**2.外部迭代与内部迭代**
使用Collection接口需要用户去迭代(比如使用 for-each),这称为外部迭代。相反，Streams库使用内部迭代--它帮你迭代做了，还把得到的流值存在了某个地方，你只要给出一个函数说要干什么就可以了

### 流操作
java.util.stream.Stream中的stream接口中定义了许多操作。它们可以分为两大类
- 中间操作 比如filter、map和limit可以连成一条流水线
- 终端操作 比如collect触发流水线执行并且关闭它

**中间操作**
操作  |  类型 | 返回类型  |  操作参数 |  函数描述符
--|---|---|---|--
filter  |中间   | Stream\<T>  |  Predicate\<T> |T -> boolean
  map| 中间	  |Stream\<R>   |  Function\<T, R> |T -> R
limit  | 中间  | Stream\<T>  |   |
sorted  |  中间 |  Stream\<T> | Comparator\<T>  |(T, T) -> int
distinct  |  中间 | Stream\<T>  |   |

**终端操作**
操作  | 类型  |  目的
--|---|--
forEach  | 终端  |  消费流中的每个元素并对其应用Lambda。这一操作返回void
count  |  终端 |  返回流中元素的个数。这一操作返回long
collect  | 终端  |  把流归约成成一个集合，比如List、map甚至是Integer

### 流的使用

**flatMap-流的扁平化**
给定单词列表 ["Hello","World"]，你想要返回列表["H","e","l", "o","W","r","d"]

```
List<String> uniqueCharacters =
    words.stream()
         .map(w -> w.split(""))
         .flatMap(Arrays::stream)
         .distinct()
         .collect(Collectors.toList())
```
使用flatMap方法的效果是，各个数组并不是分别映射成一个流，而是映射成流的内容。所 有使用map(Arrays::stream)时生成的单个流都被合并起来，即扁平化为一个流

**查找和匹配**
另一个常见的数据处理套路是看看数据集中的某些元素是否匹配一个给定的属性。Stream
API通过allMatch、anyMatch、noneMatch、findFirst和findAny方法提供了这样的工具

#### 归约
到目前为止，你见到过的终端操作都是返回一个boolean(allMatch之类的)、void (forEach)或Optional对象(findAny等)。你也见过了使用collect来将流中的所有元素组
合成一个List
在这里你将看到如何把一个流中的元素组合起来，使用reduce操作来表达更复杂的查
询，比如“计算菜单中的总卡路里”或“菜单中卡路里最高的菜是哪一个”。此类查询需要将流
中所有元素反复结合起来，得到一个值，比如一个Integer。这样的查询可以被归类为归约操作 (将流归约成一个值)。用函数式编程语言的术语来说，这称为折叠(fold)，因为你可以将这个操
作看成把一张长长的纸(你的流)反复折叠成一个小方块，而这就是折叠操作的结果

**一、元素求和**
`int sum = numbers.stream().reduce(0, (a, b) -> a + b);`
reduce接受两个参数：
- 一个初始值，这里是0
- 一个BinaryOperator\<T>来将两个元素结合起来产生一个新值，这里我们用的是lambda `(a, b) -> a + b`

**二、最大值和最小值**
`Optional<Integer> max=numbers.stream().reduce(Integer::max);`

### 构建流

**一、由值创建流**
```java
//
Stream<String> stream = Stream.of("Java 8 ", "Lambdas ", "In ", "Action");
// 得到空流
Stream<String> emptyStream = Stream.empty();
```

**二、由数组创建流**
```
int[] numbers = {2, 3, 5, 7, 11, 13};
int sum = Arrays.stream(numbers).sum();
```

**三、由文件生成流**
Java中用于处理文件等I/O操作的NIO API(非阻塞 I/O)已更新，以便利用Stream API。 java.nio.file.Files中的很多静态方法都会返回一个流。例如，一个很有用的方法是 Files.lines，它会返回一个由指定文件中的各行构成的字符串流。使用你迄今所学的内容， 你可以用这个方法看看一个文件中有多少各不相同的词:

```java
long uniqueWords = 0;
// 这种写法可以自动关闭流 java7后支持的功能
try(Stream<String> lines =Files.lines(Paths.get("/Users/yangwenchang/data.txt"), Charset.defaultCharset()))
        {
            uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" "))).distinct().count();
        }catch(IOException e){
            e.printStackTrace();
        }
```

**四、由函数生成流:创建无限流**
Stream API提供了两个静态方法来从函数生成流:Stream.iterate和Stream.generate。这两个操作可以创建所谓的无限流:不像从固定集合创建的流那样有固定大小的流。由iterate和generate产生的流会用给定的函数按需创建值，因此可以无穷无尽地计算下去!一般来说， 应该使用limit(n)来对这种流加以限制，以避免打印无穷多个值

**迭代**
```java
Stream.iterate(0, n -> n + 2)
      .limit(10)
      .forEach(System.out::println);
```

**生成**
```java
Stream.generate(Math::random)
          .limit(5)
          .forEach(System.out::println);
```


### 用流收集数据

收集器的功能，也就是那些可以从Collectors 类提供的工厂方法(例如groupingBy)创建的收集器。它们主要提供了三大功能:
- 将流元素归约和汇总为一个值
- 元素分组
- 元素分区



**分组**
一个常见的数据库操作是根据一个或多个属性对集合中的项目进行分组。就像前面讲到按货 币对交易进行分组的例子一样。
`Map<Dish.Type, List<Dish>> dishesByType =menu.stream().collect(groupingBy(Dish::getType));`
其结果是下面的Map:
{FISH=[prawns, salmon], OTHER=[french fries, rice, season fruit, pizza],MEAT=[pork, beef, chicken]}

**分区**
分区是分组的特殊情况:由一个谓词(返回一个布尔值的函数)作为分类函数，它称分区函 数
分区函数返回一个布尔值，这意味着得到的分组Map的键类型是Boolean，于是它最多可以 分为两组——true是一组，false是一组

 Map<Dish.Type, List<Dish>> partitionedMenu = menu.stream().collect(partitioningBy(Dish::isVegetarian))

 #### 收集器接口
 Collector接口包含了一系列方法，为实现具体的归约操作(即收集器)提供了范本。我们已经看过了Collector接口中实现的许多收集器，例如toList或groupingBy。这也意味着， 你可以为Collector接口提供自己的实现，从而自由地创建自定义归约操作。在6.6节中，我们 将展示如何实现Collector接口来创建一个收集器，来比先前更高效地将数值流划分为质数和非 质数
