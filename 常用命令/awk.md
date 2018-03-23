awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入(stdin)、一个或多个文件，或其它命令的输出。它支持用户自定义函数和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。awk有很多内建的功能，比如数组、函数等，这是它和C语言的相同之处，灵活性是awk最大的优势

awk命令格式和选项
```
awk [options] 'script' var=value file(s)
awk [options] -f scriptfile var=value file(s)
```
常用命令选项
- -F fs   fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:
- -v var=value   赋值一个用户定义变量，将外部变量传递给awk
- -f scripfile  从脚本文件中读取awk命令
- -m[fr] val   对val值设置内在限制，-mf选项限制分配给val的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用
