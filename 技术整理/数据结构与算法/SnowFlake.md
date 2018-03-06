转自：[理解分布式id生成算法SnowFlake](https://segmentfault.com/a/1190000011282426)

> 分布式id生成算法的有很多种，Twitter的SnowFlake就是其中经典的一种
SnowFlake算法生成id的结果是一个64bit大小的整数，它的结构如下图:
![](https://i.loli.net/2018/03/06/5a9e7bd585079.png)
- `1位` 不用。二进制中最高位为1的都是负数，但是我们生成的id一般都使用整数，所以这个最高位固定是0
- `41位` 用来记录时间戳（毫秒）
  - 41位可以表示2^41−1个数字
  - 如果只用来表示正整数（计算机中正数包含0），可以表示的数值范围是：0 至 2^41−1，减1是因为可表示的数值范围是从0开始算的，而不是1
  -也就是说41位可以表示2^41−1个毫秒的值，转化成单位年则是( 2^41−1)/(1000∗60∗60∗24∗365)=69年
- `12位` 序列号，用来记录同毫秒内产生的不同id
  - 12位（bit）可以表示的最大正整数是2^12−1=4096，即可以用0、1、2、3、....4095这4096个数字，来表示同一机器同一时间截（毫秒)内产生的4096个ID序号

由于在Java中64bit的整数是long类型，所以在Java中SnowFlake算法生成的id就是long来存储的。
SnowFlake可以保证：
- 所有生成的id按时间趋势递增
- 整个分布式系统内不会产生重复id（因为有datacenterId和workerId来做区分）

Java版Code:
```java
public class IdWorker{

    private long workerId;
    private long datacenterId;
    private long sequence;

    public IdWorker(long workerId, long datacenterId, long sequence){
        // sanity check for workerId
        if (workerId > maxWorkerId || workerId < 0) {
            throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0",maxWorkerId));
        }
        if (datacenterId > maxDatacenterId || datacenterId < 0) {
            throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0",maxDatacenterId));
        }
        System.out.printf("worker starting. timestamp left shift %d, datacenter id bits %d, worker id bits %d, sequence bits %d, workerid %d",
                timestampLeftShift, datacenterIdBits, workerIdBits, sequenceBits, workerId);

        this.workerId = workerId;
        this.datacenterId = datacenterId;
        this.sequence = sequence;
    }

    private long twepoch = 1288834974657L;

    private long workerIdBits = 5L;
    private long datacenterIdBits = 5L;
    private long maxWorkerId = -1L ^ (-1L << workerIdBits);
    private long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);
    private long sequenceBits = 12L;

    private long workerIdShift = sequenceBits;
    private long datacenterIdShift = sequenceBits + workerIdBits;
    private long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;
    private long sequenceMask = -1L ^ (-1L << sequenceBits);

    private long lastTimestamp = -1L;

    public long getWorkerId(){
        return workerId;
    }

    public long getDatacenterId(){
        return datacenterId;
    }

    public long getTimestamp(){
        return System.currentTimeMillis();
    }

    public synchronized long nextId() {
        long timestamp = timeGen();

        if (timestamp < lastTimestamp) {
            System.err.printf("clock is moving backwards.  Rejecting requests until %d.", lastTimestamp);
            throw new RuntimeException(String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds",
                    lastTimestamp - timestamp));
        }

        if (lastTimestamp == timestamp) {
            sequence = (sequence + 1) & sequenceMask;
            if (sequence == 0) {
                timestamp = tilNextMillis(lastTimestamp);
            }
        } else {
            sequence = 0;
        }

        lastTimestamp = timestamp;
        return ((timestamp - twepoch) << timestampLeftShift) |
                (datacenterId << datacenterIdShift) |
                (workerId << workerIdShift) |
                sequence;
    }

    private long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    private long timeGen(){
        return System.currentTimeMillis();
    }

    //---------------测试---------------
    public static void main(String[] args) {
        IdWorker worker = new IdWorker(1,1,1);
        for (int i = 0; i < 30; i++) {
            System.out.println(worker.nextId());
        }
    }

}
```
#### 代码理解
上面的代码中，有部分位运算的代码，如：
```java
sequence = (sequence + 1) & sequenceMask;

private long maxWorkerId = -1L ^ (-1L << workerIdBits);

return ((timestamp - twepoch) << timestampLeftShift) |
        (datacenterId << datacenterIdShift) |
        (workerId << workerIdShift) |
        sequence;
```
为了能更好理解，我对相关知识研究了一下:
**负数的二进制表示**
在计算机中，负数的二进制是用补码来表示的。
假设我是用Java中的int类型来存储数字的，
int类型的大小是32个二进制位（bit），即4个字节（byte）。（1 byte = 8 bit）
那么十进制数字3在二进制中的表示应该是这样的：
```
00000000 00000000 00000000 00000011
// 3的二进制表示，就是原码
```
那数字`-3`在二进制中应该如何表示？
我们可以反过来想想，因为-3+3=0，
在二进制运算中`把-3的二进制看成未知数x来求解`，
求解算式的二进制表示如下：
```
   00000000 00000000 00000000 00000011 //3，原码
+  xxxxxxxx xxxxxxxx xxxxxxxx xxxxxxxx //-3，补码
-----------------------------------------------
   00000000 00000000 00000000 00000000
```
反推x的值，3的二进制加上什么值才使结果变成00000000  00000000 00000000 00000000？：
```
   00000000 00000000 00000000 00000011 //3，原码
+  11111111 11111111 11111111 11111101 //-3，补码
-----------------------------------------------
1 00000000 00000000 00000000 00000000
```
反推的思路是3的二进制数从最低位开始逐位加1，使溢出的1不断向高位溢出，直到溢出到第33位。然后由于int类型最多只能保存32个二进制位，所以最高位的1溢出了，剩下的32位就成了（十进制的）`0`.
`补码的意义`就是可以拿补码和原码（3的二进制）相加，最终加出一个“溢出的0”
以上是理解的过程，实际中记住公式就很容易算出来：
- 补码 = 反码 + 1
- 补码 = （原码 - 1）再取反码

因此`-1`的二进制应该这样算：
```
00000000 00000000 00000000 00000001 //原码：1的二进制
11111111 11111111 11111111 11111110 //取反码：1的二进制的反码
11111111 11111111 11111111 11111111 //加1：-1的二进制表示（补码）
```
**用位运算计算n个bit能表示的最大数值**
比如这样一行代码：
```java
private long workerIdBits = 5L;
private long maxWorkerId = -1L ^ (-1L << workerIdBits);
```
上面代码换成这样看方便一点:
`long maxWorkerId = -1L ^ (-1L << 5L)`
咋一看真的看不准哪个部分先计算，于是查了一下Java运算符的优先级表:
![](https://i.loli.net/2018/03/06/5a9e7ef67a277.png)
所以上面那行代码中，运行顺序是：
- -1 左移 5，得结果a
- -1 异或 a

long maxWorkerId = -1L ^ (-1L << 5L)的二进制运算过程如下:
**-1 左移 5，得结果a** ：
```
      11111111 11111111 11111111 11111111 //-1的二进制表示（补码）
11111 11111111 11111111 11111111 11100000 //高位溢出的不要，低位补0
      11111111 11111111 11111111 11100000 //结果a
```
**-1 异或 a** ：
```
    11111111 11111111 11111111 11111111 //-1的二进制表示（补码）
^   11111111 11111111 11111111 11100000 //两个操作数的位中，相同则为0，不同则为1
---------------------------------------------------------------------------
    00000000 00000000 00000000 00011111 //最终结果31
```
最终结果是31
那既然现在知道算出来long maxWorkerId = -1L ^ (-1L << 5L)中的maxWorkerId = 31，有什么含义？为什么要用左移5来算？如果你看过概述部分，请找到这段内容看看：
> 5位（bit）可以表示的最大正整数是2^5−1=31，即可以用0、1、2、3、....31这32个数字，来表示不同的datecenterId或workerId

-1L ^ (-1L << 5L)结果是31，2^5−1的结果也是31，所以在代码中，-1L ^ (-1L << 5L)的写法是`利用位运算计算出5位能表示的最大正整数是多少`
**用mask防止溢出**:
有一段有趣的代码：
`sequence = (sequence + 1) & sequenceMask;`
分别用不同的值测试一下，你就知道它怎么有趣了：
```
long seqMask = -1L ^ (-1L << 12L); //计算12位能耐存储的最大正整数，相当于：2^12-1 = 4095
System.out.println("seqMask: "+seqMask);
System.out.println(1L & seqMask);
System.out.println(2L & seqMask);
System.out.println(3L & seqMask);
System.out.println(4L & seqMask);
System.out.println(4095L & seqMask);
System.out.println(4096L & seqMask);
System.out.println(4097L & seqMask);
System.out.println(4098L & seqMask);


/**
seqMask: 4095
1
2
3
4
4095
0
1
2
*/
```
这段代码通过`位与`运算保证计算的结果范围始终是 `0-4095` ！

**用位运算汇总结果**
还有另外一段诡异的代码:
```
return ((timestamp - twepoch) << timestampLeftShift) |
        (datacenterId << datacenterIdShift) |
        (workerId << workerIdShift) |
        sequence;
```
为了弄清楚这段代码
`首先` 需要计算一下相关的值：
```
private long twepoch = 1288834974657L; //起始时间戳，用于用当前时间戳减去这个时间戳，算出偏移量

private long workerIdBits = 5L; //workerId占用的位数：5
private long datacenterIdBits = 5L; //datacenterId占用的位数：5
private long maxWorkerId = -1L ^ (-1L << workerIdBits);  // workerId可以使用的最大数值：31
private long maxDatacenterId = -1L ^ (-1L << datacenterIdBits); // datacenterId可以使用的最大数值：31
private long sequenceBits = 12L;//序列号占用的位数：12

private long workerIdShift = sequenceBits; // 12
private long datacenterIdShift = sequenceBits + workerIdBits; // 12+5 = 17
private long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits; // 12+5+5 = 22
private long sequenceMask = -1L ^ (-1L << sequenceBits);//4095

private long lastTimestamp = -1L;
```
`其次` 写个测试，把参数都写死，并运行打印信息，方便后面来核对计算结果：
```java

    //---------------测试---------------
    public static void main(String[] args) {
        long timestamp = 1505914988849L;
        long twepoch = 1288834974657L;
        long datacenterId = 17L;
        long workerId = 25L;
        long sequence = 0L;

        System.out.printf("\ntimestamp: %d \n",timestamp);
        System.out.printf("twepoch: %d \n",twepoch);
        System.out.printf("datacenterId: %d \n",datacenterId);
        System.out.printf("workerId: %d \n",workerId);
        System.out.printf("sequence: %d \n",sequence);
        System.out.println();
        System.out.printf("(timestamp - twepoch): %d \n",(timestamp - twepoch));
        System.out.printf("((timestamp - twepoch) << 22L): %d \n",((timestamp - twepoch) << 22L));
        System.out.printf("(datacenterId << 17L): %d \n" ,(datacenterId << 17L));
        System.out.printf("(workerId << 12L): %d \n",(workerId << 12L));
        System.out.printf("sequence: %d \n",sequence);

        long result = ((timestamp - twepoch) << 22L) |
                (datacenterId << 17L) |
                (workerId << 12L) |
                sequence;
        System.out.println(result);

    }

    /** 打印信息：
        timestamp: 1505914988849
        twepoch: 1288834974657
        datacenterId: 17
        workerId: 25
        sequence: 0

        (timestamp - twepoch): 217080014192
        ((timestamp - twepoch) << 22L): 910499571845562368
        (datacenterId << 17L): 2228224
        (workerId << 12L): 102400
        sequence: 0
        910499571847892992
    */
```
代入位移的值得之后，就是这样：
```
return ((timestamp - 1288834974657) << 22) |
        (datacenterId << 17) |
        (workerId << 12) |
        sequence;
```
上面的管道符号`|`在Java中也是一个位运算符。其含义是：
x的第n位和y的第n位 只要有一个是1，则结果的第n位也为1，否则为0，因此，我们对四个数的位或运算如下：
对于尚未知道的值，我们可以先看看概述 中对SnowFlake结构的解释，再代入在合法范围的值(windows系统可以用计算器方便计算这些值的二进制)，来了解计算的过程。
左移运算是为了将数值移动到对应的段(41、5、5，12那段因为本来就在最右，因此不用左移)。然后对每个左移后的值(la、lb、lc、sequence)做位或运算，是为了把各个短的数据合并起来，合并成一个二进制数。最后转换成10进制，就是最终生成的id。
**扩展**：
在理解了这个算法之后，其实还有一些扩展的事情可以做：
1. 根据自己业务修改每个位段存储的信息。算法是通用的，可以根据自己需求适当调整每段的大小以及存储的信息。
2. 解密id，由于id的每段都保存了特定的信息，所以拿到一个id，应该可以尝试反推出原始的每个段的信息。反推出的信息可以帮助我们分析。比如作为订单，可以知道该订单的生成日期，负责处理的数据中心等等
