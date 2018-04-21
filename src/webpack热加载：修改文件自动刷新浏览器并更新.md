# webpack热加载：修改文件自动刷新浏览器并更新

## 概述

之前用react脚手架，觉得那种修改了能立即自动刷新浏览器并更新的功能实在非常人性化，所以想在开发其它项目的时候能用上。于是查了一些资料记录在此，供以后开发时参考，相信对其他人也有用。

其实代码也挺简单的，只不过我查的时候绕了许多弯路，有人说用--watch啊，有人说webpack-dev-server实时编译后的文件都保存到了内存中，然后blablabla的，最后我却在当初看的[入门Webpack，看这篇就够了](https://www.jianshu.com/p/42e11515c10f)和[webpack快速入门——实战技巧](http://www.cnblogs.com/hezihao/p/8072750.html)里面找到了。

### 配置

(1)首先安装依赖。

```
npm install --save-dev webpack
npm install --save-dev webpack-dev-server
//如果是webpack4的话可能还需要运行如下命令
npm install webpack-cli -D
```

(2)然后在package.config.js文件中加入如下代码：

```
//这里是表示打包时使用source-map，打包之后调试会直接跳到source-map中，再也不用看压缩代码。
  devtool: '#eval-source-map',

//配置服务器
  devServer: {
    port: 3030,//端口
    contentBase: "./public",//本地服务器所加载的页面所在的目录
    historyApiFallback: true,//不跳转
    inline: true//实时刷新
  },

//配置自带插件--watch的刷新频率
  watchOptions: {
    poll: 1000,//监测修改的时间(ms)
    aggregateTimeout: 500,//防止重复按键，500毫秒内算按一次
    ignored: /node_modules/,//不监测
  }
```

(3)修改package.json文件，在script里面加入如下内容即可。其中--open表示运行时自动打开浏览器。

```
"start": "webpack-dev-server --open"
```
