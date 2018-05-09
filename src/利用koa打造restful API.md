## 概述

最近学习利用**koa搭建API接口**，小有所得，现在记录下来，供以后开发时参考，相信对其他人也有用。

就目前我所知道的而言，API有2种，一种是**jsonp**这种API，前端通过ajax来进行跨域请求获得数据；另一种是**restful API**，前端通过fetch或者axios进行cors请求来获得数据。

本篇博文记录我用koa打造的**jsonp API**。

可以先查看我的上一篇文章：[利用koa打造jsonp API](http://www.cnblogs.com/yangzhou33/p/9011794.html)。

参考资料：[《Koa2进阶学习笔记》](https://chenshenhai.github.io/koa2-note/)，[KOA docs](https://koa.bootcss.com/)

### restful API

其实搭建restful API很简单，引入**cors中间件**即可，不需要设置请求头为**Access-Control-Allow-Origin**，这个中间件会自动帮我们设置。我们先引入中间件，然后重开一个路由存放restful API即可，代码如下：

```
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');
const Router = require('koa-router');
const cors = require('@koa/cors');

const app = new Koa();

app.use(logger());

app.use(cors());

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

// 子路由3：restful api
let routerRest = new Router();
routerRest.get('/data1', async (ctx) => {
    ctx.body = 'rest数据';
})

// 装载所有路由
let router = new Router();
router.use('/', routerHome.routes(), routerHome.allowedMethods());
router.use('/jsonp', routerJsonp.routes(), routerJsonp.allowedMethods());
router.use('/restful', routerRest.routes(), routerRest.allowedMethods());
app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

然后利用下面的请求代码，就会在控制台输出**“rest数据”**。

```
$.ajax({
    url : 'http://localhost:3000/restful/data1',
    type : 'get',
    success : function(res){
        console.log(res);
    },
    error: function() {
        alert("网络出现错误，请刷新！");
    }
});
```

### 改进

不得不说，我们的api是非常简陋的，我们考虑对它做如下改进：
1. **支持post请求**。这个很好办，在路由那里添加post方法即可。
2. **支持yaml数据导入**，然后通过api，把数据作为resonse发送出去。这个需要2步操作，第一步用node导入并解析yaml数据为对象，第二步把对象转化为字符串传递出去。第一步需要用到fs库和yamljs库，第二步需要用到JSON.stringify方法。具体代码如下：

首先支持post请求：

```
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');
const Router = require('koa-router');
const cors = require('@koa/cors');

const app = new Koa();

app.use(logger());

app.use(cors());

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
}).post('/data1', async (ctx) => {
    let cb = ctx.request.query.callback;
    ctx.type = 'text';
    ctx.body = cb + '(' + '"数据"' + ')';
})

// 子路由3：restful api
let routerRest = new Router();
routerRest.get('/data1', async (ctx) => {
    ctx.body = 'rest数据';
}).post('/data1', async (ctx) => {
    ctx.body = 'rest数据';
})

// 装载所有路由
let router = new Router();
router.use('/', routerHome.routes(), routerHome.allowedMethods());
router.use('/jsonp', routerJsonp.routes(), routerJsonp.allowedMethods());
router.use('/restful', routerRest.routes(), routerRest.allowedMethods());
app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

然后我们新建jsonp.data1.yaml文件作为jsonp API的**原始数据**，新建restful.data1.yaml作为restful API的原始数据。

```
//jsonp.data1.yaml
api: "jsonp"
info:
  version: "0.0.1"
  title: test for jsonp

//restful.data1.yaml
api: "restful"
info:
  version: "0.0.1"
  title: test for restful
```

然后我们添加**fs库(node自带，不需要install)和yamljs库**进行导入和解析yaml文件，并且用**JSON.stringify方法**把json对象转化为字符串：

```
'use strict'
const Koa = require('koa');
const logger = require('koa-logger');
const Router = require('koa-router');
const cors = require('@koa/cors');
const fs = require('fs');
const YAML = require('yamljs');

const app = new Koa();

app.use(logger());

app.use(cors());

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
    ctx.body = cb + '(' + JSON.stringify(YAML.parse(fs.readFileSync('./jsonp.data1.yaml').toString())) + ')';
}).post('/data1', async (ctx) => {
    let cb = ctx.request.query.callback;
    ctx.type = 'text';
    ctx.body = cb + '(' + JSON.stringify(YAML.parse(fs.readFileSync('./jsonp.data1.yaml').toString())) + ')';
})

// 子路由3：restful api
let routerRest = new Router();
routerRest.get('/data1', async (ctx) => {
    ctx.body = YAML.parse(fs.readFileSync('./restful.data1.yaml').toString());
}).post('/data1', async (ctx) => {
    ctx.body = YAML.parse(fs.readFileSync('./restful.data1.yaml').toString());
})

// 装载所有路由
let router = new Router();
router.use('/', routerHome.routes(), routerHome.allowedMethods());
router.use('/jsonp', routerJsonp.routes(), routerJsonp.allowedMethods());
router.use('/restful', routerRest.routes(), routerRest.allowedMethods());
app.use(router.routes()).use(router.allowedMethods());

app.listen(3000, () => {
    console.log('koa starts at port 3000!');
})
```

用前面类似的方法进行请求，可以看到返回了如下数据，并且支持了post请求。

```
//jsonp接口
Object {api: "jsonp", info: Object}

//restful接口
Object {api: "restful", info: Object}
```

### 其它

到这里就全部完成了，我尽量一点一点地浅显的写出来。实际上还有更多可以优化的地方：
1. 支持匹配各种模糊的**url路径**。
2. 支持对**传入参数**进行处理。
3. 支持**https**。

而且我们再一次看到，学习koa其实就是各种**中间件和api**的学习罢了。

最后写一下需要install的库：(虽然可以通过require推测出来)

```
"@koa/cors": "^2.2.1",
"koa": "^2.5.1",
"koa-bodyparser": "^4.2.0",
"koa-logger": "^3.2.0",
"koa-router": "^7.4.0",
"yamljs": "^0.3.0"
```

本文代码存放在[我的github的blog_server仓库](https://github.com/sishenhei7/blog_server)的demo文件夹里面。

