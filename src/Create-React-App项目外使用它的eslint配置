# Create-React-App项目外使用它的eslint配置

## 概述

使用Create-React-App脚手架感觉它的eslint配置有点好用，于是考虑不用Create-React-App脚手架该怎么使用这些配置。

我于是eject了Create-React-App脚手架，查看它的详细配置和官方文档，总结了使用它的eslint配置的方法，记录如下，供以后开发时参考，相信对其它人也有用。

### 配置

(1)首先安装依赖：

```
npm install eslint --save-dev
npm install babel-eslint --save-dev
npm install eslint-plugin-flowtype --save-dev
npm install eslint-plugin-jsx-a11y --save-dev
```

(2)然后配置```package.json```文件。(*不需要配置.eslintrc.js文件*，详见[Eslint Configuring文档](http://eslint.cn/docs/user-guide/configuring))

```
"eslintConfig": {
  "parser": "babel-eslint",
  "extends": [
    "plugin:flowtype/recommended",
    "plugin:jsx-a11y/recommended"
  ],
  "plugins": [
    "flowtype",
    "jsx-a11y"
  ]
}
```

(3)在主目录下面输入eslint + 文件名即可。比如```eslint test.js```。

### 测试是否生效

测试内容如下，如果有5个报错，那么证明是生效的。

```
type X = bool
// Message: Use "boolean", not "bool"

// Options: ["boolean"]
type X = bool
// Message: Use "boolean", not "bool"

// Options: ["bool"]
type X = boolean
// Message: Use "bool", not "boolean"
```

### 感想

以前用eslint的时候感觉每次要配置```.eslintrc.js```文件超级麻烦，现在才发现可以直接在```package.json```配置，真的很方便。

