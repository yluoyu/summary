#### php-fpm命令

php `5.3.3` 以后的php-fpm 不再支持 php-fpm 以前具有的 /usr/local/php/sbin/php-fpm (start|stop|reload)等命令，所以不要再看这种老掉牙的命令了，需要使用`信号控制`

```
ps -ef | grep php-fpm
502  1042     1   0  1:52下午 ??         0:00.48 /usr/local/opt/php56/sbin/php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf
502  3017  1042   0  2:24下午 ??         0:00.32 /usr/local/opt/php56/sbin/php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf
502  3018  1042   0  2:24下午 ??         0:00.00 /usr/local/opt/php56/sbin/php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf
502  3019  1042   0  2:24下午 ??         0:00.00 /usr/local/opt/php56/sbin/php-fpm --fpm-config /usr/local/etc/php/5.6/php-fpm.conf
```
上图中国 `1042`就是master进程pid
一般默认也存到 `/usr/local/var/run/php-fpm.pid` 文件中，php-fpm.conf中可配置

- `php-fpm &` 后台运行php-fpm
- 通过信息号控制php-fpm
  - `INT`, `TERM` 立即终止 `kill -TERM pid`
  - `QUIT`  平滑终止 `kill -QUIT pid`
  - `USR2` 平滑重载所有worker进程并重新载入配置和二进制模块

简单方式，不用查pid
```
关闭
kill -INT `cat /usr/local/php/var/run/php-fpm.pid`
重启
kill -USR2 `cat /usr/local/var/run/php-fpm.pid`
```

#### PHP语法
一、生成二维数组
```php
$c = array(
    "address" =>  "北京",
);
$rs = [];
array_push($rs,$c);
```

二、判读是否为一维数组
```php
if(count($arr) == count($arr,1)){
     echo "是一维";
}else{
     echo "不是一维";
}
```
三、判断数组中是否有指定值
`in_array($borrow['p_id'],array(77,88,83,87))?1:0;`

判断是否是数组
`if(is_array($u))`

#### Smart语法
用smarty时，获取数组的长度可以有以下几种方法:
- `{count($Arr)}`
- `{$Arr|@count}`
- `{$Arr|count}`
