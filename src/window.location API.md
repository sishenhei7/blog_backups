# window.location API

## 概述

今天被自己鄙视了，竟然不会用window.location.search进行页面传值。现在好好总结下window.location API，记录一下供以后开发时参考，相信对其它人也有用。

### 页面传值

在浏览器窗口的url后面加入？和一串数字或者#和一串数字再看确认，一般情况下当前页面并不会发生变化。所以？和#都可以用来页面传值。

window.location.search能够返回url里面**？后面**的数据(包含？)，*页面传值和ajax经常用这个*。

window.location.hash能够返回url里面**#后面**的数据(包含#)，*锚点和路由经常用这个*。

### 页面跳转

其实window.location返回一个**只读的Location对象**，但是你仍然可以对它进行赋值，赋值其实是赋到href属性上面了。

```
//页面跳转，前三种效果一样，第四种不会把之前的页面保存在history里面
window.location.assign("http://www.mozilla.org")
window.location = "http://www.mozilla.org";
window.location.href = "http://www.mozilla.org";
window.location.replace("http://www.mozilla.org");

//页面刷新，下面二种效果一样
window.location.reload(true);
window.location.reload();
```

### 属性

window.location返回的Location对象有很多**属性**。

```
window.location.href //包含整个URL的字符串
window.location.protocol //整个URL的协议
window.location.host //整个URL的域名
window.location.port //整个URL的端口号
window.location.origin //包含页面来源的域名
window.location.search //包含URL参数的字符串
window.location.hash //包含块标识符的字符串
```

### document.location

document.location与window.location的异同点：
1. 无框架(frame)，那么是相同的。
2. 有框架(frame)，那么是不同的，框架里面的document.location肯定和外面的window.location不一样啊。

所以可以用window.location和top.location来**防止别人用frame引用你的网站**：

```
<script>
    top.location !== self.location || (top.location = self.location);
</script>
```
