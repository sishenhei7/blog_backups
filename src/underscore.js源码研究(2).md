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

参考资料：underscore.js官方注释[](http://underscorejs.org/docs/underscore.html)，[undersercore 源码分析](https://yoyoyohamapi.gitbooks.io/undersercore-analysis/content/supply/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%A3%8E%E6%A0%BC%E7%9A%84%E6%94%AF%E6%8C%81.html)，[undersercore 源码分析 segmentfault](https://segmentfault.com/u/hanzichi/articles?page=2)

### 解决冲突

我们知道，_既是underscore的**函数也是对象**，我们把方法先挂载到它的对象属性上，使得方法可以直接调用，然后我们利用mixin方法把这些方法挂载到它的原型属性上，使得这个函数返回的underscore对象可以利用这些方法。

那么如果其它库也用到了_这个符号呢？underscore中是这样解决的：

```
var previousUnderscore = root._;

_.noConflict = function() {
    root._ = previousUnderscore;
    return this;
};

//给underscore重新定义一个符号：
var __ = _.noConflict();
```

### 内建方法

underscore这个库里面有一些私有的变量和方法，也会暴露出一些方法，这些暴露出的方法一旦被**用户改写**，可能整个库就不能按照希望的方向运行了。所以underscore把一些暴露的方法赋给一些内建方法，然后在其它方法使用这些方法的时候可以用内建方法判断是否被改写了。示例如下：

```
//声明内建方法
var builtinIteratee;

//双重赋值赋给内建方法
_.iteratee = builtinIteratee = function(value, context) {
return cb(value, context, Infinity);
};

//cb函数中判断iteratee方法是否被改写。
var cb = function(value, context, argCount) {
    if (_.iteratee !== builtinIteratee) return _.iteratee(value, context);
    if (value == null) return _.identity;
    if (_.isFunction(value)) return optimizeCb(value, context, argCount);
    if (_.isObject(value) && !_.isArray(value)) return _.matcher(value);
    return _.property(value);
};
```

值得一提的是，由于js中传递的是引用，所以赋给内建方法并不会有太大的内存开销，所以我觉得这个想法非常好。

### 创建对象

new Person()实际上为我们做了如下事情：

```
//创建一个对象，并设置其指向的原型
var obj = {'__proto__': Person.prototype};

//调用Person()的构造方法，并且将上下文(this)绑定到obj上
Person.apply(obj, arguments);

//返回创建的对象
return obj;
```

但是使用new**需要一个构造函数**，这在很多时候非常不方便。更多时候，我们希望**通过一个对象**直接创建另一个对象，于是就有了Object.create()方法。Object.create(obj)的原理是这样的：

```
//创建一个临时的构造函数，并将原型指向
var Ctor = function() {};
ctor.prototype = obj;

//通过new新建一个对象
var obj2 = new Ctor();

//清除Ctor，防止内存泄漏。并返回obj2
Ctor.prototype = null;
return obj2;
```

所以，**mdn上这样描述Object.create()方法**：创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。

需要注意的是，为了提升速度，underscore把Ctor进行了**缓存**：

```
var Ctor = function() {};
```

所以后面当我们为了防止内存泄漏清除Ctor的时候，只是清除它的原型。

而underscore的baseCreate方法就是对Object.create()方法的**polyfill**：

```
var baseCreate = function(prototype) {
    if (!_.isObject(prototype)) return {};
    //nativeCreate即是Object.create()方法
    if (nativeCreate) return nativeCreate(prototype);

    Ctor.prototype = prototype;
    var result = new Ctor;
    Ctor.prototype = null;

    return result;
};
```

需要注意的是，Object.create()方法支持多个参数，但是underscore的baseCreate方法只支持**一个参数**。









