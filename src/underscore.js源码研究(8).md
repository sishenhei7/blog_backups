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

### 链式调用

对于一个对象的方法，我们可以在方法里面return this来使它支持**链式调用**。如果不修改原方法的源码，我们可以通过下面的函数使它支持链式调用。

```
//其中obj是一个对象，functions是对象的方法名数组
function chained(obj, functions) {
    //由于functions是一个数组，所以可以使用foreach
    functions.forEach((funcName) => {
        const func = obj[funcName];
        //修改原方法
        obj[funcName] = function() {
            func.apply(this, arguments);
            return this;
        }
    })
}
```

示例如下：

```
let speaker = {
    haha() {
        console.log('haha');
    },
    yaya() {
        console.log('yaya');
    },
    lala() {
        console.log('lala');
    }
}

chained(speaker, ['haha', 'lala', 'yaya']);
speaker.haha().yaya().haha().lala().yaya();
```

输出如下：

```
haha
yaya
haha
lala
yaya
```

### underscore.js里面的链式调用

underscore.js里面的链式调用与上面的稍微有些不同。

它的机制是这样的：
1. 把underscore.js的方法全部挂载到_下面。
2. 利用_.function方法遍历所有挂载在_下面的方法，然后挂载到_.prototype下面，这样生成的underscore对象就可以直接调用这些方法了。
3. 在把方法挂载到_.prototype下面的时候，会利用类似上面的函数对方法进行改写(增加return this)。
4. 在改写的时候建立一个标识_chain，来标识这个方法能不能链式调用。

下面来具体看看是怎么运作的：

首先，利用下面的函数，可以得到一个数组，这个数组里面全是挂载到_下面的**方法名字**。

```
_function = _.methods = function(obj) {
    var names = [];
    for( var key in obj) {
        if(_.isFunction(obj[key])) names.push(key);
    }
    return names.sort();
};
```

然后我们对每一个方法名字，把它**挂载到_.prototype下面**：

```
_.mixin = function(obj) {
    _.each(_.functions(obj), function(name) {
        var func = _[name] = obj[name];
        _.prototype[name] = function() {
            var args = [this._wrapped];
            push.apply(args, arguments);
            return chainResult(this, func.apply(_, args));
        };
    });
};
_.mixin(_);
```

从上面可以看到，在挂载的时候返回一个chainResult方法处理过的东西。而args就等于原对象+参数1+参数2+...组成的数组，所以```func.apply(_, args)```就是```_[func](原对象，参数1，参数2，...)```处理后的**结果**。

再来看看chainResult是怎么样的：

```
var chainResult = function(instance, obj){
    return instance._chain ? _(obj).chain() : obj;
};
```

它判断如果原对象能够链式调用，那么处理后的结果obj也能够链式调用。怎么让结果也能链式调用呢？答案是使用_.chain方法：

```
_chain = function(obj) {
    var instance = _(obj);
    instance._chain = true;
    return instance;
}
```

这个方法和我们最开始的chained方法差不多，但是它会把原对象转化为underscore对象，并且这个对象的**_chain属性为真**，即它能够被链式调用。

所以如果我们要在underscore.js中实现链式调用的话，直接用chain方法即可，实例如下：

```
_([1,2]).push(3).push(5) //输出4，并不是我们想要的
_([1,2]).chain().push(3).push(5).value() //输出[1, 2, 3, 5]，正是我们想要的
```

可以看到，我们上面用到了value()函数，它能够**中断链式调用**，并不返回指针而是返回值，代码如下：

```
_.prototype.value = function() {
    return this._wrapped;
}
```

等等，_wrapped又是什么？它是生成underscore对象前的原对象，看下面的代码：

```
var _ = function(obj) {
    if(obj instanceof _) return obj;
    if(!(this instanceof _)) return new _(obj);
    this._wrapped = obj;
}
```

也就是说，在使用_生成underscore对象的时候，把原对象储存在**_wrapped属性**里面了，所以_wrapped就是原对象。



