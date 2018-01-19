
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

    df -hl

**Apidoc 生成文档**

    apidoc -i ./ -o ./

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
Command +Option +O: 在浏览器中预览
Command+Option+X: 导出HTML
Ctrl+Alt+C: HTML标记拷贝至剪贴板

**puppeteer安装**
设置
npm config set PUPPETEER_DOWNLOAD_HOST = https://npm.taobao.org/mirrors

**Homebrew 命令**
brew info mysql 主要看具体的信息，比如目前的版本，依赖，安装后注意事项等
搜索：brew search mysql #搜索
brew update  # 更新 Homebrew 的信息
brew outdated # 看一下哪些软件可以升级
brew upgrade <xxx> # 如果不是所有的都要升级，那就这样升级指定的
brew upgrade;
brew cleanup #清理不需要的版本极其安装包缓存
brew cleanup git #清理单个已安装软件包的历史版本
brew home git #访问软件包官方站
brew list #显示已经安装的所有软件包
brew uninstall git #卸载软件包
brew install git #安装软件包(这里是示例安装的Git版本控制)
