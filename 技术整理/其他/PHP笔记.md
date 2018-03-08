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

#### Smart语法
用smarty时，获取数组的长度可以有以下几种方法:
- `{count($Arr)}`
- `{$Arr|@count}`
- `{$Arr|count}`
