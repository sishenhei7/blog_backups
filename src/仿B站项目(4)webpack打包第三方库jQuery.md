## 概述

在项目中不可避免的会用到jquery等第三方库，来看看有什么问题，怎么解决。

### 遇到的问题

一般情况下，直接require第三方库，比如jquery，然后webpack会自动把第三方库打包进bundle.js里面去，这样就造成了三个问题：
1. bundle.js的**文件会非常大**。
2. 每次更新bundle.js的时候根本没有更新第三方库，但是用户仍然要下载包含**所有**第三方库的bundle.js，不利于**缓存**。
3. **更新第三方库**的时候很麻烦，又要重新下bundle.js。(虽然这种情况很少)

### 理想的解决方案

我理想的解决方案是：
1. 打包前把所有第三方库都放在一个**静态文件夹**里面。
2. 打包的时候这个静态文件夹里面的内容不打包进bundle.js。
3. 打包的时候把这个静态文件夹里面的内容全部扔在打包文件夹里面的一个static文件夹里面。
4. 打包后在html文件的script标签里面引入这些第三方库。

### 现实很骨感

我现在也没有时间按照我的方案自己学习并且写一个打包插件，所以只能用现有的解决方案。**网上**主要的解决方案包括以下几种，具体可自行上网搜索。
1. 用resolve的**alias属性**给第三方库起别名来引用。(只解决了全局引用问题，没有解决上面的任何一个问题)
2. 用**ProvidePlugin插件**。(可能存在全局引用问题，并且eslint会不生效，并且没有解决上面的任何一个问题)
3. 使用**expose-loader**。(和方案1一样，只解决了全局引用问题，没有解决上面的任何一个问题)
4. 使用**CommonsChunkPlugin插件**，把引用次数多的第三方库打包进vendor.js里面去(解决了问题1，没有解决问题12)
5. 用**externals属性**，设置哪些库不打包，还是可以require，只是不打包而已。(可行)

### 实际用到的方案

最后我只能选择用方案5：利用externals属性。虽然和我的理想解决方案有点出入，但是问题不大。下面来看看具体怎么操作。

(1)修改webpack.config.js，加入**externals属性**。意思是jquery不打包，并且给它设置一个全局变量，叫做jQuery，在任何地方都可以用jQuery访问jquery库。

```
//简写
module.exports = {
  externals: {
      jquery: 'jQuery'
    }
}
```

(2)虽然我们把jQuery设置为全局变量，但是为了保险，我们在引用jquery库的时候，还是**用require**。把下面语句加入到使用jquery的js文件里面去。

```
var $ = require('jquery');
```

(3)由于不打包jquery，所以需要在html的head标签里面加入引入jquery的**script标签**。建议用网上免费的开源cdn。(搜jquery cdn即可)

```
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
```
