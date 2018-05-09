## 概述

最近学习利用**koa搭建API接口**，小有所得，现在记录下来，供以后开发时参考，相信对其他人也有用。

就目前我所知道的而言，API有2种，一种是**jsonp**这种API，前端通过ajax来进行跨域请求获得数据；另一种是**restful API**，前端通过fetch或者axios进行cors请求来获得数据。

本篇博文记录我用koa打造的**jsonp API**。

参考资料：[《Koa2进阶学习笔记》](https://chenshenhai.github.io/koa2-note/)，[KOA docs](https://koa.bootcss.com/)

### 建一个服务器

首先用koa来建一个服务器，在这个过程中，我们用到了**koa-logger中间件**，它会在服务端显示各种请求操作记录，便于我们开发和调试。

```
//app.js
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');

const app = new Koa();

app.use(logger());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

*注意：如果不能运行的话，就换一个端口！*

### babel

根据官网文档，如果node版本**大于7.6**，就可以**无痛使用async方法**，否则要加入babel-register库和transform-async-to-generator库，并且在app.js里面加入如下代码：

```
require('babel-register');
```

而且要在```.babel```文件中，至少有如下代码：

```
{
  "plugins": ["transform-async-to-generator"]
}
```

### 路由

我们需要给jsonp的API**新建一个路由**，这样就不影响其它路由了。（我们可能会在其它路由搭建restful api，也可能在其它路由搭建静态页面等等。）

```
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');
const Router = require('koa-router');

const app = new Koa();

app.use(logger());

// 子路由1：主页
let routerHome = new Router();
routerHome.get('/', async (ctx) => {
    ctx.body = '欢迎欢迎！';
})

// 子路由2：jsonp api
let routerJsonp = new Router();
routerJsonp.get('/data1', async (ctx) => {
    ctx.body = '数据';
})

// 装载所有路由
let router = new Router();
router.use('/', routerHome.routes(), routerHome.allowedMethods());
router.use('/jsonp', routerJsonp.routes(), routerJsonp.allowedMethods());
app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

现在，先用node运行app.js文件，然后用浏览器访问```http://localhost:3000/```会看到欢迎欢迎四个字，访问```http://localhost:3000/jsonp/data1```会看到数据两个字。

### jsonp

jsonp的机制是，我们传给服务器一个callback参数，值是我们要调用的函数名字，然后服务器返回一个字符串，这个字符串不仅仅是需要返回的数据，而且这个数据要用这个函数名字包裹。

所以我们需要做如下事情：
1. 解析请求所带的参数，并且读取callback参数的值。解决方法是，我们用ctx.request.query获得请求所带的所有参数，然后读取出callback参数：ctx.request.query.callback。
2. 把数据转化为字符串，并用这个函数名包裹。这个很简单，字符串连接即可。

代码如下：

```
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');
const Router = require('koa-router');

const app = new Koa();

app.use(logger());

// 子路由1：主页
let routerHome = new Router();
routerHome.get('/', async (ctx) => {
    ctx.body = '欢迎欢迎！';
})

// 子路由2：jsonp api
let routerJsonp = new Router();
routerJsonp.get('/data1', async (ctx) => {
    let cb = ctx.request.query.callback;
    ctx.type = 'text';
    ctx.body = cb + '(' + '"数据"' + ')';
})

// 装载所有路由
let router = new Router();
router.use('/', routerHome.routes(), routerHome.allowedMethods());
router.use('/jsonp', routerJsonp.routes(), routerJsonp.allowedMethods());
app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

最后用node运行app.js文件就**大功告成**了！！！我们首先在控制台引入jquery库，然后调用jquery的ajax方法，进行jsonp请求，就能获得“数据”了。

首先我们在浏览器的控制台引入jquery库：

```
var myScript = document.createElement('script');
myScript.src = 'https://cdn.bootcss.com/jquery/3.3.1/jquery.js';
document.getElementsByTagName('head')[0].appendChild(myScript);
```

然后用ajax方法进行jsonp跨域请求数据，就可以在控制台看到“数据”二个字了。

```
$.ajax({
    url : 'http://localhost:3000/jsonp/data1',
    dataType : 'jsonp',
    type : 'get',
    success : function(res){
        console.log(res);
    },
    error: function() {
        alert("网络出现错误，请刷新！");
    }
});
```

注意：由于原页面是http页面，所以**不能在https页面进行jsonp跨域请求**。找一个http页面进行jsonp跨域请求测试吧。
