## 概述

使用koa免不了要操纵数据库，现阶段流行的数据库是mongoDB，所以我研究了一下koa里面mongoDB数据库的增删改查，记录下来，供以后开发时参考，相信对其他人也有用。

源代码请看：[我的github](https://github.com/sishenhei7/blog_server/tree/master/demo/koa2%20and%20mongoose)

可以参考我的这2篇博文：
[mongoose入门](http://www.cnblogs.com/yangzhou33/p/8996316.html)
[koa中返回404并且刷新后才正常的解决方案](http://www.cnblogs.com/yangzhou33/p/9038695.html)

### 说明

值得一提的是，这只是一个demo而已，有很多判断并没有写，代码也有优化的空间，但是能够实现mongoDB数据库的增删改查操作。我个人不准备继续优化了，因为我接下来要写一个后台0.0

另外，需要电脑上事先安装好mongodb数据库，并且开启mongodb服务！

代码我就不贴出来了，自己在[我的github](https://github.com/sishenhei7/blog_server/tree/master/demo/koa2%20and%20mongoose)上面看吧0.0

我也不打算讲解代码了，看[mongoose入门](http://www.cnblogs.com/yangzhou33/p/8996316.html)应该就能懂了，因为说到底koa操纵mongoDB数据库其实和**nodejs操纵mongoDB数据库**并没有太多的不同。最大的不同我写在这篇博文里面了：[koa中返回404并且刷新后才正常的解决方案](http://www.cnblogs.com/yangzhou33/p/9038695.html)。

















