## 概述

前几天研究了一个下开发者工具的performance面板，挺有意思的。**文件的加载顺序**又对页面性能有着至关重要的影响。所以我用performance面板研究了以下几种配置的加载顺序，把过程和结果记录下来，供以后开发时参考，相信对其他人也有用。
1. js放在body最后的加载。
2. js放在body前面的加载。
3. async，defer的加载。
4. setTimeout的加载。
5. onload事件的加载。
6. DOMContentLoaded事件的加载。
7. onreadystatechange事件的加载。
8. 加入jquery的加载。

### performance面板。

利用开发者工具的performance面板可以查看**首屏时间和加载情况**。方法是：
1. 打开chrome开发者工具的**performance面板**。
2. 点击左上角实心小圆点右边的**刷新图标**。（快捷键：Ctrl + Shift + E）

### js放在body最后

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
var a = 1;
while(a < 1000000000) {
  a++;
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_1_0.png)
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_0_1.png)

图一表示一直到2800ms才开始渲染首屏；图二表示css先加载，js后加载，他们是同时加载的，并且虽然js先加载完，但是并没有执行，而是等到css加载完并执行之后再执行的，这也符合了谁写在前面谁先执行的规范。

从第一张图可以得出，js的运行阻塞了首屏的渲染，一直到2800ms才开始渲染首屏。这表示js的运行阻塞了渲染。

从第二张图可以得出，css的下载没有阻塞js的下载，并且谁写在前面谁先执行。

### js放在body前面

代码如下：

```
//haha.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script type="text/javascript" src="haha.js"></script>
    <link rel="stylesheet" type="text/css" href="haha.css">
</head>
<body>
    <div id="haha"></div>
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
var a = 1;
while(a < 1000000000) {
  a++;
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_2_0.png)
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_0_2.png)

图一表示一直到2800ms才开始渲染首屏；图二表示css先加载，js后加载，他们是同时加载的，并且虽然js先加载完，但是并没有执行，而是等到css加载完并执行之后再执行的，这也符合了谁写在前面谁先执行的规范。

从第一张图可以得出，js的运行阻塞了首屏的渲染，一直到2800ms才开始渲染首屏。这符合我们的预期。

从第二张图可以得出，js的下载并没有阻塞css的下载，当js在下载的时候也会下载css。但为什么css的下载这么慢？？？因为js在下载完毕之后开始执行，在js执行的时候阻塞了css的下载！所以css的下载暂停了，一直等到js执行完毕之后再继续下载。

所以从上面2个实例可以得出：
1. js在下载的时候不会阻塞页面的下载和渲染(可能会阻塞渲染？)。
2. js在执行的时候会阻塞页面的下载和渲染。

另外，css的执行和下载不会阻塞任何事情。还有一点需要注意的是，js在执行的时候可能不会阻塞图片等资源的下载，而且根据浏览器的优化，js在执行的时候究竟会不会阻塞资源下载还是要看浏览器的处理，但是可以肯定的是会阻塞页面的渲染。

那么如何让js不阻塞页面的渲染，提高首屏时间呢？我们继续进行实例。

### defer

我们给script标签加上defer。代码如下：

```
//haha.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script defer type="text/javascript" src="haha.js"></script>
    <link rel="stylesheet" type="text/css" href="haha.css">
</head>
<body>
    <div id="haha"></div>
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
var a = 1;
while(a < 1000000000) {
  a++;
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_3_0.png)
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_0_3.png)

从第一张图中可以看出，首屏时间并没有变化。

从第二张图中可以看出，js并没有在加载完成之后立刻运行，而且css下载完成之后都没有运行，而是等到css执行完毕之后才运行。这和js放在body最下面的效果完全一样。

### async

我们给script标签加上async。代码如下：

```
//haha.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script async type="text/javascript" src="haha.js"></script>
    <script async type="text/javascript" src="haha2.js"></script>
    <link rel="stylesheet" type="text/css" href="haha.css">
</head>
<body>
    <div id="haha"></div>
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
var a = 1;
while(a < 1000000000) {
  a++;
}

//haha2.js
var a = 1;
while(a < 1000000000) {
  a++;
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_4_0.png)
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_0_4.png)

从第一张图中可以看出，首屏时间并没有变化。

从第二张图中可以看出，虽然haha2.js后加载，但是haha2.js先执行，并且都没有阻塞css的下载和执行，这代表都是在css下载和执行完毕之后才执行的。

### setTimeout

我们给haha.js加上setTimeout。代码如下：

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
setTimeout(function() {
  var a = 1;
  while(a < 1000000000) {
    a++;
  }
}, 2000);
```

performance面板部分截图如下：
![](https://images2018.cnblogs.com/blog/854981/201805/854981-20180520143422175-739082327.png)


可以看到，首屏时间得到极大优化，大约100ms就出现首屏，并且直到2000ms才开始执行js。

所以有很多人利用setTimeout进行延迟加载，延迟几秒后再通过js插入script标签进行加载js。但是这样有一个确定，就是我们精确地确定这个延迟时间。

### onload

我们给haha.js加上onload。代码如下：

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
window.onload = function () {
    var a = 1;
    while(a < 1000000000) {
      a++;
    }
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_6_0.png)

可以看到，不仅首屏时间得到优化，而且js很早就开始执行了，大约从55ms就开始加载js。

### DOMContentLoaded

那给haha.js加上DOMContentLoaded事件呢？。代码如下：

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
  var a = 1;
  while(a < 1000000000) {
      a++;
  }
});
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_7_0.png)

可以看到，虽然在DOMContentLoaded事件之后(大约51.5ms)立即执行js，但是这个时候DOMContentLoaded事件并没有结束，直到js执行完成之后才结束，然后才进行页面渲染。大大延迟了首屏时间。

### onreadystatechange

那给haha.js加上onreadystatechange事件呢？。代码如下：

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
document.onreadystatechange = function () {
    if(document.readyState === "complete") {
      var a = 1;
      while(a < 1000000000) {
        a++;
      }
    }
}
```

performance面板部分截图如下：
![图片描述](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_8_0.png)

可以看到，首屏时间得到优化，而且在readyState为complete之后(大约48ms)立即执行js，但是这个时候并不能判断DOM是否已经加载完成。

### 加入jquery

我先引入jquery再引入haha.js。代码如下：

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
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
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
var a = 1;
while(a < 1000000000) {
  a++;
}
```

performance面板部分截图如下：
![](https://images2018.cnblogs.com/blog/854981/201805/854981-20180520143357405-765165616.png)


可以看到，首屏时间奇迹般的得到了优化。就算我们并没有使用ready方法，但是也得到了优化。可能jquery里面进行了某些优化处理吧。

而且，经过试验，当haha.js里面用DOMContentLoaded事件时，首屏时间也奇迹般的得到了优化。所以我猜测，jquery对DOMContentLoaded事件进行了处理，让他提前结束？？？

### 总结
1. 谁先加载谁先执行，除非async。
2. defer没卵用。
3. 有jquery用jquery，没jquery用onload，千万别单独使用DOMContentLoaded。(其中的机制可要好好琢磨了。)
