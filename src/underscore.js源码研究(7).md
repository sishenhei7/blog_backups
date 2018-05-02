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

### 函数节流与去抖

有时候有这么一个情形，就是前段设计一个按钮，每点击一次这个按钮都会向服务器发送一次http请求，那么如果有恶意用户使用代码在1秒内点击100次，这样会对服务器造成难以想象的压力，可能导致服务器崩溃。

还有这么一个情形，前端有一个事件监听，需要监听鼠标移动的位置，然后执行一个回调函数，那么每次拖拽鼠标都会执行这个回调函数，如果这个回调函数引起了DOM的变化，则会引起重排重绘，严重影响了浏览器的性能。

这个时候就可以使用函数节流与去抖。

### 函数节流

函数节流就是限制在一定时间内的函数**执行次数**。

在讨论函数节流之前，我们先来说一下**函数独占**：如果函数正在执行中，则不允许再次执行函数。机制是通过设置一个flag来标记函数是否正在执行。

```
var isQuerying = false;

//complete是一个回调函数
var sendQuery = function(complete) {
    if(isQuerying) {
        //使用return来中断函数的执行
        return;
    }
    isQuerying = true;
    // 我们模拟一个耗时操作(回调)
    setTimeout(function(){
        complete && complete();
    },2000);
}

var complete = function() {
   // 在回调中， 我们刷新标记量
   isQuerying = false;
}

$("#queryBtn").click(function(){sendQuery(complete);});
```

再来看一下函数节流：限制在一定时间内的函数**执行次数**，多余的次数将被合并为一次并延迟waiting秒执行(节流)。

```
//cb是回调函数，waiting是在waiting时间段内只能执行一次
var throttle = function(cb, waiting) {
    var timeout;
    var previous = 0;
    var throttled = function() {
        var now = (new Date()).getTime();
        if(!previous) previous = now;
        var remaining = waiting - (now - previous);
        //如果在时间内已经执行过
        if(remaining >= 0) {
            //如果之前延迟过一个函数那就清除这个延迟不执行
            if(timeout) {
                clearTimeout(timeout);
                timeout = null;
            }
            //这里延迟函数执行时比如更新previous
            //underscore.js直接把这里独立为一个函数
            //即把原回调函数包裹为一个延迟执行函数
            timeout = setTimeout(function() {
                var now = (new Date()).getTime();
                previous = now;
                cb();
            }, waiting);
        } else {
            previous = now;
            cb();
        }
    }
    return throttled;
}
```

注意：这里的执行情况可以深入研究，多余的次数到底怎么执行是个问题。

### 函数去抖

函数去抖就是对于**一定时间段**的连续的函数调用，只让其执行一次。

下面是说明**函数节流(throttle)**和**函数去抖(debounce)**区别的很好的例子：
- 按一个按钮发送 AJAX：给 click 加了 debounce 后就算用户不停地点这个按钮，也只会最终发送一次；如果是 throttle 就会间隔发送几次。
- 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果是 throttle 的话，只要页面滚动就会间隔一段时间判断一次。

所以关键在于，延迟执行回调函数，如果在waiting时间内又调用了这个回调函数，则**刷新延迟时间**(取消上次的延迟，执行新的延迟)。与函数节流的关键区别在于，**没有remaining参数**。

代码如下：

```
//cb是回调函数，waiting是时间段内，immediate是是否第一次需要立即执行一次
var debounce = function(cb, waiting, immediate) {
    var timeout;
    var debounced = function() {
        if(timeout) clearTimeout(timeout);
        timeout = null;
        //如果需要第一次立即执行一次
        if(immediate && !timeout) cb();
        //无论如何都延迟执行一次
        timeout = setTimeout(cb, waiting);
    }
    return debounced;
}
```

测试代码如下：

```
var debounce = function(cb, waiting, immediate) {
    var timeout;
    var debounced = function() {
        if(timeout) clearTimeout(timeout);
        timeout = null;
        //如果需要第一次立即执行一次
        if(immediate && !timeout) cb();
        //无论如何都延迟执行一次
        timeout = setTimeout(cb, waiting);
    }
    return debounced;
}

function print() {
  console.log('hello world');
}

var debouncedPrint = debounce(print, 1000);

window.onscroll = function() {
  debouncedPrint();
};
```

### 对象属性和方法遍历

我们可以通过Object.prototype.hasOwnProperty来判断某个属性是不是自身属性(方法)，它**不会去寻找原型链上**的属性(方法)。

我们还可以通过Object.keys来列举出某个对象的所有自身属性(方法)，它也**不会去寻找原型链上**的属性(方法)。

但是for in除了寻找对象的自身属性(方法)外，还**会去寻找原型链上**的属性(方法)。

in不仅会寻找自身和原型上的可枚举属性，还会寻找**不可枚举属性**。

值得注意的是：
1. Object.prototype.hasOwnProperty，Object.keys和for in都只会寻找可枚举的属性(方法)，不会寻找不可枚举的，比如**toString方法**等。
2. 如果要区分属性和方法，加一个判断函数**isFunction**即可。

### Object函数

相信对于大多数人，都只会在new里面用到Object函数，其实也可以单独使用Object函数：

```
var obj = Object(obj);
```

它的意义是：
- 如果obj是对象，则返回这个对象。
- 如果obj是undefined或者null，则返回{}。
- 如果obj是原始值，则返回对象包裹的原始值。

示例如下：

```
console.log(Object({a:2})); //{a: 2}
console.log(Object(null)); //{}
console.log(Object(2)); //{[[PrimitiveValue]]: 2}
console.log(Object(2) + 1); //3
```

