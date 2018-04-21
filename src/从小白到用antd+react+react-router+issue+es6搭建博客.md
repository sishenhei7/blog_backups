# 从小白到用antd+react+react-router+issue+es6搭建博客

## 概述

本身是前端小白，学过html,css,js的各种书，各种视屏，就是没有接触**web开发**的内容。偶然看见一个朋友用react搭建了一个博客，于是本着程序员无所不能的精神，也尝试着用react搭建博客。

下面记录我从小白到搭建博客的过程，没有写方法，因为网上已经很多方法了。

这是我搭建的博客地址：[馒头加梨子](https://candybullet.github.io)

### 结论

先说结论，我**学到了什么**：

1. 单页面web开发的流程。把要做的分为一个个模块，逐个实现，然后用webpack设置，开发并打包上线。

2. antd库的使用和一些组件的配置参数。

3. 相关知识：SPA, react, react-router4, antd, fetch, promise, es6等。

4. 怎么搜索？在github和相关社区搜索，通常能找到意想不到的惊喜。

5. 程序员怎么学习？一定要手打教程，并思考为什么要这么做。

我碰到了**哪些难点**：

- webpack配置。一开始我没有使用脚手架，以前只打过webpack的demo但是忘了，看了很多资料才学会webpck的实际使用。
- antd库组件配置。以前没用过这种库，完全不知道怎么下手，后来在官网看见每个例子都有代码demo，才慢慢熟悉的。
- router4中的路由赋值。为了把博客打造成SPA的形式，我思考了很多。
- fetch内容报错。我看了很多遍关于promise和生命周期的内容，最后通过添加loading解决。
- 添加目录。给文章加上锚点和通过锚点跳转，为了更优雅的编程，我遇到了很多坑。

### 搜索参考博客

既然自己是小白，那么当然要去**参考其他人的博客**，寻找他们的优点并且学下来呀。

那怎么搜呢？我主要通过这些渠道搜索：

- [百度](https://www.baidu.com/)
- [bing](https://cn.bing.com/)
- [react中文社区](http://react-china.org/)
- [github](https://github.com/)
- [segmentfault](https://segmentfault.com)
- [阮一峰的日志](http://www.ruanyifeng.com/blog/)

经过一番搜索，我最后定下来参考这几个大神的博客，他们都是用react搭建的，并且都能在github上找到源码。

- [reminia](https://reminia.github.io/)
- [cobish](https://cobish.github.io/#/all)
- [water](http://119.29.151.195/app/index)

然后样式参考这几个大神的博客，他们不是用react搭建的，但是样式很好看。

- [我自己的博客](http://www.cnblogs.com/yangzhou33/)
- [Brook's Blog](https://uedsky.com/)
- [早起搬砖](http://morning.work/)

### 学习react

作为一个小白，肯定要先**学习react**，那么去[react中文官网](https://doc.react-china.org/)把文档看一遍，并且把教程手打一遍啦。

### 思考博客架构

我要一个**什么样的博客**？

我的博客要有以下特点：

1. 一个好看时髦的导航栏。
2. 一个自动生成的目录(文章界面)。
3. 一个回到顶端的功能。
4. 要有代码高亮。
5. 一个分类的功能。(没做)
6. 一个加载的时候的进度条。(没做)
7. 要简洁，扁平化。
8. 要响应式。
9. 要速度快。

我还进行了如下思考：

1. 我不要首页和侧栏。因为显得太复杂了。

2. 我不要翻页。因为我有回到顶端功能，而且我现在写的文章还少，不需要翻页。而且阮一峰的日志也没有做翻页功能。

### 单页面软件SPA

我之前没有接触SPA，但是在搜索的过程中偶然碰到了，觉得很有必要学习一下，因为这是**当代web开发的分水岭**。于是去看了一本书[《单页Web应用:JavaScript从前端到后端》](https://book.douban.com/subject/25986284/)。

这是我学完SPA之后写的一篇感想博客：[当我们说前端，我们在说什么](http://www.cnblogs.com/yangzhou33/p/8470975.html)。

SPA让我了解到了模块化开发的思想，也解决了我的一个需求：速度快。

### 路由

路由是SPA，也是react中很重要的一个功能。

于是我去学习了**react-router4**，并且把[react-router-tutorial](https://github.com/reactjs/react-router-tutorial)自己手打了一遍。并且查资料补充了redirect等内容。

### antd

在学习[water](http://119.29.151.195/app/index)博客的时候接触了一个很有意思的ui库：[antd](https://ant.design/docs/react/introduce-cn)。我以前也没有用过这种类型的库，于是本着好奇的精神，也打算用这个库。

我学习了这个库里面的这些组件：Button, Icon, Layout, Affix, Dropdown, Menu, Card, Collapse, List, Tag, BackTop。在学习的时候踩了很多坑，也懂了这些组件的一些**配置参数**。

[antd](https://ant.design/docs/react/introduce-cn)也解决了我的一个需求：响应式。

### es6

虽然我在[react中文官网](https://doc.react-china.org/)的教程里面学习了部分es6语法，但是在学习别人博客的时候碰到了很多**es6语法**，我自己也有强迫症，能用es6语法的地方尽量用es6语法。比如我就很不喜欢用if-else，能用map+箭头函数的地方我就用map+箭头函数。

于是我学习并实践了如下es6的知识：模板对象，箭头函数，解构赋值，类，promise，let和const。

### 路由赋值

路由路由，还是路由。

学过SPA之后，我觉得有必要把博客打造成SPA的形式。于是各种思考和查资料。最后打造成了目前这种形式：**只在打开博客的时候发送http请求，其余操作不发送http请求，直接由浏览器完成**。

其实还有另一种实现方法，就是利用redux，真的是与我的想法不谋而合，由于我还没有学，而且redux文档不建议小型网站使用redux，所以我没有用这种方法。

这个时候我总结了一篇博文：[react在router中传递数据的2种方法](http://www.cnblogs.com/yangzhou33/p/8495459.html)。

### fetch

怎么**获取博客内容**呢？

我一开始打算用老办法：写完markdown文件就上传至github，然后一个个解析md文件。但是这个方案有个缺点，就是每次写完都要build上传非常麻烦。强迫症迫使我寻找更好的方法。偶然我发现用issue写博客是真的好，于是最后改用从issue获取博客内容。

那么怎么获取呢？在别人博客中找了三种方案：

- 用es5原生的fetch方法。
- 用isomorphic-fetch库的fetch方法。
- 用axios库的相关方法。

最后我决定用用isomorphic-fetch库的fetch。

### 代码高亮

一开始我还不知到什么是**代码高亮**，只是看资料各种说代码高亮。代码写着写着才发现，文章页面的代码区很单调啊，所以这才醒悟原来是代码区的代码高亮。

我最后用的别人现成的方案：marked结合highlight.js设置代码高亮。

### fetch报错

由于fetch方法返回的是promise对象，有一定的延迟，所以模块的render函数开始渲染的时候并不能取到数据，然后marked库各种报错。

于是我去看了又看**promise**的文档和**组件生命周期**的文档。

最后通过在模块的state属性里面添加一个loading属性成功解决。

### 目录和锚点

由于antd没有在文章界面自动生成目录的组件，于是我自己动手写了自动生成目录的组件。

目录要不要**跳转功能**？我的目录必须要与众不同啊，强迫症需要我添加这个功能。

在用js写跳转功能的时候，我才发现react的锚是个巨坑，因为react的路由，es5的#锚点不能正确被解析，于是我又去查资料，最后用scrollIntoView解决了。本来以为解决方法超麻烦的，最后一看真的超简单。

### 路由污染

对，路由路由，还是路由。

搭建快完成了，搬上github可以看了，但是我发现，**我github上的其它githubPage都变成了我的博客了**。

为了解决这个问题，我只好把博客放在我的github的一个分目录下面。又要改路由。

改好路由之后我又发现，刷新键不能用了。网上查资料，最后看大牛router4的原理解析里面说，需要在服务端解决。但是我是github博客，没有服务端。也不能不支持刷新键啊，而且不解决的话，收藏文章也不支持了，只能收藏首页。

由于强迫症，我只好又把博客放在github小号上面，来让别人体验完整的功能。

### 收尾阶段

这个时候有2个问题。

一个是**导航栏太单调**了，需要加入一些其它的模块，经过考虑，我加入了作品和关于模块。这个没有什么难度。

另一个是**css样式**，我参考[我自己在博客园的博客](http://www.cnblogs.com/yangzhou33/)和其他人的博客，一下就设置好了，没什么难度。


