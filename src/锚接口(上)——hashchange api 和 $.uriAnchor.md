# 锚接口(上)——hashchange api 和 $.uriAnchor

## 概述

这是我在[单页Web应用](https://book.douban.com/subject/25986284/)这本书上看到的方法，并深入的研究了一下，把结果记录在下面，供以后开发时参考，相信对其它人也有用。

说明一下，这个方法*已经过时*了，H5有更新的方法：history api，我们在锚接口(下)会讲到。

### 引子

自从接触单页面spa之后，我就对它的路由非常好奇：既然不是传统的利用文件夹存放路径的形式来路由的话，那到底单页面spa是怎么路由的呢？

更关键的是，复制url并在另一个页面打开，内容为什么不会变呢？

### 说明

1. url的hash值改变的时候，页面并**不会刷新**，这是唯一改变url而不刷新页面的方法。
2. 可以利用```window.location.hash```给url添上hash值。
3. url的hash值改变的时候，会触发一个**hashchange事件**，通过监听这个hashchange事件可以做一些处理。
4. 监听这个hashchange事件的时候可以通过```window.location.hash```获得url的hash值。

### 解决方案

#### SPA怎么路由

1. SPA切换页面的时候，记录哪些模块需要渲染，哪些不需要渲染，然后做成**hash值**。
2. SPA切换页面后，把这个hash值放在url中，生成一个**新的url**。
3. 由于url的hash值改变的时候，页面并不会刷新，所以切换页面时，**页面不刷新**。
4. 当浏览器按**后退键**的时候，url发生改变，因为是hash值改变，页面也不刷新。
5. 此时会触发一个**hashchange事件**。
6. 通过监听这个hashchange事件，**获得**此时的hash值。
7. 通过这个hash值可以知道哪些模块需要**渲染**，从而渲染他们。
8. 渲染时url没有发生改变，页面仍然**不刷新**。
9. 路由完成，整个过程页面不刷新。

#### 复制url并在另一个页面打开

由于页面加载的时候不触发hashchange事件，那复制url并在另一个页面打开的时候怎么获取hash里面的数据呢？

解决方案其实很简单，在页面加载的时候，用js**触发一次**hashchange事件即可。

### 例子

下面是我编写的一段测试代码供大家参考。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
    <script type="text/javascript">
    //自动生成数组
    function generateNum (num){
            var ret = [];
            for (var i = 0; i < num; i++) {
              ret.push(Math.ceil(100*Math.random()));
            };
            return ret;
          }
    //hashchange事件，如果有hash值，则输出到oDiv。
    function onHashchange() {
      var oDiv = document.getElementById("div1");
      console.log(window.location.hash.substring(1));
      if(window.location.hash.substring(1)) {
        oDiv.innerHTML = window.location.hash.substring(1);
      }
    }
    //页面加载
    window.onload=function (){
        //加载时触发一次hashchange事件
        $(window)
            .bind( 'hashchange', onHashchange )
            .trigger( 'hashchange' );
        //点击事件，把数组装在hash里面
        document.getElementById("input1").onclick=function(){
            window.location.hash = generateNum (6);
        }
    }
    </script>
</head>
<body>
    这是数组：
<div id="div1"></div>
<input type="button" value="生成随机数组" id="input1" />
</body>
</html>
```

### 其它

1. 这项技术不仅在SPA中有用处，而且在**翻页，商城选购**等地方也有用到，我就不一一举例了。
2. jQuery封装了```window.location.hash```方法，提供了更加便捷的方法：```$.uriAnchor```。主要是利用```$.uriAnchor.setAnchor()```方法来修改hash值，用```$.uriAnchor.makeAnchorMap()```方法来读取hash值。
3. 由于```window.location.hash```方法的**初衷**并不是做这个的，所以H5特别为这个写了一个api——history api，我会在锚接口(下)里面讲。














