## 概述

这是我看[《你不知道的JavaScript（中卷）》](https://book.douban.com/subject/26854244/)中关于**类型检查**的笔记，供以后开发时参考，相信对其他人也有用。

### typeof

我们知道js中有七种内置类型：undefined, null, string, boolean, object, number, symbol。

我们分别用typeof对它们一一进行检查：

```
typeof void(0)      //输出'undefined'
typeof null         //输出'object'
typeof 'haha'       //输出'string'
typeof true         //输出'boolean'
typeof {}           //输出'object'
typeof 3            //输出'number'
typeof (Symbol(2))  //输出'symbol'
```

可以看到，除了null值会被检查为object之外，其它的都显示正常。

那么怎么检查null值呢，方法是利用**所有的对象都是真值但是null是假值**这一特性：

```
var a = null;
!a && typeof a === 'object'; //true
```

### 函数

虽然函数并不是内置类型，但是它的typeof值与上面的任何一个都不同：

```
typeof function() {} //输出'function'
```

所以，当我们判断是否为对象的时候，**仅仅用typeof的值为object是不行的，还要加上typeof值为function的情况。**

值得一提的是，函数也有“**长度**”，它的length值是它的参数个数：

```
function haha() {
    return;
}

function jaja(a) {
    return;
}

function yaya(...args) {
    return args;
}

haha.length; //0
jaja.length; //1
yaya.length; //0
```

### 数组

数组也是一个特殊的对象，它和函数一样不是内置类型，但是和函数一样非常常用。那怎么判断一个值是否是数组呢？

最简单的方法是利用**Array.isArray()**方法：

```
Array.isArray([2, 3]); //true
Array.isArray({}); //false
```

但是isArray在有些浏览器里面不兼容，所以要写一个**polyfill代码**：

```
function isArray(obj) {
    return Object.prototype.toString.call(obj) === '[object Array]';
}
```

### Object.prototype.toString.call

正如上面的例子所示，**Object.prototype.toString.call**是另一个判断类型的好办法。比如判断boolean：

```
function isBoolean(obj) {
    return Object.prototype.toString.call(obj) === '[object Boolean]';
}
```

### typeof的防范机制

我们经常会先判断一个变量存不存在，然后再来使用这个变量：

```
if(debug) {
    console.log('Debugging is starting!');
}
```

但是如果debug这个变量**没有声明**的话，上面的语句就会报语法错误：Uncaught ReferenceError: debug is not defined。

怎么办呢？冒昧的去声明一个变量并不是一个很好的解决方法。这个时候就可以利用typeof的防范机制：**如果一个变量a未声明，那么typeof a不会报语法错误，而是会返回undefined**。

```
typeof hahahhhh; //'undefined'
```

所以一般把上面的代码改成下面的形式：

```
if(typeof debug !== 'undefined') {
    console.log('Debugging is starting!');
}
```




















