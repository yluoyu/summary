### 安装
- `yum install python-pip`
- `pip install shadowsocks`

### 配置
- `vi /etc/shadowsocks.json`
```
{
    "server":"45.63.0.217",
    "port_password":{
        "8081":"123456",
        "8281":"123456",
        "8381":"123456",
        "8481":"123456"
     },
    "timeout":300,
    "method":"aes-256-cfb"
}
```
- 注意:`修改IP`

### 启动、停止
- `ssserver -c /etc/shadowsocks.json -d start`
- `ssserver -c /etc/shadowsocks.json -d stop`

### 防火墙
- 关闭防火墙 `systemctl stop firewalld.service`
- 开启防火墙 `systemctl start firewalld.service`
- 关闭开机启动 `systemctl disable firewalld.service`
- 开启开机启动 `systemctl enable firewalld.service`

### 加速
#### 方法一.安装锐速
- [CentOS6/7 专用破解版锐速一键安装脚本](https://www.vultrcn.com/7.html)

`uname -r`
- 结果以 2 开头，例如 2.6.32-696.18.7.el6.x86_64
  - 说明我们的服务器为 CentOS6 x64 系统
- 结果以 3 开头，例如 3.10.0-693.11.6.el7.x86_64
  - 这种输出结果说明我们的服务器为 CentOS7 x64 系统
- 结果以 4 开头，例如 4.12.10-1.el7.elrepo.x86_64
  - 输出结果说明我们的服务器已经安装 Google BBR 拥塞控制算法，此时已经无法继续安装锐速

- CentOS6 x64 系统安装锐速
  - `wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install '2.6.32-642.el6.x86_64'`
  - 要求输入配置选项时直接回去即可
- CentOS7 x64 系统安装锐速
  - `wget --no-check-certificate -O rskernel.sh https://raw.githubusercontent.com/uxh/shadowsocks_bash/master/rskernel.sh && bash rskernel.sh` 执行后会自动重启
  - `yum install net-tools -y && wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install`
  - 要求配置时 直接回车即可

#### 方法二 Google BBR 拥塞控制算法
- 原版
  - `wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh`
- 魔改版
  - `wget --no-check-certificate https://raw.githubusercontent.com/nanqinlang-tcp/tcp_nanqinlang/master/General/CentOS/bash/tcp_nanqinlang-1.3.2.sh && bash tcp_nanqinlang-1.3.2.sh`
