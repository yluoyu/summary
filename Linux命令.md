

### 常用命令

**Markdown Preview 快捷键**
支持预览实时渲染。(Ctrl + Shift + M)
支持Latex公式。(Ctrl + Shift + X)

端口查看
`lsof -i:8080`

命令行下 输错命令 删除整行
```
ctrl + u //删除整行
ctrl + k //删除光标后面
ctrl + e //光标移动到行尾
ctrl + a //光标移动到行首
命令行太长的话，分两行输入
\Enter //（\加键盘回车）即可实现
```
Linux 使用退格键时出现^H解决方法
当我们再和脚本交互的时候，在终端上输错了内容，使用退格键，屏幕上会出现乱码，比如 H。H不是H键的意思，是backspace 解决方法：
- 要使用回删键（backspace）时，同时按住ctrl键
- 设定环境变量 在脚本的开头可结尾 参数 stty erase ^H stty erase ^?

**后台运行nohup**
`nohup command &`
如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件
`nohup command > myout.file 2>&1 &`

在当shell中提示了nohup成功后，还需要按终端上键盘任意键退回到shell输入命令窗口，然后通过在shell中输入exit来退出终端；如果在nohup执行成功后直接点关闭程序按钮关闭终端的话，这时候会断掉该命令所对应的session，导致nohup对应的进程被通知需要一起shutdown，起不到关掉终端后调用程序继续后台运行的作用
nohup是永久执行
**&是指在后台运行**
nohup 是指忽略挂起信号
就是指，用nohup运行命令可以使命令永久的执行下去，和用户终端没有关系，例如我们断开SSH连接都不会影响他的运行，注意了nohup没有后台运行的意思；&才是后台运行

&是指在后台运行，但当用户推出(挂起)的时候，命令自动也跟着退出

```
ping 192.168.3.176 &  // 可以看到不停在运行
fg  // 将后台中的命令调至前台继续运行
Ctrl + z //将一个正在前台执行的命令放到后台，并且处于暂停状态
bg // 将一个在后台暂停的命令，变成在后台继续执行

```

**生产环境SSH转发**
本地8086 通过122服务器 转发到 206:8080
`ssh -f root@192.168.1.122 -L 8086:172.16.0.206:8080 -N`
-L 本地端口转发
-R 远程端口转发

**连接Zookeeper**
`zkCli -server 192.168.5.44:2181`

**查看系统版本信息**
`cat /etc/redhat-release`
**查看系统位数**
`getconf LONG_BIT`

**查看磁盘容量**
`df -hl`

**Less命令**
-N 显示每行的行号
常见用法：
- `/` 向前搜索 使用一个模式进行搜索，并定位到下一个匹配的文本
- `?` 向后搜索 使用模式进行搜索，并定位到前一个匹配的文本
- n 向前查找下一个匹配的文本
- N 向后查找前一个匹配的文本
- j 向下移动一行
- k 向上移动一行
- G 移动到最后一行
- g 移动到第一行
- 空格 向下翻一页
- b 向上翻一页
- d 向下翻半页
- u 向上翻半页

less 版 tail -f
使用 less file-name 打开日志文件，执行命令 F，可以实现类似 tail -f 的效果


### **文件传输**
**sftp**
Secure Ftp
是一个基于SSH安全协议的文件传输管理工具。由于它是基于SSH的，会在传输过程中对用户的密码、数据等敏感信息进行加密，因此可以有效的防止用户信息

建立连接：`sftp user@host`
从本地上传文件：`put localpath`
下载文件：`get remotepath`
与远程相对应的本地操作，只需要在命令前加上”l” 即可，方便好记。

**scp (secure copy)**
- 复制local_file 到远程目录remote_folder下
```
scp local_file remote_user@host:remote_folder
```
- 复制local_folder 到远程remote_folder（需要加参数 -r 递归）

```
scp –r local_folder remote_user@host:remote_folder
```
**以上命令反过来写就是远程复制到本地**

**sz/rz**
sz/rz 是基于ZModem传输协议的命令。对传输的数据会进行核查，并且有很好的传输性能。使用起来更是非常方便，但前提是window端需要有能够支持ZModem的telnet或者SSH客户端，例如secureCRT

- 下载数据到本地下载目录：`sz filename1 filename2 ...`
- 上传数据到远程.执行rz –be 命令，客户端会弹出上传窗口，用户自行选择(可多选)要上传的文件即可

**Apidoc 生成文档**
`apidoc -i ./ -o ./`

查看端口占用情况
`lsof -i:8080`
列出打开文件（lists open files）”。切记，在Unix中一切（包括网络套接口）都是文件

#### 2>&1
linux中有三种标准输入输出，分别是STDIN，STDOUT，STDERR，对应的数字是0，1，2
STDIN是标准输入，默认从键盘读取信息；STDOUT是标准输出，默认将输出结果输出至终端；STDERR是标准错误，默认将输出结果输出至终端。
由于STDOUT与STDERR都会默认显示在终端上，为了区分二者的信息，就有了编号的0，1，2的定义，用1表示STDOUT，2表示STDERR
比如这样一条命令`php index.php task testOne >/dev/null 2>&1`
对于& 1 更准确的说应该是文件描述符 1,而1标识标准输出，stdout
对于2 ，表示标准错误，stderr
2>&1 的意思就是将标准错误重定向到标准输出。这里标准输出已经重定向到了 /dev/null。那么标准错误也会输出到/dev/null

```
ls 2>1测试一下，不会报没有2文件的错误，但会输出一个空的文件1；
ls xxx 2>1测试，没有xxx这个文件的错误输出到了1中；
ls xxx 2>&1测试，不会生成1这个文件了，不过错误跑到标准输出了；
ls xxx >out.txt 2>&1, 实际上可换成 ls xxx 1>out.txt 2>&1；重定向符号>默认是1,错误和输出都传到out.txt了
```

**shadowsocks服务启动命令**
`ssserver -c /etc/shadowsocks.json -d start`

ssh developer@192.168.3.163      4iD5wRki
ssh test@192.168.3.163      1234

/usr/local/bin/sshpass -p Maizijf2016 ssh -p60022 yangwenchang@192.168.1.21

/etc/resove.conf 修改后需要重启网络服务  /etc/init.d/network restart
dig openapi.stb.nonobank.com   查询dns解析记录    修改zone 文件后请使用  rndc reload 命令刷新

service bind9 restart  重启bind9服务

windows下  ping 和 nslookup 不一致，因为dns 没刷新   ipconfig /flushdns 刷新
linux   /etc/init.d/networking restart

**AB并发测试**
`ab -n 1  -c 1 -p 'post.txt' -T 'application/json' 'http://127.0.0.1:8080/loan-app/lend/videoSubmitAudit'`

**puppeteer安装**
设置
`npm config set PUPPETEER_DOWNLOAD_HOST = https://npm.taobao.org/mirrors`
