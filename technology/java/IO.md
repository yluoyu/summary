## IO相关

### 字节流 字符流 File操作

###IO流体系

**InputStream & Reader**

- InputStream 和 Reader 是所有输入流的基类
- InputStream（典型实现：FileInputStream）
    + int read()
    + int read(byte[] b)
    + int read(byte[] b, int off, int len)
- Reader（典型实现：FileReader）
    + int read()
    + int read(char [] c)
    + int read(char [] c, int off, int len)
- 程序中打开的文件 IO 资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件 IO 资源

**OutputStream & Writer**

- OutputStream 和 Writer 也非常相似
    + void write(int b/int c);
    + void write(byte[] b/char[] cbuf);
    + void write(byte[] b/char[] buff, int off, int len);
    + void flush();
    + void close(); 需要先刷新，再关闭此流
- 因为字符流直接以字符作为操作单位，所以 Writer 可以用字符串来替换字符数组，即以 String 对象作为参数
    + void write(String str);
    + void write(String str, int off, int len);

**处理流之一：缓冲流**

- 为了提高数据读写的速度，Java API提供了带缓冲功能的流类，在使用这些流类时，会创建一个内部缓冲区数组
- 根据数据操作单位可以把缓冲流分为：
    + BufferedInputStream 和 BufferedOutputStream
    + BufferedReader 和 BufferedWriter
- 缓冲流要"套接"在相应的节点流之上，对读写的数据提供了缓冲的功能，提高了读写的效率，同时增加了一些新的方法
- 对于输出的缓冲流，写出的数据会先在内存中缓存，使用flush()将会使内存中的数据立刻写出

**处理流之二：转换流**

- 转换流提供了在字节流和字符流之间的转换
- Java API提供了两个转换流
    + InputStreamReader和OutputStreamWriter
- 字节流中的数据都是字符时，转成字符流操作更高效。
- InputStreamReader
    + 于将字节流中读取到的字节按指定字符集解码成字符。需要和InputStream"套接"
    + public InputStreamReader(InputStream in)
    + public InputSreamReader(InputStream in,String charsetName)
- OutputStreamWriter
    + 用于将要写入到字节流中的字符按指定字符集编码成字节。需要和OutputStream"套接"。
    + public OutputStreamWriter(OutputStream out)
    + public OutputStreamWriter(OutputStream out,String charsetName)

**处理流之三：标准输入输出流**

- System.in和System.out分别代表了系统标准的输入和输出设备
- 默认输入设备是键盘，输出设备是显示器
- System.in的类型是InputStream
- System.out的类型是PrintStream，其是OutputStream的子类FilterOutputStream 的子类
- 通过System类的setIn，setOut方法对默认设备进行改变。

**处理流之四：打印流**
- PrintStream(字节打印流)和PrintWriter(字符打印流)
    + 提供了一系列重载的print和println方法，用于多种数据类型的输出
    + System.out返回的是PrintStream的实例
    + PrintStream和PrintWriter有自动flush功能

**处理流之五：数据流**

- 为了方便地操作Java语言的基本数据类型的数据，可以使用数据流。
- 数据流有两个类：(用于读取和写出基本数据类型的数据）
    + DataInputStream 和 DataOutputStream
    + 分别"套接"在 InputStream 和 OutputStream 节点流上
- DataInputStream中的方法
    +  boolean readBoolean()
    +  byte readByte()
    +  char readChar()
    +  String readUTF()
    +  void readFully(byte[] b)
- DataOutputStream中的方法 与上面相对应

**处理流之六：对象流**

- ObjectInputStream和OjbectOutputSteam
- ObjectOutputStream和ObjectInputStream不能序列化static和transient修饰的成员变量

**对象序列化**

- 如果需要让某个对象支持序列化机制，则必须让其类是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一
    + Serializable
    + Externalizable
- 凡是实现Serializable接口的类都有一个表示序列化版本标识符的静态变量
    + private static final long serialVersionUID;
    + serialVersionUID用来表明类的不同版本间的兼容性
    + 如果类没有显示定义这个静态变量，它的值是Java运行时环境根据类的内部细节自动生成的。若类的源代码作了修改，serialVersionUID 可能发生变化。故建议，显示声明
- 显示定义serialVersionUID的用途
    + 希望类的不同版本对序列化兼容，因此需确保类的不同版本具有相同的serialVersionUID
    + 不希望类的不同版本对序列化兼容，因此需确保类的不同版本具有不同的serialVersionUID

**使用对象流序列化对象**
    
- 若某个类实现了 Serializable 接口，该类的对象就是可序列化的
    + 创建一个 ObjectOutputStream
    + 调用 ObjectOutputStream 对象的 writeObject(对象) 方法输出可序列化对象。注意写出一次，操作flush()
- 反序列化
    + 创建一个 ObjectInputStream
    + 调用 readObject() 方法读取流中的对象
- **强调**：如果某个类的字段不是基本数据类型或 String 类型，而是另一个引用类型，那么这个引用类型必须是可序列化的，否则拥有该类型的 Field 的类也不能序列化


**File操作 && 字节流**

    File file = new File("/Users/yangwenchang/testIO02"); // 这里并不会创建文件
    //FileInputStream inputStream = new FileInputStream(file); 这里会报错因为上面文件不存在
    f.createNewFile(); // 创建文件
    FileOutputStream outputStream = new FileOutputStream(file); // 这里如果file不存在的话会创建
    System.out.println(File.separator); // 目录分割符
    System.out.println(File.pathSeparator); // 路径分割符 一般为 ;
    f.delete(); // 删除
    String[] str=f.list(); // 列出目录全部文件名 包括隐藏文件
    File[] str=f.listFiles(); // 列出目录下全部文件
    outputStream.write("testI0".getBytes()); // 向文件写字节流
    //OutputStream out =new FileOutputStream(f,true); 追加方式打开

**字符流**

    Writer out =new FileWriter(f);
    out.write("Hello");
    out.close(); // 流关闭 必须注意
    System.getProperty("line.separator"); //获取换号符 \r 回车Carriage Return \n换行 New Line 
    //前者使光标到行首，后者使光标下移一格。通常用的Enter是两个加起来
    File f=new File(fileName);
    char[] ch=new char[100];
    Reader read=new FileReader(f);
    int temp=0;
    int count=0;
    while((temp=read.read())!=(-1)){
    ch[count++]=(char)temp;
    }
    read.close();

每读取一个自己我都要去用到FileInputStream，如果文件十分庞大，这样的操作效率会成为问题，所以引出了缓冲区的概念。可以将streamReader.read()改成streamReader.read(byte[]b)此方法读取的字节数目等于字节数组的长度，读取的数据被存储在字节数组中，返回读取的字节数，InputStream还有其他方法mark,reset,markSupported方法

markSupported 判断该输入流能支持mark 和 reset 方法。
mark用于标记当前位置；在读取一定数量的数据(小于readlimit的数据)后使用reset可以回到mark标记的位置。
FileInputStream不支持mark/reset操作；BufferedInputStream支持此操作

在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打10个字符。但是它有一个问题，就是打完一行换行的时候，要用去0.2秒，正好可以打两个字符。要是在这0.2秒里面，又有新的字符传过来，那么这个字符将丢失。 于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做“回车”，告诉打字机把打印头定位在左边界；另一个叫做“换行”，告诉打字机把纸向下移一行

计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧。 
Unix系统里，每行结尾只有“<换行>”，即“\n”；Windows系统里面，每行结尾是“<换行><回 车>”，即“\r\n”；Mac系统里，每行结尾是“<回车>”

**字节流和字符流区别**

实际上字节流在操作的时候本身是不会用到缓冲区的，是 **文件本身的直接操作**的，但是字符流在操作的 时候下后是会用到缓冲区的，是 **通过缓冲区来操作文件**的。

读者可以试着将上面的字节流和字符流的程序的最后一行关闭文件的代码注释掉，然后运行程序看看。你就会发现使用字节流的话，文件中已经存在内容，但是使用字符流的时候，文件中还是没有内容的，这个时候就要刷新缓冲区

**OutputStreramWriter 和InputStreamReader类**

整个IO类中除了字节流和字符流还包括字节和字符转换流。

- 1.OutputStreramWriter将输出的字符流转化为字节流
- 2.InputStreamReader将输入的字节流转换为字符流

但是不管如何操作，最后都是以字节的形式保存在文件中的

    File file=new File(fileName);
    Writer out=new OutputStreamWriter(new FileOutputStream(file)); // OutputStream To Writer
    out.write("hello");
    Reader read=new InputStreamReader(new FileInputStream(file)); // inputstream To Reader

**内存操作流**

前面列举的输出输入都是以文件进行的，现在我们以内存为输出输入目的地，使用内存操作流。内容操作流一般使用来生成一些临时信息采用的，这样可以避免删除的麻烦

- ByteArrayInputStream 主要将内容写入内存
- ByteArrayOutputStream 主要将内容从内存输出


代码举例

    String str="ROLLENHOLT";
    ByteArrayInputStream input=new ByteArrayInputStream(str.getBytes());
    ByteArrayOutputStream output=new ByteArrayOutputStream();
    int temp=0;
    while((temp=input.read())!=-1){
    char ch=(char)temp;
    output.write(Character.toLowerCase(ch));
    }
    String outStr=output.toString();

**管道流**

管道流主要可以进行两个线程之间的通信。

- PipedOutputStream 管道输出流
- PipedInputStream 管道输入流


测试管道

    /**
    * 消息发送类
    * 
    */
    class Send implements Runnable{
     private PipedOutputStream out=null;
     public Send() {
        out=new PipedOutputStream();
     }
     public PipedOutputStream getOut(){
        return this.out;
     }
     public void run(){
        String message="hello , Rollen";
        try{
            out.write(message.getBytes());
        }catch (Exception e) {
            e.printStackTrace();
        }try{
            out.close();
        }catch (Exception e) {
            e.printStackTrace();
        }
        }
    }

    /**
    * 接受消息类
    * */
    class Recive implements Runnable{
     private PipedInputStream input=null;
     public Recive(){
        this.input=new PipedInputStream();
     }
     public PipedInputStream getInput(){
        return this.input;
     }
     public void run(){
        byte[] b=new byte[1000];
        int len=0;
        try{
            len=this.input.read(b);
        }catch (Exception e) {
            e.printStackTrace();
        }try{
            input.close();
        }catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("接受的内容为 "+(new String(b,0,len)));
     }
    }

    /**
    * 测试类
    * */
    class hello{
     public static void main(String[] args) throws IOException {
        Send send=new Send();
        Recive recive=new Recive();
        try{
         //管道连接
            send.getOut().connect(recive.getInput());
        }catch (Exception e) {
            e.printStackTrace();
        }
        new Thread(send).start();
        new Thread(recive).start();
     }
    }

**格式化输出PrintStream**

    /**
     * 使用PrintStream进行输出
     * 并进行格式化
     * */
    import java.io.*;
    class hello {
     public static void main(String[] args) throws IOException {
     PrintStream print = new PrintStream(new FileOutputStream(new File("d:"
     + File.separator + "hello.txt")));
     String name="Rollen";
     int age=20;
     print.printf("姓名：%s. 年龄：%d.",name,age);
     print.close();
     }
    }

 - 使用OutputStream向屏幕上输出内容 OutputStream out=System.out; 
 - 输入输出重定向
 
 测试重定向

    System.setOut(new PrintStream(new FileOutputStream(file)))
    System.out.println("这些内容在文件中才能看到哦！");
    //System.setIn(new FileInputStream(file)); // 这个是测试输入重定向的

**BufferedReader**

BufferedWriter 和BufferedReader用文件级联的方式进行写入，即将多个文件写入到同一文件中（自带缓冲区的输出输出流BufferedReader和BufferedWriter,该流最常用的属readLine()方法了，读取一行数据，并返回String

BufferedReader只能接受字符流的缓冲区，因为每一个中文需要占据两个字节，所以需要将System.in这个字节输入流变为字符输入流，采用

    /**
    * 使用缓冲区从键盘上读入内容
    * */
    public class BufferedReaderDemo{
     public static void main(String[] args){
        BufferedReader buf = new BufferedReader(
        new InputStreamReader(System.in));
        String str = null;
        System.out.println("请输入内容");
        try{
        str = buf.readLine();
        }catch(IOException e){
        e.printStackTrace();
        }
        System.out.println("你输入的内容是：" + str);
        }
    } 

**Scanner类**
我们比较常用的是采用Scanner类来进行数据输入

    Scanner sca = new Scanner(System.in);
    int temp = sca.nextInt();
    //读取浮点数
    float flo=sca.nextFloat(); 

**数据操作流DataOutputStream、DataInputStream类**

    DataOutputStream out = null;
    out = new DataOutputStream(new FileOutputStream(file));
    out.writeChar('A');
    DataInputStream input = new DataInputStream(new FileInputStream(file));
    while((temp = input.readChar()) != 'C'){
        ch[count++] = temp;
    }

**合并流 SequenceInputStream**

SequenceInputStream主要用来将2个流合并在一起，比如将两个txt中的内容合并为另外一个txt

    InputStream input1 = new FileInputStream(file1);
    InputStream input2 = new FileInputStream(file2);
    OutputStream output = new FileOutputStream(file3);
    // 合并流
    SequenceInputStream sis = new SequenceInputStream(input1, input2);
    while((temp = sis.read()) != -1){
    output.write(temp);
    }

**文件压缩 ZipOutputStream类**

    InputStream input = new FileInputStream(file);
    ZipOutputStream zipOut = new ZipOutputStream(new FileOutputStream(zipFile));
    zipOut.putNextEntry(new ZipEntry(file.getName()));

**文件解压 ZipFile & ZipInputStream**

**回退流 PushBackInputStream**

    push = new PushbackInputStream(bat);
    push.unread(temp);
    temp = push.read();

**获取系统默认编码**

    System.out.println("系统默认编码为：" + System.getProperty("file.encoding"));

**序列化**
流允许读取或写入用户自定义的类，但是要实现这种功能，被读取和写入的类必须实现Serializable接口

ObjectInputStream和ObjectOutputStream

    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file));
    oos.writeObject(new Person("rollen", 20));

    ObjectInputStream input = new ObjectInputStream(new FileInputStream(file));
    Object obj = input.readObject();

**Externalizable**

被Serializable接口声明的类的对象的属性都将被序列化，但是如果想自定义序列化的内容的时候，就需要实现Externalizable接口。

当一个类要使用Externalizable这个接口的时候，这个类中必须要有一个无参的构造函数，如果没有的话，在构造的时候会产生异常，这是因为在反序列话的时候会默认调用无参的构造函数

    // 复写这个方法，根据需要可以保存的属性或者具体内容，在序列化的时候使用
    @Override
    public void writeExternal(ObjectOutput out) throws IOException{
    out.writeObject(this.name);
    out.writeInt(age);
    }
 
    // 复写这个方法，根据需要读取内容 反序列话的时候需要
    @Override
    public void readExternal(ObjectInput in) throws IOException,
    ClassNotFoundException{
    this.name = (String) in.readObject();
    this.age = in.readInt();
    }

**注意 部分字段不想序列化的除了自定义序列化之外 还可以使用transient 关键字**

**StreamTokenizer 类**

这个类非常有用，它可以把输入流解析为标记（token）, StreamTokenizer 并非派生自InputStream或者OutputStream，而是归类于io库中，因为StreamTokenizer只处理InputStream对象
使用StreamTokenizer来统计文件中的字符数


