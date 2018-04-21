# 原生js加载script标签

## 概述

我在查看一些别人的网站的时候，常常因为这个网站没有引入jquery等库而不方便操作而苦恼。所以这里主要是记录一段代码，在查看别人网站的时候，直接在控制台输入这段代码即可引入想**引入的库**。供以后开发时用，相信对其他人也有用。

### 代码

```
var myScript = document.createElement('script');
myScript.src = 'https://cdn.bootcss.com/jquery/3.3.1/jquery.js';
document.getElementsByTagName('head')[0].appendChild(myScript);
```

原理主要是利用jsonp跨域来加载库，上面是加载jquery的3.3.1版本的库，如果想加载其它库需要去网上搜这个库的**cdn地址**，然后把上面的地址替换为搜到的地址即可。




