# antd在webpack里面的配置

## 概述

antd是蚂蚁金服打造的一个react组件，真的非常棒，我看了下官方文档，感觉比bootstrap要好。唯一的缺点可能就是打包的时候要打包它的一些样式表，所以资源体积会很大，并且css可能会和自己的相冲突。

我在webpack中配置antd配置了很久，现在把它记录下来，供以后开发时参考，相信对其他人也有用。

### 配置

配置代码如下：

```
    module: {
        loaders: [
        {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            query: {
                presets:['react', 'es2015'],
                plugins:[
                    ['import', {libraryName: 'antd', style: 'css'}] //按需加载
                ]
            },
        },
        {
            test: /\.css$/,
            loader: 'style-loader!css-loader'
        },
        ]
    }
```

### 我从中学到了什么

由于自己还是比较小白的，所以在写配置文件的时候踩了许多坑，记录如下：

(1)引号后面的大括号，中括号，小括号的左括号{、{、(不能另起一行。(本来为了好看，我就把他们另起一行写，但是这样是不行的，原因是编译的时候会自动在引号后面加分号，造成错误)

(2)变量名一定要注意，有的变量名要用单引号括起来，有的不需要。(比如上面的libraryName和style就不能用单引号括起来)



