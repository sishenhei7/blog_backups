## 概述

很早就想研究underscore源码了，虽然underscore.js这个库有些过时了，但是我还是想学习一下库的架构，函数式编程以及常用方法的编写这些方面的内容，又恰好没什么其它要研究的了，所以就了结研究underscore源码这一心愿吧。

[underscore.js源码研究(1)](http://www.cnblogs.com/yangzhou33/p/8859331.html)
[underscore.js源码研究(2)](http://www.cnblogs.com/yangzhou33/p/8886992.html)
[underscore.js源码研究(3)](http://www.cnblogs.com/yangzhou33/p/8910945.html)
[underscore.js源码研究(4)](http://www.cnblogs.com/yangzhou33/p/8922700.html)
[underscore.js源码研究(5)](http://www.cnblogs.com/yangzhou33/p/8972394.html)
[underscore.js源码研究(6)](http://www.cnblogs.com/yangzhou33/p/8975205.html)
[underscore.js源码研究(7)](http://www.cnblogs.com/yangzhou33/p/8977090.html)
[underscore.js源码研究(8)](http://www.cnblogs.com/yangzhou33/p/8983245.html)

参考资料：[underscore.js官方注释](http://underscorejs.org/docs/underscore.html)，[undersercore 源码分析](https://yoyoyohamapi.gitbooks.io/undersercore-analysis/content/supply/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%A3%8E%E6%A0%BC%E7%9A%84%E6%94%AF%E6%8C%81.html)，[undersercore 源码分析 segmentfault](https://segmentfault.com/u/hanzichi/articles?page=2)

### 类型判断

对于类型判断，大部分元素可以通过**Object.prototype.toString**的结果进行判断，包括：Arguments, Function, String, Number, Date, RegExp, Symbol, Map, WeakMap, Set, WeakSet。实例如下：

```
//判断是否为函数，判断其他的只要把下面的Function改成其它的就可以了
function isFunction (obj) {
    return Object.prototype.toString.call(obj) === '[object Function]';
}

//建立一个函数
var haha = function haha() {
    consolog.log('haha');
}

console.log(isFunction(haha)); //输出true
console.log(isFunction(123)); //输出false
```

注意，IE9 以前的版本以及早期 V8 引擎对类型判断有一些**小bug**，underscore.js修复了这些问题，具体可自行查看[underscore.js源码](http://underscorejs.org/docs/underscore.html)。

#### isElement

判断是否为DOM节点，使用**nodeType属性**：

```
function isElement(obj) {
    return !!(obj && obj.nodeType === 1);
}
```

#### isArray

判断是否为数组，**优先使用Array.isArray**，不支持则使用Object.prototype.toString

```
var isArray = Array.isArray || function(obj) {
    return Object.prototype.toString.call(obj) === '[object Array]';
}
```

#### isObject

使用**typeof**来判断是否为对象。

```
function isObject(obj) {
    var type = typeof obj;
    return type === 'function' || type ==='object' && !!obj;
}
```

可以看到，函数会被判断为对象，空对象{}，undefined，null，NaN 等则不被认为是对象。

#### isFinite

判断是否有限。主要是使用js全局提供的**isFinite函数**来判断。

```
function isFinite(obj) {
    return !_.isSymbol(obj) && isFinite(obj) && !isNaN(parseFloat(obj));
}
```

最后那个!isNaN(parseFloat(obj))是为了排除bool值。

#### isNaN

判断是否为NaN，主要通过js全局提供的**isNaN**来判断。

```
function isNaN(obj) {
    return _.isNumber(obj) && isNaN(obj);
}
```

#### is Boolean

判断是否为Boolean。

```
function isBoolean(obj) {
    return obj === true || obj === false || Object.prototype.toString.call(obj) === '[object Boolean]';
}
```

#### isNull

判断是否是null，直接判断即可。

```
function isNull(obj) {
    return obj === null;
}
```

#### isUndefined

判断是否是undefined，利用void 0。

```
function isUndefined(obj) {
    return obj === void 0;
}
```

### 相等性判断

很多时候我们都要判断2个元素的相等性，在这个时候我们可以**先判断它们的类型，然后再判断它们的值。**可以有一些特殊的情况：
- 0 === -0
- null == undefined
- NaN != NaN
- NaN !== NaN

它们的解决方案如下：
- 1/0 === 1/0； 1/-0 !== 1/0
- null === null; null !== undefined;
- if(a !== a) return b !== b;

这里我不打算写一般的比较情况，只是把几个**特殊的情况**写一下：

#### 正则表达式和字符串

主要是**转化为字符串**再比较

```
Eq = function(a, b) {
    if (Object.prototype.toString.call(a) !== Object.prototype.toString.call(b)) return false;
    return '' + a === '' + b;
}
```

#### 数字

需要排除0和NaN的情况，并且**利用+a把a转化为数字**。

```
Eq = function(a, b) {
    if (Object.prototype.toString.call(a) !== Object.prototype.toString.call(b)) return false;
    return +a === 0 ? 1 / +a === 1 / b : +a === +b;
}
```

#### 日期和布尔值

**利用+a把a转化为数字**。

```
Eq = function(a, b) {
    if (Object.prototype.toString.call(a) !== Object.prototype.toString.call(b)) return false;
    return +a === +b;
}
```

#### Symbol

利用**SymbolProto.valueOf**方法。

```
Eq = function(a, b) {
    if (Object.prototype.toString.call(a) !== Object.prototype.toString.call(b)) return false;
    return SymbolProto.valueOf.call(a) === SymbolProto.valueOf.call(b);
}
```

注意：*不能直接用'=='或者'==='来判断对象的相等性*，原因如下：

```
var a = new Object();
var b = new Object();
a.name = "mm";
b.name = "mm";
console.log(a == b); //false
```




