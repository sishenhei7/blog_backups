## 概述

这篇博文记录了用new Image()进行**预加载的总结**，供以后开发时参考，相信对其他人也有用。

### 旧的预加载

一般我们为了让背景图更快的加载，我们常常把背景图放在一个**display:none的img标签**里面进行预加载，如下面代码所示：

```
<div class="preload" style="display: none;">
    <img src="bg1.jpg" alt="缓存">
    <img src="bg2.jpg" alt="缓存">
    <img src="bg3.jpg" alt="缓存">
</div>
```

如果bg1.jpg用在background里面，就会等到**下载完css然后解析到background才会进行加载**，但是如果在html里面写上了上面的代码的话，就会在**加载完html并解析到上面代码的时候**直接加载。

### new Image()

利用上面的方法我们并不能控制图片什么时候加载，但是 如果用new Image()的话，就可以用js控制**在什么时候加载图片**，比如执行js的时候就加载啊，onload事件之后再加载啊，加载完页面之后2s再加载啊之类的。代码如下：

```
new Image().src = 'bg1.jpg';
new Image().src = 'bg2.jpg';
new Image().src = 'bg3.jpg';
```

### 加载其它资源

令人惊喜的是，利用new Image()不仅能够加载图片，还**能够加载css和js**，写法和上面差不多：

```
new Image().src = 'util.css';
new Image().src = 'haha.css';
new Image().src = 'util.js';
```

### 加载完成

如果需要的话，我们还可以加一个加载完成的事件，在加载完成的时候执行一个回调函数。如下面的代码所示：

```
let count = 0;
const a = new Image();
a.src = 'util.css';
const b = new Image();
b.src = 'haha.css';
const c = new Image();
c.src = 'util.js';

a.onload = function() {
    count++;
}

b.onload = function() {
    count++;
}

c.onload = function() {
    count++;
}
```

### 其它

值得一提的是，new Image()的方法在FF浏览器里面会有不同的实现，如果要兼容FF的话，需要作出一些调整，具体可以参考[js的new Image()做图片预加载](https://blog.csdn.net/lijian820708/article/details/40398601)。





