## 概述

我在之前的博文([Performance面板看js加载](https://www.cnblogs.com/yangzhou33/p/9048839.html))中提到过，如果利用监听DOMContentLoaded事件的方式来加载js是不能优化加载的，不能够替代jquery中的**ready方法**，*原因是加载js的时候DOMContentLoaded事件还没有结束，自然不会发生页面渲染。*

于是我去看jquery的源码，发现jquery里面用到了异步。我灵机一动，对啊，如果利用异步把监听DOMContentLoaded事件来加载的js**放到任务队列**进行延迟那不就行了。于是我分别用setTimeout和promise的方式来实现，*竟然发生了不同的结果*，真是令人惊喜。我把过程记录下来，供以后开发时参考，相信对其他人也有用。

### 使用setTimeout

代码如下：

```
//haha.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="haha.css">
</head>
<body>
    <div id="haha"></div>
    <script type="text/javascript" src="haha.js"></script>
</body>
</html>

//haha.css
div {
    width: 800px;
    height: 800px;
    background-color: green;
    font-size: 300px;
}

//haha.js
document.addEventListener("DOMContentLoaded", function(event) {
  setTimeout(function() {
    var a = 1;
    while(a < 1000000000) {
        a++;
    }
  }, 0);
});
```

通过js查看首屏渲染时间，发现*非常完美的得到了优化*，而且效果和jquery里面的ready方法达到的效果差不多，nice。

有一点需要提出的是，setTimeout可以只加一个参数，因为第二个参数delay有一个**默认值，是0**。所以上面的haha.js里面的代码可以改写如下：

```
document.addEventListener("DOMContentLoaded", function(event) {
  setTimeout(function() {
    var a = 1;
    while(a < 1000000000) {
        a++;
    }
  });
});
```

如果把执行代码变成一个**模块**，也能完美的嵌入到上面的代码里面。

### 使用Promise

自然的，那使用es6里面的promise呢？代码如下：

```
//haha.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" type="text/css" href="haha.css">
</head>
<body>
    <div id="haha"></div>
    <script type="text/javascript" src="haha.js"></script>
</body>
</html>

//haha.css
div {
    width: 800px;
    height: 800px;
    background-color: green;
    font-size: 300px;
}

//haha.js
document.addEventListener("DOMContentLoaded", function(event) {
  const haha = new Promise( (resolve, reject)=>{
    resolve();
  } );
  haha.then(function() {
    var a = 1;
    while(a < 1000000000) {
        a++;
    }
  });
});
```

但是这一次，首屏时间*没有得到优化*。

通过复习一遍事件循环和任务队列的知识，可以解释这种情况：setTimeout是放在**macrotask queue**，这是这是**浏览器实现的任务队列**，并不是es6规范的任务队列；而promise是放在**microtask queue**里面，这才是**es6规范的任务队列**，每一个macrotask可以有许多小的microtask queue，并且当这次的macrotask执行完了之后再执行它下面的microtask queue，执行完microtask queue之后再执行下一个macrotask。

所以当我们用setTimeout的时候，会在**DOMContentLoaded事件，渲染事件之后**再加一个延迟事件，它是macrotask，与DOMContentLoaded事件和渲染事件同级，所以等DOMContentLoaded事件，渲染事件执行完之后才执行这个延迟的js。但是如果我们用promise的话，只会在DOMContentLoaded事件下的microtask queue塞入js，执行DOMContentLoaded事件完以后**不会执行渲染事件**，而是会先执行microtask queue。并且，通过看performance面板也可以看到，执行promise里面的代码的时候，DOMContentLoaded事件并没有结束，也是因为microtask queue是属于DOMContentLoaded事件的缘故。

### 其它

这个例子使我对macrotask queue和microtask queue的**理解加深**了许多！！！







