# 异步三部曲之promise

## 概述

这是我看[你不知道的JavaScript（中卷）](https://book.douban.com/subject/26854244/)的读书笔记，供以后开发时参考，相信对其他人也有用。

### 例子

首先来看一个例子，如果我们要异步获取x和y，然后把他们打印出来，那么用回调可以编写代码如下：

```
ajax(urlX, function(x) {
    ajax(urlY, function(y){
        console.log(x + y);
    })
})
```

不得不说，上面的代码是很丑陋的，x和y的获取本来是**互不影响**的，但是现在y的获取需要依赖于x的完成，这导致了执行速度很慢。

所以我们把获取x和y的回调**分开写**：

```
var x, y;
ajax(urlX, function(xVal) {
    x = xVal;
    if(y !== undefined) {
        console.log(x + y);
    }
});
ajax(urlY, function(yVal) {
    y = yVal;
    if(x !== undefined) {
        console.log(x + y);
    }
});
```

上面的代码能达到要求，但是加入了全局变量x,y污染了全局空间，所以我们把他们**模块化**：

```
function add(urlX, urlY, cb) {
    var x, y;
    ajax(urlX, function(xVal) {
        x = xVal;
        if(y !== undefined) {
            cb(x, y);
        }
    });
    ajax(urlY, function(yVal) {
        y = yVal;
        if(x !== undefined) {
            cb(x, y);
        }
    });
}
add(urlX, urlY, function(x, y) {
    console.log(x + y);
})
```

可以看到，利用回调的方式代码**非常冗长**，并且在每一个ajax里面为了解决信任问题，都要做判断并且执行cb，代码有重复。下面来看看如果用promise的话，代码是怎样的：

```
function add(urlX, urlY) {
    var x = new Promise(function(resolve, reject) {
            ajax(urlX, resolve);
        }),
        y = new Promise(function(resolve, reject) {
            ajax(urlY, resolve);
        });
    return Promise.all([x, y]);
}
add(urlX, urlY).then(function(values) {
    console.log(values[0] + values[1]);
});
```

可以看到，全程**没有了参数判断**，这是因为Promise封装了依赖于时间的状态——等待底层值的完成和拒绝，所以Promise用起来是和时间无关的，不用关心时序了。

### Promise的状态

我们再来看看Promise到底是怎么封装依赖于时间的状态的。

一个Promise有以下几种状态：
- pending：初始状态，既不是成功，也不是失败状态。
- fulfilled：意味着操作完成。
- rejected：意味着操作失败。

下面是一个Promise实例：

```
var p = new Promise( function(resolve, reject){
    // resolve()用于完成
    // reject()用于拒绝
} );
```

Promise经过pending状态，会给出一个**决议**，如果这个决议是完成，会调用resolve函数，如果这个决议是拒绝，则会调用reject函数。

需要注意的是，resolve并不代表完成，因为Promise.resolve()这个API可能会返回一个决议为拒绝的Promise：

```
var rejectedTh = {
    then: function(resolved,rejected) {
        rejected( "Oops" );
    }
};
var rejectedPr = Promise.resolve( rejectedTh );
```

### 控制反转

在上面的例子中，还有一个细节，就是在使用promise的代码中，add函数里面没有任何回调函数！这就很爽了，实现了**关注点分离**，即在Promise里面我们并不需要关注回调函数(不需要关注回调函数是否存在，更不需要关注回调函数长什么样子)，在回调函数里面我们也不需要关注Promise(不需要关注Promise是否完成)。

其中的机制很简单，就是返回了一个**中间的Promise对象**，然后让这个中间Promise对象执行回调函数。

这也实现了控制反转，我们把控制权交给了中间Promise对象，再让这个中间对象控制回调函数。而不是直接把控制权交给回调函数。

### thenable

**鸭子类型**：如果它看起来像只鸭子，叫起来像只鸭子，那它一定就是只鸭子。

一个拥有then方法的对象，不管这个then方法在实例方法里面还是在原型方法里面，它都不是一个Promise，把它当做Promise调用可能会引起很大的错误。

```
var p = {
    then: function(cb,errcb) {
        cb( 42 );
        errcb( "evil laugh" );
    }
};
p.then(
    function fulfilled(val){
        console.log( val ); // 42
    },
    function rejected(err){
        // 啊，不应该运行！
        console.log( err ); // 邪恶的笑
    }
);
```

但是我们可以使用**Promise.resolve()**这个API将p转化为一个规范的Promise：

```
Promise.resolve( p ).then(
    function fulfilled(val){
        console.log( val ); // 42
    },
    function rejected(err){
    // 永远不会到达这里
    }
);
```

### 信任问题

现在来详细看下Promise怎么解决信任问题：
1. 调用回调过早；
2. 调用回调过晚（或不被调用）；
3. 调用回调次数过少或过多；
4. 未能传递所需的环境和参数；
5. 吞掉可能出现的错误和异常。

**调用回调过早**主要是担心一个任务有时同步完成，有时异步完成，这可能会导致竞态条件。但是由于Promise实现了关注点分离，所以Promise内部并不会调用回调函数，所以不会出现这种问题。而且，一旦Promise决议完成就会立即调用then方法，所以也不会有**调用过晚**的问题。

**回调不被调用**分为2种情况，一种是Promise决议完成但没有调用回调，很显然，then方法确保了一定会调用回调。另一种情况是Promise根本不被决议，因此也不会调用回调。可以用下面的代码解决这一问题：
```
// 用于超时一个Promise的工具
function timeoutPromise(delay) {
    return new Promise( function(resolve,reject){
        setTimeout( function(){
            reject( "Timeout!" );
        }, delay );
    } );
}
// 设置foo()超时
Promise.race( [
    foo(), // 试着开始foo()
    timeoutPromise( 3000 ) // 给它3秒钟
] ).then(
    function(){
    // foo(..)及时完成！
    },
    function(err){
    // 或者foo()被拒绝，或者只是没能按时完成
    // 查看err来了解是哪种情况
    }
);
```

*调用回调次数过少或过多*：由于调用一次then方法就一定只调用一次回调，因此不会出现这个问题，除非调用多次then方法。

*未能传递所需的环境和参数*：由于我们把参数封装在一个Promise里面，并且由于闭包原理，不可能会出现这个问题。需要注意的是，不要多次调用resolve()函数，否则后面的会自动被忽略，如果要传递多个值，就把他们封装在数组或对象里面再调用。

*吞掉可能出现的错误和异常*：then方法的第二个参数是错误处理函数，如果赋值的话，就不会吞掉可能出现的错误和异常。但是需要注意的是，then方法是处理上一个Promise的决议(完成或拒绝)。

### Promise的局限性

虽然Promise很好用，但是他还有很多局限性。

第一个是它很容易**忽略错误**。如果不在then的调用链中进行错误处理，错误就会被自动忽略，在结尾使用catch函数能处理这个问题。但是一旦then的调用链中嵌入了错误处理，catch函数就不会受到错误消息。

第二个是它只能传递**一个完成值**的，所以如果要传递多个决议的话，只能把多个决议进行封装，用的时候再进行拆解。（可能这就是为什么es6要发明解构的原因吧）

第三个是它是**单决议**的，所以遇到多次触发决议的情况（事件或数据流），只能通过多次构造Promise实例来实现了。

第四个是**代码风格**，如果代码中同时存在Promise风格和回调风格的话，会非常不易读。

第五个是决议**无法从内部取消**，只能通过Promise.race封装Promise和settimeout函数来解决了。

第六个是**性能**，因为Promise本身进行了很多异步处理，所以它比直接使用回调需要消耗更长的时间。所以对于小项目，还是使用回调更好。







