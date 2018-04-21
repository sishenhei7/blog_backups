# create-react-app脚手架中配置webpack的方法

## 概述

create-react-app脚手架中的react-scripts能够(1)帮我们自动下载需要的webpack依赖；(2)自己写了一个nodejs服务端脚本代码；(3)使用express的Http服务器；(4)并帮我们*隐藏了配置文件*。

那么假如我们需要额外配置webpack该怎么办呢？比如添加md的loader。

我总结了2种方法，供以后开发时参考，相信对其他人也有用。

### 方法一

运行如下命令即可把配置文件显示出来：

```
npm run eject
//然后输入Y
```

输入后项目目录会多出一个congfig文件夹，里面就有webpack的配置文件。

但是此过程不可逆，所以显现回来后就不能再隐藏回去了。

### 方法二

(**以修改babel插件实现按需加载antd为例，修改其它配置可以仿照这个方法来做。**)

(1)使用 [react-app-rewired](https://github.com/timarney/react-app-rewired) （一个对 create-react-app 进行自定义配置的社区解决方案）和[babel-plugin-import](https://github.com/ant-design/babel-plugin-import)（一个babel的管理加载的插件）。

```
$ yarn add react-app-rewired --dev
$ yarn add babel-plugin-import --dev

//或者使用npm
npm install react-app-rewired --dev
npm install babel-plugin-import --dev
```

(2)修改package.json 里的启动配置。

```
/* package.json */
"scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test --env=jsdom",
}
```

(3)在项目根目录创建一个 config-overrides.js 用于修改默认配置。

```
/* config-overrides.js */
const { injectBabelPlugin } = require('react-app-rewired');

module.exports = function override(config, env) {
    config = injectBabelPlugin(['import', { libraryName: 'antd', libraryDirectory: 'es', style: 'css' }], config);
    return config;
  };
```


