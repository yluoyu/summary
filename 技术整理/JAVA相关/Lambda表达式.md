
>利用行为参数化来传递代码有助于应对不断变化的需求


可以把Lambda表达式理解为简洁地表示可传递的匿名函数的一种方式:它没有名称，但它 有参数列表、函数主体、返回类型，可能还有一个可以抛出的异常列表
![](https://i.loli.net/2018/02/11/5a7fbbad43bfd.png)

Lambda表达式由参数、箭头和主体组成

Java 8中有效的Lambda表达式
![有效表达式](https://i.loli.net/2018/02/11/5a7fd1065acd8.png)

Lambda的基本语法是
    `(parameters) -> expression`
或(请注意语句的花括号)
    `(parameters) -> { statements; }`

以下哪个不是有效的Lambda表达式？
- (1) () -> {}
- (2) () -> "Raoul"
- (3) () -> {return "Mario";}
- (4) (Integer i) -> return "Alan" + i;
- (5) (String s) -> {"IronMan";}

答案:只有4和5是无效的Lambda。
(1) 这个Lambda没有参数，并返回void。它类似于主体为空的方法:public void run() {}。 (2) 这个Lambda没有参数，并返回String作为表达式。
(3) 这个Lambda没有参数，并返回String(利用显式返回语句)。

(4) return是一个控制流语句。要使此Lambda有效，需要使花括号，如下所示: (Integer i) -> {return "Alan" + i;}。
(5)“Iron Man”是一个表达式，不是一个语句。要使此Lambda有效，你可以去除花括号 和分号，如下所示:(String s) -> "Iron Man"。或者如果你喜欢，可以使用显式返回语 句，如下所示:(String s)->{return "IronMan";}。

### 在哪里以及如何使用Lambda
你可以在函数式接口上使用Lambda表达式

#### 函数式接口
函数式接口就是只定义一个抽象方法的接口

常见的函数式接口：
- java.util.Comparator
```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```
- java.lang.Runnable
- java.util.concurrent.Callable

接口现在还可以拥有默认方法(即在类没有对方法进行实现时， 其主体为方法提供默认实现的方法)。哪怕有很多默认方法，只要接口只定义了一个抽象 方法，它就仍然是一个函数式接口

Lambda表达式允许你直接以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例(具体说来，是函数式接口一个具体实现 的实例)。你用匿名内部类也可以完成同样的事情，只不过比较笨拙:需要提供一个实现，然后 再直接内联将它实例化。下面的代码是有效的，因为Runnable是一个只定义了一个抽象方法run 的函数式接口:
```java
Runnable r1 = () -> System.out.println("Hello World 1");
Runnable r2 = new Runnable(){
        public void run(){
            System.out.println("Hello World 2");
        }
};
```

#### 函数描述符
函数式接口的抽象方法的签名基本上就是Lambda表达式的签名。我们将这种抽象方法叫作函数描述符.
`() -> void`代表 了参数列表为空，且返回void的函数。这正是Runnable接口所代表的

**@FunctionalInterface**
这个标注用于表示该接口会设计成 一个函数式接口

#### 使用函数式接口

**一、Predicate**
`java.util.function.Predicate<T>`接口定义了一个名叫test的抽象方法，它接受泛型 T对象，并返回一个boolean

**二、Consumer**
`java.util.function.Consumer<T>`定义了一个名叫accept的抽象方法，它接受泛型T 的对象，没有返回(void)。你如果需要访问类型T的对象，并对其执行某些操作，就可以使用这个接口

**三、Function**
`java.util.function.Function<T, R>`接口定义了一个叫作apply的方法，它接受一个 泛型T的对象，并返回一个泛型R的对象。如果你需要定义一个Lambda，将输入对象的信息映射到输出，就可以使用这个接口(比如提取苹果的重量，或把字符串映射为它的长度)

**原始类型特化**

Java类型要么是引用类型(比如Byte、Integer、Object、List)，要么是原 始类型(比如int、double、byte、char)。但是泛型(比如Consumer<T>中的T)只能绑定到 引用类型。这是由泛型内部的实现方式造成的。1因此，在Java里有一个将原始类型转换为对应 的引用类型的机制。这个机制叫作装箱(boxing)。相反的操作，也就是将引用类型转换为对应的原始类型，叫作拆箱(unboxing)。Java还有一个自动装箱机制来帮助程序员执行这一任务:装 箱和拆箱操作是自动完成的

但这在性能方面是要付出代价的。装箱后的值本质上就是把原始类型包裹起来，并保存在堆 里。因此，装箱后的值需要更多的内存，并需要额外的内存搜索来获取被包裹的原始值

Java 8为我们前面所说的函数式接口带来了一个专门的版本，以便在输入和输出都是原始类 型时避免自动装箱的操作。比如，在下面的代码中，使用IntPredicate就避免了对值1000进行 装箱操作，但要是用Predicate<Integer>就会把参数1000装箱到一个Integer对象中
```java
public interface IntPredicate{
    boolean test(int t);
}
//true(无装箱)
IntPredicate evenNumbers = (int i) -> i % 2 == 0;
evenNumbers.test(1000);

//false(装箱)
Predicate<Integer> oddNumbers = (Integer i) -> i % 2 == 1;
oddNumbers.test(1000);
```
针对专门的输入参数类型的函数式接口的名称都要加上对应的原始类型前缀，比 如DoublePredicate、IntConsumer、LongBinaryOperator、IntFunction等。Function
 接口还有针对输出参数类型的变种:ToIntFunction<T>、IntToDoubleFunction等

 ### 类型检查、类型推断以及限制

 #### 类型检查

 Lambda的类型是从使用Lambda的上下文推断出来的。上下文(比如，接受它传递的方法的
参数，或接受它的值的局部变量)中Lambda表达式需要的类型称为目标类型

从**赋值的上下文、方法调用的上下文(参数和返回值)，以及类型转换的上下文中**获得目标类型

特殊的void兼容规则
如果一个Lambda的主体是一个语句表达式， 它就和一个返回void的函数描述符兼容(当
然需要参数列表也兼容)。例如，以下两行都是合法的，尽管List的add方法返回了一个 boolean，而不是Consumer上下文`(T -> void)`所要求的void
```
// Predicate返回了一个boolean
Predicate<String> p = s -> list.add(s);
// Consumer返回了一个void
Consumer<String> b = s -> list.add(s);
```

类型检查——为什么下面的代码不能编译呢?
`Object o = () -> {System.out.println("Tricky example"); };`
答案:Lambda表达式的上下文是Object(目标类型)。但Object不是一个函数式接口。
为了解决这个问题，你可以把目标类型改成Runnable，它的函数描述符是() -> void: Runnable r = () -> {System.out.println("Tricky example"); };

#### 类型推断

Java编译器会从上下文(目标类型)推断出用什么函数式接 口来配合Lambda表达式，这意味着它也可以推断出适合Lambda的签名，因为函数描述符可以通 过目标类型来得到。这样做的好处在于，编译器可以了解Lambda表达式的参数类型，这样就可 以在Lambda语法中省去标注参数类型，换句话说，Java编译器会像下面这样推断Lambda的参数类型
```
// 参数a没有显示给出类型
List<Apple> greenApples =filter(inventory, a ->"green".equals(a.getColor()));
```
注：当Lambda仅有一个类型需要推断的参数时，参数名称两边的括号也可以省略

```java
// 没有类型推断
Comparator<Apple> c =(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

// 有类型推断
Comparator<Apple> c =(a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
```

#### 使用局部变量
Lambda表达式 也允许使用自由变量(不是参数，而是在外层作用域中定义的变量)，就像匿名类一样。 它们被 称作捕获Lambda。例如，下面的Lambda捕获了portNumber变量
```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```
Lambda可以没有限 制地捕获(也就是在其主体中引用)实例变量和静态变量。但局部变量必须显式声明为final， 或事实上是final。换句话说，Lambda表达式只能捕获指派给它们的局部变量一次

下面的代码无法编译，因为portNumber 变量被赋值两次
```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
portNumber = 31337;
```

### 方法引用
方法引用让你可以重复使用现有的方法定义，并像Lambda一样传递它们
```java
//之前
inventory.sort((Apple a1, Apple a2)-> a1.getWeight().compareTo(a2.getWeight()));

//之后 使用方法引用和java.util.Comparator.comparing
inventory.sort(comparing(Apple::getWeight));
```

方法引用可以被看作仅仅调用特定方法的Lambda的一种快捷 写法。它的基本思想是，如果一个Lambda代表的只是“直接调用这个方法”，那最好还是用名称 来调用它，而不是去描述如何调用它

事实上，方法引用就是让你根据已有的方法实现来创建 Lambda表达式。但是，显式地指明方法的名称，你的代码的可读性会更好。它是如何工作的呢? 当你需要使用方法引用时，目标引用放在分隔符::前，方法的名称放在后面。例如， Apple::getWeight就是引用了Apple类中定义的方法getWeight。请记住，不需要括号，因为 10 你没有实际调用这个方法。方法引用就是Lambda表达式(Apple a) -> a.getWeight()的快捷 写法
Lambda  |  等效的方法引用
--|--
`(Apple a) -> a.getWeight()`  |  Apple::getWeight
`() -> Thread.currentThread().dumpStack()`  | Thread.currentThread()::dumpStack
`(str, i) -> str.substring(i)`  |  String::substring
`(String s) -> System.out.println(s)`  |  System.out::println
