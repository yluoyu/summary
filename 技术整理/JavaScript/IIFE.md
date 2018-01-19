
#### IIFE
有时需要在定义函数之后，立即调用该函数。这种函数就叫做立即执行函数，全称为立即调用的函数表达式IIFE(Imdiately Invoked Function Expression)

**javascript引擎规定，如果function关键字出现在行首，一律解释成函数声明语句**

**实现：**

函数跟随一对圆括号()表示函数调用

```javascript
//函数声明语句写法
function test(){};
test();

//函数表达式写法
var test = function(){};
test();
```

函数声明语句加上一对有值的圆括号，也仅仅是函数声明语句与不报错的分组操作符的组合而已


所以，解决方法就是不要让function出现在行首，让引擎将其理解成一个表达式

**最常用的两种办法**

```javascript
(function(){ /* code */ }());
(function(){ /* code */ })();
```

**其他写法**
```javascript
var i = function(){ return 10; }();
true && function(){ /* code */ }();
0, function(){ /* code */ }();

!function(){ /* code */ }();
~function(){ /* code */ }();
-function(){ /* code */ }();
+function(){ /* code */ }();

new function(){ /* code */ };
new function(){ /* code */ }();

```
