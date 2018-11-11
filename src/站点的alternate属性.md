## 概述

今天看决战平安京官网源码，突然看到了rel的alternate属性，百度了一下，记录下来，供以后开发时参考，相信对其他人也有用。

### PC端rel

在pc版网页上，添加指向对应移动版网址的特殊链接**rel="alternate"标记**，这有助于百度发现网站的移动版网页所在的位置。代码如下：

```
<link rel="alternate" media="only screen and(max-width: 640px)" href="//moba.163.com/m/">
```

后面的href是移动端网址。

### 移动端rel

在移动版网页上，添加指向对应pc版网址的链接**rel="canonical"标记**。代码如下：

```
<link rel="canonical" href="//moba.163.com" >
```





























