## 概述

很早就想研究underscore源码了，虽然underscore.js这个库有些过时了，但是我还是想学习一下库的架构，函数式编程以及常用方法的编写这些方面的内容，又恰好没什么其它要研究的了，所以就了结研究underscore源码这一心愿吧。

[underscore.js源码研究(1)](http://www.cnblogs.com/yangzhou33/p/8859331.html)
[underscore.js源码研究(2)](http://www.cnblogs.com/yangzhou33/p/8886992.html)

参考资料：underscore.js官方注释[](http://underscorejs.org/docs/underscore.html)，[undersercore 源码分析](https://yoyoyohamapi.gitbooks.io/undersercore-analysis/content/supply/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%A3%8E%E6%A0%BC%E7%9A%84%E6%94%AF%E6%8C%81.html)，[undersercore 源码分析 segmentfault](https://segmentfault.com/u/hanzichi/articles?page=2)

### 函数式编程

之前一直没搞懂为什么要用map而不用for循环，原来是因为map是函数式编程中的**迭代式思维**。

对于一个迭代来说，他至少由两个部分构成：**被迭代集合**和**当前迭代过程**。在underscore中，当前迭代过程就是一个函数，叫做**iteratee**，它会对被迭代集合进行处理。示例如下：

```
_.map = _.collect = function(obj, iteratee, context) {
    iteratee = cb(iteratee, context);
    var keys = !isArrayLike(obj) && _.keys(obj),
        length = (keys || obj).length,
        results = Array(length);
    for (var index = 0; index < length; index++) {
      var currentKey = keys ? keys[index] : index;
      results[index] = iteratee(obj[currentKey], currentKey, obj);
    }
    return results;
};
```

可以看到，iteratee函数会先经过cb方法进行处理，而cb方法是通过**optimizeCb**来处理一个函数的，原代码如下：

```
var cb = function(value, context, argCount) {
    if (_.iteratee !== builtinIteratee) return _.iteratee(value, context);
    if (value == null) return _.identity;
    if (_.isFunction(value)) return optimizeCb(value, context, argCount);
    if (_.isObject(value) && !_.isArray(value)) return _.matcher(value);
    return _.property(value);
};
```

optimizeCb的代码很长：

```
var optimizeCb = function(func, context, argCount) {
    if (context === void 0) return func;
    switch (argCount == null ? 3 : argCount) {
      case 1: return function(value) {
        return func.call(context, value);
      };
      case 3: return function(value, index, collection) {
        return func.call(context, value, index, collection);
      };
      case 4: return function(accumulator, value, index, collection) {
        return func.call(context, accumulator, value, index, collection);
      };
    }
    return function() {
      return func.apply(context, arguments);
    };
  };
```

虽然代码很长，但是optimizeCb方法只起了1个作用：把context这个上下文绑定在func函数上面。那些长长的case只是原样返回func不同参数的情形：func只**带一个参数**，比如times的迭代函数；func**带三个参数**，比如map,some等的迭代函数；func**带四个参数**，比如reduce等的迭代函数。

### 剩余参数的实现

我们常常有这样一个需求，就是想要使一个函数接受**多个参数**，但是在调用的时候可以不必输入全部参数，我们可以这样实现：

```
function add(x, y, z) {
    z = z == null ? 0 : z;
    return x + y + z;
}

add(1,2,0); //输出3
add(1,2); //输出3
```

但是如果我们想要使add能够接受无限多个参数呢？(这在函数式编程里面是非常正常的)我们可以把剩余的参数装在一个rest参数里面：

```
function add(x, rest) {
    return _.reduce(rest,function(accum, current){
      return accum+current;
    },x);
}

add(1, [2, 3]); //输出6
add(1, [2, 3, 4]); //输出10
add(1, [2, 3, 4, 5]); //输出15
```

所以问题变成了怎么把add进行封装使得```add(1,2,3,4)```会变成```add(1, [2,3,4])```的形式。

对此underscore利用了**restArguments方法**，源码和注释如下：

```
var restArguments = function(func, startIndex) {
    //不输入startIndex则自动取最后一个为rest
    startIndex = startIndex == null ? func.length - 1 : +startIndex;
    //接受一个函数为参数，返回一个包装后的函数，参数用arguments获取
    return function() {
      var length = Math.max(arguments.length - startIndex, 0),
          rest = Array(length),
          index = 0;
      for (; index < length; index++) {
        rest[index] = arguments[index + startIndex];
      }
      switch (startIndex) {
        //原函数只接受一个rest参数
        case 0: return func.call(this, rest);
        //原函数接受1个参数 + rest参数
        case 1: return func.call(this, arguments[0], rest);
        //原函数接受2个参数 + rest参数
        case 2: return func.call(this, arguments[0], arguments[1], rest);
      }
      var args = Array(startIndex + 1);
      for (index = 0; index < startIndex; index++) {
        args[index] = arguments[index];
      }
      args[startIndex] = rest;
      //原函数接受2个以上参数 + rest参数
      return func.apply(this, args);
    };
};
```

使用起来是这样的：

```
var addWithRest = _.restArguments(add, 1);
addWithRest(1,2,3,4); //10
```

值得一提的是，es6发明了一个语法，叫做**Rest parameters**。这种语法比上面的方法更加人性化，示例如下：

```
//...theArgs这种写法就是Rest parameters的写法
function sum(...theArgs) {
  return theArgs.reduce((previous, current) => {
    return previous + current;
  });
}

console.log(sum(1, 2, 3));
// expected output: 6

console.log(sum(1, 2, 3, 4));
// expected output: 10
```

**注意**：restArguments方法是最新加入的，cdn上的1.8.3 版本的underscore里面没有restArguments方法。





