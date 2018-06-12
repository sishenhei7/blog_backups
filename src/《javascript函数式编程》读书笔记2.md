## 概述

这是我读[《javascript函数式编程》](https://book.douban.com/subject/26579320/)的读书笔记，供以后开发时参考，相信对其他人也有用。

说明：虽然本书是**基于underscore.js库写的**，但是其中的理念和思考方式都讲的很好，**值得一读**。不过如果不熟悉underscore.js库的话，读起来会有点困难。

[《javascript函数式编程》读书笔记1](https://www.cnblogs.com/yangzhou33/p/9142606.html)

### 对象的不变性

函数式编程中函数是一等公民，所以对于数据来说，需要他们是**不可变的**。另外，对象的不可变确实能够**带来一些好处**，比如：如果数据是不可变的，那么可以直接通过“===”来判断数据是否发生变化，这使得react的效率得到极大提升。下面我们来讨论在js中实现immutable对象的方法。

### freeze

用Object.freeze可以冻结一个数组或者对象，当使用Object.freeze的时候，将导致后续的变化失败。如果在严格模式下使用，将抛出一个TypeError；否则，所有的变化将悄悄地失败。实例如下：

```
var a = [2,3,4];
a[1]; //输出3
Object.freeze(a);
a[1]=5;
a[1]; //输出3
```

也有一个isFrozen API来判断a是否确实被冻结。

```
Object.isFrozen(a); //输出true
```

但是这么实现有如下几个痛点：
1. 变化会**悄悄地失败**，可能会导致微妙的错误。
2. **不**是所有的库都**支持**freeze。
3. 这个是最重要的，Object.freeze是一个**浅操作**。

由于Object.freeze是一个浅操作，所以嵌套对象里面的变量不会被冻结，除非用递归进行**深冻结**，代码如下：

```
function isObject(obj) {
    var type = typeof obj;
    return type === 'function' || type === 'object' && !!obj;
}

function deepFreeze(obj) {
    if(!Object.isFrozen(obj)) Object.freeze(obj);
    for (var key in obj) {
        if(!obj.hasOwnProperty(key) || !isObject(obj)) continue;
        deepFreeze(obj[key];
    }
}
```

### 使用容器

我们也可以使用深复制，并把它放在一个容器里面来保持变量的不变性。简单代码如下：

```
function isObject(obj) {
    var type = typeof obj;
    return type === 'function' || type === 'object' && !!obj;
}

function deepClone(obj) {
    if(!isObject(obj)) return obj;
    var temp = new obj.constructor();
    for (var key in obj) {
        if(obj.hasOwnProperty(key))
            temp[key] = deepClone(obj[key]);
    }
    return temp;
}

function Container(init) {
    this.state = init;
}

Container.prototype = {
    setState: function(obj) {
        this.state = deepClone(obj);
        return this.state;
    }
}

var aObj = new Container({a:{b:1}, c:2});
aObj.setState({a:{b:3}, c:6, d:8});

aObj; //输出{a:{b:3}, c:6, d:8}
```

### 使用immutable.js

使用上面的方法实现不可变数据问题很多，用freeze会悄悄地失败，并且是浅操作，使用容器的话深复制对象会消耗大量的内存，并且速度很慢。

immutable.js就是**facebook开源**的为了解决这个问题的库。它可以说是和react一样的划时代的产物。它通过参考[hash maps tries](https://en.wikipedia.org/wiki/Hash_array_mapped_trie)和[hash maps tries](https://hypirion.com/musings/understanding-persistent-vector-pt-1)提供了一种优雅地实现javascript Immutable Data的方式。

它使用了**Structural Sharing（结构共享）**，即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享。所以避免了深拷贝带来的CPU 和内存的浪费。

具体可参考:
[搞定immutable.js详细说明](https://www.jb51.net/article/83373.htm)
[IMMUTABLE 详解](https://www.cnblogs.com/3body/p/6224010.html)


