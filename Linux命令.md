

### 常用命令

**Markdown Preview 快捷键**
支持预览实时渲染。(Ctrl + Shift + M)
支持Latex公式。(Ctrl + Shift + X)

**生成环境SSH转发**
本地8086 通过122服务器 转发到 206:8080
```
ssh -f root@192.168.1.122 -L 8086:172.16.0.206:8080 -N
```
-L 本地端口转发
-R 远程端口转发

**连接Zookeeper**
`zkCli -server 192.168.5.44:2181`

**查看系统版本信息**
cat /etc/redhat-release
**查看系统位数**
getconf LONG_BIT

**查看磁盘容量**
```
df -hl
```
**Apidoc 生成文档**
`apidoc -i ./ -o ./`

查看端口占用情况
`lsof -i:8080`

**shadowsocks服务启动命令**
ssserver -c /etc/shadowsocks.json -d start

ssh developer@192.168.3.163      4iD5wRki
ssh test@192.168.3.163      1234

/usr/local/bin/sshpass -p Maizijf2016 ssh -p60022 yangwenchang@192.168.1.21

/etc/resove.conf 修改后需要重启网络服务  /etc/init.d/network restart
dig openapi.stb.nonobank.com   查询dns解析记录    修改zone 文件后请使用  rndc reload 命令刷新

service bind9 restart  重启bind9服务


windows下  ping 和 nslookup 不一致，因为dns 没刷新   ipconfig /flushdns 刷新
linux   /etc/init.d/networking restart

**AB并发测试**
ab -n 1  -c 1 -p 'post.txt' -T 'application/json' 'http://127.0.0.1:8080/loan-app/lend/videoSubmitAudit'

**puppeteer安装**
设置
npm config set PUPPETEER_DOWNLOAD_HOST = https://npm.taobao.org/mirrors
