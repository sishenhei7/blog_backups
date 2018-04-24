# underscore源码研究(1)

## 概述

很早就想研究underscore源码了，虽然underscore.js这个库有些过时了，但是我还是想学习一下库的架构，函数式编程以及常用方法的编写这些方面的内容，又恰好没什么其它要研究的了，所以就了结研究underscore源码这一心愿吧。

参考资料：underscore.js官方注释[](http://underscorejs.org/docs/underscore.html)，[undersercore 源码分析](https://yoyoyohamapi.gitbooks.io/undersercore-analysis/content/supply/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%A3%8E%E6%A0%BC%E7%9A%84%E6%94%AF%E6%8C%81.html)，[undersercore 源码分析 segmentfault](https://segmentfault.com/u/hanzichi/articles?page=2)

### void 0

虽然window的全局属性undefined是只读的，但是undefined可以在函数作用域内被改写。

所以一般用**void 0**代替undefined，另一方面也可以减少字节。

### 缓存方法

一直以来，我以为下面这段代码的作用只是**缓存方法**而已：

```
var ArrayProto = Array.prototype, 
    ObjProto = Object.prototype;
```

可是我错了，这段代码的真正意图是**便于压缩**。示例如下：

```
ArrayProto.slice.call(arr, parameter);
//上述代码一般被压缩为：
a.slice.call(arr, parameter);

//但是因为Array是关键字，下面的代码不能被压缩：
Array.prototype.slice.call(arr, parameter);
```

### 库的对象

很多库都会有自己的对象，比如jquery对象，underscore对象，初看起来非常神秘，但其实只是给它**挂载了一些方法**罢了。

```
//既起到了函数的作用，又起到了构造函数的作用
//当obj不是_的实例时，以obj为参数新建一个实例(underscore对象)
var _ = function (obj) {
  if (obj instanceof _) return obj;
  if (!(this instanceof _)) return new _(obj);
  this._wrapped = obj;
};
```

利用上述代码可以用```_([1, 2, 3])```的形式把```[1, 2, 3]```**变成一个underscore对象**，并利用下面的代码给这个对象添加方法：

```
//混入
_.mixin = function(obj) {
    _.each(_.functions(obj), function(name) {
        //直接挂载，使之能够函数式调用
        var func = _[name] = obj[name];
        //挂载到prototype上，使之能够OOP调用
        _.prototype[name] = function() {
            var args = [this._wrapped];
            push.apply(args, arguments);
            return chainResult(this, func.apply(_, args));
      };
    });
    return _;
};

_.mixin(_);

```

需要注意的是，underscore的所有方法都是先直接**挂载到_下面**，然后通过```_.mixin(_);```语句一并挂载到_.prototype下面使之能够OOP调用的。

值得一提的是，underscore也支持**函数式编程范式**：通过_.map()这样的形式直接调用map等方法。

### 判断浏览器还是服务器

underscore.js既可用于**浏览器**还可以用于**服务器**，它是用下面的代码判断浏览器和服务器的：

```
var root = typeof self == 'object' && self.self === self && self ||
            typeof global == 'object' && global.global === global && global ||
            this ||
            {};
```

然后用下面的代码把_挂载到全局变量上面：

```
if (typeof exports != 'undefined' && !exports.nodeType) {
  if (typeof module != 'undefined' && !module.nodeType && module.exports) {
      exports = module.exports = _;
  }
  exports._ = _;
} else {
  root._ = _;
}
```
















