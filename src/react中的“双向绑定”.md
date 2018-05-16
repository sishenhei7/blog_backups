## 概述

React并**不是**一个MVVM框架，其实它连一个框架都算不上，它**只是一个库**，但是react生态系统中的**flux却是一个MVVM框架**，所以我研究了一下**flux官方实现**中的“双向绑定”，并记录下来供以后开发时参考，相信对其他人也有用。

参考资料：[如何监听 js 中变量的变化?](https://www.zhihu.com/question/44724640?sort=created)[Flux For Beginners](https://blog.andrewray.me/flux-for-stupid-people/)[数据双向绑定的分析和简单实现](https://zhuanlan.zhihu.com/p/25464162)[The ReactJS Controller View Pattern](https://blog.andrewray.me/the-reactjs-controller-view-pattern/)

### Controller View

React的核心思想是**Controller View设计模式**：顶层组件具有所有的state，并把它们作为props向下传递给子组件。

这样的好处有：
1. 如果要增加一个相同的子组件，直接增加即可。
2. 如果有url参数解析，直接从父组件得到，不需要单独重复处理。
3. 与可变的state相比，静态的props更加容易理解和预测。
4. 方便测试。（只需传入props即可）

这种设计模式实现的数据流动其实是一种**单向流动**，即从父组件流向子组件。如果子组件要向父组件传递数据，那么只能通过父组件把回调函数作为props传递给子组件，然后子组件通过调用这个回调函数来传递数据给父组件，这样就实现了父组件和子组件之间数据的**双向流动**。

然而，这个设计模式有一个缺点，就是需要**一层一层地**向下传递数据，如果层级很多的话，就特别麻烦，每个子组件接收的props不全是它需要的数据，还有很多它并不需要但是它的子组件需要的数据。在这种情况下就需要用到flux。

*注意：如果层级很少的话，就不建议使用flux或redux。*

### flux的双向绑定

我们知道数据的双向绑定是指：
1. view层的用户修改界面数据，model层的数据也会被修改。这个可以通过浏览器自带的事件响应来解决。
2. model层的数据修改会同步到view层画面的变化。这个时候就涉及到2个方面，一个是model层的数据会**渲染**到view层，通过react的数据流动即可实现；另一个是model层的**数据变化**会引起注意，在这个方面，angular是通过脏检测实现的，vue是通过es5的getter和setter以及Object.defineProperty方法（数据劫持）实现的，那么flux是怎么实现的呢？

flux是通过和浏览器类似的**事件响应**实现，通过事件监听数据的变化，如果有变化，就引发一个change事件，从而实现同步数据到view层。

事件监听其实是一种观察者模式，下面我们来具体讨论一下事件监听的实现。这个实现需要解决下列问题（以change事件为比方）：
1. 数据**具有**绑定change事件的方法。
1. 数据在change事件发生的时候**能够调用**绑定的回调函数。
2. 数据在改变的时候**能够触发**change事件。

首先我们要引入[MicroEvent.js](http://notes.jetienne.com/2011/03/22/microeventjs.html)，这个库只有一页代码，我选取其中重要的部分来讲解：

```
var MicroEvent  = function(){};
MicroEvent.prototype    = {
    bind    : function(event, fct){
        this._events = this._events || {};
        this._events[event] = this._events[event]   || [];
        this._events[event].push(fct);
    },
    unbind  : function(event, fct){
        this._events = this._events || {};
        if( event in this._events === false  )  return;
        this._events[event].splice(this._events[event].indexOf(fct), 1);
    },
    trigger : function(event /* , args... */){
        this._events = this._events || {};
        if( event in this._events === false  )  return;
        for(var i = 0; i < this._events[event].length; i++){
            this._events[event][i].apply(this, Array.prototype.slice.call(arguments, 1));
        }
    }
};
```

MicroEvent对象的实例有bind，unbind和trigger方法，分别对应绑定自定义事件，解绑，触发事件。这样通过**object.asign方法**就能把这些方法“给”数据。这就解决了第一个问题。

然后数据在通过bind绑定自定义事件及回调函数之后，可以通过**trigger触发**自定义事件并且依次执行绑定在事件上的回调函数。这就解决了第二个问题。

接下来是第三个问题，我们怎么在数据改变的时候触发事件？？？

在这一点上，vue是通过es5的getter和setter以及Object.defineProperty方法，通过重写setter函数，并在里面写上trigger事件的代码，实现数据在改变的时候能自动调用trigger方法，从而实现了触发事件。

但是flux用的并不是这种方法！！！我们先来看一下vue里面实现事件响应的过程，数据的**任何改动**都会触发Object.defineProperty绑定的setter方法，从而实现调用trigger方法。所以如果我们只定义**具体的改动**呢？这样是不是可以不用Object.defineProperty方法？

这就是flux的实现，我们并不是直接修改数据，而是通过**定义具体的动作**，通过这个动作修改数据。

```
AppDispatcher.dispatch({
    actionName: 'new-item',
    newItem: { name: 'Marco' } // example data
});
```

而这个动作在被调用的时候会自动触发trigger函数，从而实现事件响应！！！

这就是为什么flux里面要分为**Actions，Dispatcher，Store**的原因。我们并不是直接修改数据，而是通过一个**中间层Actions**修改数据，这样这个中间层在被调用的时候会触发trigger函数，实现事件响应！

### 其它

值得一提的是，node自带**Events库**，通过这个库也能够实现与上面的事件响应。但是MicroEvent.js适用性更广，它还能够适用于客户端。

另外，es6定义了新的双向绑定机制——**Proxy**。

Proxy就是**对象代理**，类似上面的中间层actions，它可以给一个对象绑定一个代理对象，通过这个代理对象来代理原对象的各种行为。

```
var p = new Proxy(target, handler);

let a = new Proxy({}, {
  set: function(obj, prop, value) {
    obj[prop] = value;

    if (prop === 'zhihu') {
      console.log("set " + prop + ": " + obj[prop]);
    }

    return true;
  }
});

a.zhihu = 100;
```

当然，Proxy的能力远不止此，还可以实现代理转发等等。

至于**兼容性**，Proxy的大部分方法都被各大浏览器实现了，只有少数几个方法没有被实现。具体可以看这里：[MDN Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。



















