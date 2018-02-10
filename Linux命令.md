

### 常用命令

**Markdown Preview 快捷键**
支持预览实时渲染。(Ctrl + Shift + M)
支持Latex公式。(Ctrl + Shift + X)

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
