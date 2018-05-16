## 概述

**前端路由与后端路由**的结合一直是一个难题。koa-static这个中间件能够把静态资源“搬到”后端路由上面去，react-create-app在不解构的情况下只会把资源打包到build文件夹，怎么协调koa-static，react-create-app和react-router-dom？我摸索出了一个解决方案，记录下来，供以后开发时参考，相信对其他人也有用。

**源码**可以参考[我的github](https://github.com/sishenhei7/blog_server/tree/master/demo/koa-router%20and%20react-dom-router)。

### 文件结构

我们的koa服务器的地址为：```http://localhost:3000/```，文件结构如下：

```
├── controllers                  #后端 
├── models                       #后端 
├── routes                       #后端路由
├── node_modules
├── static                       #前端react文件夹
│   ├── build                    #react-create-app打包默认地址
│   ├── node_modules
│   ├── public
│   ├── src
│   └── package.json等配置
│
├──app.js                        #后端启动文件
└──package.json等配置
```

### 前端路由

前端路由是由react-router-dom控制的，我们需要创造一个**中间路径**，我命名为react-blog，代码如下：

```
<Router>
  <Switch>
    <Route path='/react-blog' component={Layout} />
    <Redirect from='/' to='/react-blog'/>
  </Switch>
</Router>
```

我们的目的是要把'/'路径映射到'/react-blog'路径，这样react的静态资源路由就**不会影响到全局路由**。

### koa-static

koa-static这个中间件起一个**映射的作用**，它把被映射的文件夹全部映射到```http://localhost:3000/```下面，我们关于koa-static这个中间件的代码如下：

```
//app.js
//koaStatic
app.use(koaStatic(
    path.join(__dirname, './static')
));
```

这样就可以把static文件夹下的资源全部映射到```http://localhost:3000/```下面，我们在浏览器中输入如下地址则可访问相应的文件。

```
//访问前端react文件夹下的package.json文件
http://localhost:3000/package.json

//访问前端react打包后的index文件
http://localhost:3000/build/index.html
```

这个时候我们能通过```http://localhost:3000/build/index.html```访问我们的react静态资源，访问的时候路由会**自动交给前端路由**，并且跳转到```http://localhost:3000/react-blog```这个地址。

但是当我们在另一个浏览器标签里面输入```http://localhost:3000/react-blog```这个地址的时候，会返回404，原因是我们的**后端路由并没有react-blog这个选项**，所以我们需要继续配置后端路由。

### koa-router

我们用koa-router另外开一个后端路由，来处理```http://localhost:3000/react-blog```这个路径，代码如下：

```
//blog.js
const Router = require('koa-router');
let routerBlog = new Router();
routerBlog.get('*', async (ctx) => {
    ctx.redirect('/build/index.html');
})
module.exports = routerBlog;

//router.js
const routerBlog = require('./blog');
let router = new Router();
router.use('/react-blog', routerBlog.routes(), routerBlog.allowedMethods());
module.exports = router;
```

上面的代码把路径```http://localhost:3000/react-blog```下的所有路径全部导向```http://localhost:3000/build/index.html```，这样它会**自动跳转到react静态资源**，并且把路由交给前端路由。

### 其它

上面就基本实现了前端路由和后端路由相结合。

但是还美中不足的是，所有指向前端静态资源的url都会跳转到前端静态资源的主页。这就造成了一个bug，就是我收藏了前端静态资源其中的一个页面，当我下次想打开这个页面的时候**只会跳到主页**，并不会跳到这个页面。

这个bug留待后续优化了，思路是**用正则截取后面的路径，然后通过传参传给前端路由，再由前端路由处理。**



