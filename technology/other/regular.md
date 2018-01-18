### 正则表达式

正则表达式(Regular Expression)是一种文本模式，包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为"元字符"）

#### 语法

##### 普通字符
普通字符包括没有显式指定为元字符的所有可打印和不可打印字符。这包括**所有大写和小写字母、所有数字、所有标点符号和一些其他符号**。
##### 非打印字符

字符  |  描述
--|--
\cx  |  匹配由x指明的控制字符 使用较少
\f  |  匹配一个换页符
\n  |  匹配一个换行符
\r  |  匹配一个回车符
\s  |  匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]
\S  |  匹配任何非空白字符
\t	|  匹配一个制表符
\v  |  匹配一个垂直制表符

##### 特殊字符
所谓特殊字符，就是一些有特殊含义的字符，如上面说的 runoo*b 中的 *，简单的说就是表示任何字符串的意思。如果要查找字符串中的 * 符号，则需要对 * 进行转义，即在其前加一个 \
字符  |  描述
--|--
$  |  匹配输入字符串的结尾位置
( )  |  标记一个子表达式的开始和结束位置。子表达式可以获取供以后使用。要匹配这些字符，请转义
*  | 匹配前面的子表达式零次或多次
+  | 匹配前面的子表达式一次或多次
.  | 匹配除换行符 \n 之外的任何单字符
[  | 标记一个中括号表达式的开始
?  |  匹配前面的子表达式零次或一次
\  |  将下一个字符标记为或特殊字符、或原义字符、或向后引用、或八进制转义符
^  |  匹配输入字符串的开始位置，除非在方括号表达式中使用，此时它表示不接受该字符集合
{  |  标记限定符表达式的开始
\|  |  指明两项之间的一个选择

**限定符**
限定符用来指定正则表达式的一个给定组件必须要出现多少次才能满足匹配。有 * 或 + 或 ? 或 {n} 或 {n,} 或 {n,m} 共6种

字符  |  描述
--|--
*  |  匹配前面的子表达式零次或多次
+  |  匹配前面的子表达式一次或多次
?  |  匹配前面的子表达式零次或一次
{n}  |  n 是一个非负整数。匹配确定的 n 次
{n,}  |  n 是一个非负整数。至少匹配n 次
{n,m}  |  m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次

***、+限定符都是贪婪的，因为它们会尽可能多的匹配文字，只有在它们的后面加上一个?就可以实现非贪婪或最小匹配**

**定位符**

定位符使您能够将正则表达式固定到行首或行尾。它们还使您能够创建这样的正则表达式，这些正则表达式出现在一个单词内、在一个单词的开头或者一个单词的结尾。

定位符用来描述字符串或单词的边界，^ 和 $ 分别指字符串的开始与结束，\b 描述单词的前或后边界，\B 表示非单词边界

字符  |  描述
--|--
^  |  匹配输入字符串开始的位置
$  |  匹配输入字符串结尾的位置
\b  |  匹配一个字边界，即字与空格间的位置
\B  |  非字边界匹配

**选择**

用圆括号将所有选择项括起来，相邻的选择项之间用|分隔。但用圆括号会有一个副作用，使相关的匹配会被缓存，此时可用?:放在第一个选项前来消除这种副作用
其中 ?: 是非捕获元之一，还有两个非捕获元是 ?= 和 ?!，这两个还有更多的含义，前者为正向预查，在任何开始匹配圆括号内的正则表达式模式的位置来匹配搜索字符串，后者为负向预查，在任何开始不匹配该正则表达式模式的位置来匹配搜索字符串。

**反向引用**
对一个正则表达式模式或部分模式两边添加圆括号将导致相关匹配存储到一个临时缓冲区中，所捕获的每个子匹配都按照在正则表达式模式中从左到右出现的顺序存储。缓冲区编号从 1 开始，最多可存储 99 个捕获的子表达式。每个缓冲区都可以使用 \n 访问，其中 n 为一个标识特定缓冲区的一位或两位十进制数。

表达式在匹配时，表达式引擎会将小括号 "( )" 包含的表达式所匹配到的字符串记录下来。在获取匹配结果的时候，小括号包含的表达式所匹配到的字符串可以单独获取

可以使用非捕获元字符 ?:、?= 或 ?! 来重写捕获，忽略对相关匹配的保存
反向引用的最简单的、最有用的应用之一，是提供查找文本中两个相同的相邻单词的匹配项的能力。以下面的句子为例

```
Is is the cost of of gasoline going up up?

/\b([a-z]+) \1\b/ig
```


捕获的表达式，正如 [a-z]+ 指定的，包括一个或多个字母。正则表达式的第二部分是对以前捕获的子匹配项的引用，即，单词的第二个匹配项正好由括号表达式匹配。\1 指定第一个子匹配项。

字边界元字符确保只检测整个单词。否则，诸如 "is issued" 或 "this is" 之类的词组将不能正确地被此表达式识别。

正则表达式后面的全局标记 g 指定将该表达式应用到输入字符串中能够查找到的尽可能多的匹配。

表达式的结尾处的不区分大小写 i 标记指定不区分大小写。

多行标记指定换行符的两边可能出现潜在的匹配。

反向引用还可以将通用资源指示符 (URI) 分解为其组件。假定您想将下面的 URI 分解为协议（ftp、http 等等）、域地址和页/路径：

**预搜索，不匹配；反向预搜索，不匹配**
正向预搜索："(?=xxxxx)"，"(?!xxxxx)"   格式："(?=xxxxx)"，在被匹配的字符串中，它对所处的 "缝隙" 或者 "两头" 附加的条件是：所在缝隙的右侧，必须能够匹配上 xxxxx这部分的表达式
因为它只是在此作为这个缝隙上附加的条件，所以它并不影响后边的表达式去真正匹配这个缝隙之后的字符。这就类似 "/b"，本身不匹配任何字符。"/b" 只是将所在缝隙之前、之后的字符取来进行了一下判断，不会影响后边的表达式来真正的匹配

#### 元字符
除了之前的 \ ^ $ * + ? {n} {n,} {n,m} . 之外还有下面这些

字符  |  描述
--|--
(pattern)  |匹配 pattern 并获取这一匹配
(?:pattern)  | 匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用
(?=pattern)  | 正向肯定预查（look ahead positive assert），在任何匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配
(?!pattern)  |  正向否定预查(negative assert)，在任何不匹配pattern的字符串开始处匹配查找字符串
(?<=pattern)  |  反向(look behind)肯定预查，与正向肯定预查类似，只是方向相反
x|y  |  匹配 x 或 y
[xyz]  |  字符集合 匹配所包含的任意一个字符
[^xyz]  |  负值字符集合。匹配未包含的任意字符
[a-z]  |  字符范围。匹配指定范围内的任意字符
[^a-z]  |  负值字符范围。匹配任何不在指定范围内的任意字符
\d  |  匹配一个数字字符。等价于 [0-9]
\D  |  匹配一个非数字字符。等价于 [^0-9]
\w  |  匹配字母、数字、下划线。等价于'[A-Za-z0-9_]'
\W  |  匹配非字母、数字、下划线。等价于 '[^A-Za-z0-9_]'
\xn  |  匹配 n，其中 n 为十六进制转义值
\num  |  匹配 num，其中 num 是一个正整数。对所获取的匹配的引用。例如，'(.)\1' 匹配两个连续的相同字符。
\un  |  匹配 n，其中 n 是一个用四个十六进制数字表示的 Unicode 字符。例如， \u00A9 匹配版权符号 (?)

#### 运算符优先级

正则表达式从左到右进行计算，并遵循优先级顺序，这与算术表达式非常类似

相同优先级的从左到右进行运算，不同优先级的运算先高后低。下表从最高到最低说明了各种正则表达式运算符的优先级顺序：

运算符  |  描述
--|--
\  |  转义符
(), (?: ), (?=), []  |  圆括号和方括号
*, +, ?, {n}, {n,}, {n,m}  |  限定符
^, $, \任何元字符、任何字符  |  定位点和序列（即：位置和顺序）
\|  |  "或"操作

#### 常用表达式

**校验密码强度**
密码的强度必须是包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间。
```
^(?=.*\\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$
```
**校验中文**
字符串仅能是中文
```
^[\\u4e00-\\u9fa5]{0,}$
```

**由数字、26个英文字母或下划线组成的字符串**
```
^\\w+$
```
**校验E-Mail 地址**

```
[\\w!#$%&'*+/=?^_`{|}~-]+(?:\\.[\\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\\w](?:[\\w-]*[\\w])?\\.)+[\\w](?:[\\w-]*[\\w])?
```
**校验身份证号码**

15位：
```
^[1-9]\\d{7}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}$
```
18位：
```
^[1-9]\\d{5}[1-9]\\d{3}((0\\d)|(1[0-2]))(([0|1|2]\\d)|3[0-1])\\d{3}([0-9]|X)$
```

**校验金额**
金额校验，精确到2位小数

^[0-9]+(.[0-9]{2})?$