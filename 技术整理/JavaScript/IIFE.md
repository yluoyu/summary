
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

下面大神的解释：
```javascript
function foo() {...}     // 这是定义，Declaration；定义只是让解释器知道其存在，但是不会运行。

foo();                   // 这是语句，Statement；解释器遇到语句是会运行它的。
```

IIFE 并非必须，传统一点可以这么写：

```javascript
function foo() {...}
foo();
```

那么为什么要 IIFE？
1. 传统的方法啰嗦，定义和执行分开写；
2. 传统的方法直接污染全局命名空间（浏览器里的 global 对象，如 window）

于是，开发者们想找一个可以解决以上问题的写法。那么像下面这么写行不行呢？
`function foo(...){}();`
当然是不能，但是为什么呢？因为 function foo(...){} 这个部分只是一个声明，对于解释器来说，就好像你写了一个字符串 "function foo(...){}"，它需要使用解析函数，比如 eval() 来执行它才可以。所以把 () 直接放在声明后面是不会执行，这是错误的语法

如何把它变得正确？说起来也简单，只要把 **声明** 变成 **表达式（Expression）** 就可以了

实际上转变表达式的办法还是很多的，最常见的办法是把函数声明用一对 () 包裹起来，于是就变成了：
```javascript
(function foo() {...})    // 这里是故意换行，实际上可以和下面的括号连起来
();
```
这就等价于：
```javascript
var foo = function () {...};    // 这就不是定义，而是表达式了。
foo();
```
但是之前我们说不行的那个写法，其实也可以直接用括号包起来，这也是一种等价的表达式：
`(function foo(){...}());`

下面这几种也可以
`+function foo() {...}();`
```javascript
void function () {
    // 这里是真正需要的代码
}();
```
