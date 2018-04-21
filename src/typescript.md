# webpack中使用typescript

## 概述

这是我学习webpack中使用typescript的记录，供以后开发时参考，相信对其他人也有用。

学习typescript建议直接看[中文文档](https://www.tslang.cn/docs/handbook/basic-types.html)或[英文文档](http://www.typescriptlang.org/docs/handbook/basic-types.html)，休闲之余可以看这篇[TypeScript 总结博客](https://www.cnblogs.com/tansm/p/TypeScript_Handbook.html)。

### 安装

在命令行下输入如下内容即可：

```
npm install -g typescript
```

### tsconfig

首先需要配置**tsconfig.json**文件，官方常用配置如下。

一般这个时候在命令行输入tsc，npm就会自动把src目录下的所有ts文件编译过来放在built文件夹里面，并且文件夹也会移过来，并且是在同一个命名空间下面(所以**不能声明重复的变量方法**等)。

```
{
    "compilerOptions": {
        //输出目录为build
        "outDir": "./built",
        //接受js作为输入
        "allowJs": true,
        //转换为es5
        "target": "es5"

        //下面为可选的

        //模块引用方式为commonjs
        "module": "commonjs",
        //用mode进行模块解析
        "moduleResolution": "node",
        //使用sourceMap
        "sourceMap": true,
        //启用实验性的metadata API
        "emitDecoratorMetadata": true,
        //启用实验性的装饰器
        "experimentalDecorators": true,
        //不删去注释
        "removeComments": false,
        //不启用严格检查
        "noImplicitAny": false
    },
    "include": [
        //读取src目录下的所有文件
        "./src/**/*"
    ]
}
```

### Webpack

在webpack中使用要先安装相关loader，官方建议安装awesome-typescript-loader(因为它比ts-loader更快)：

```
npm install awesome-typescript-loader source-map-loader --save-dev
```

webpack.config.js设置如下(要把awesome-typescript-loader放在ts的所有loader之前)。

```
module.exports = {
    entry: "./src/index.ts",
    output: {
        filename: "./dist/bundle.js",
    },

    // Enable sourcemaps for debugging webpack's output.
    devtool: "source-map",

    resolve: {
        // Add '.ts' and '.tsx' as resolvable extensions.
        extensions: ["", ".webpack.js", ".web.js", ".ts", ".tsx", ".js"]
    },

    module: {
        loaders: [
            // All files with a '.ts' or '.tsx' extension will be handled by 'awesome-typescript-loader'.
            { test: /\.tsx?$/, loader: "awesome-typescript-loader" }
        ],

        preLoaders: [
            // All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
            { test: /\.js$/, loader: "source-map-loader" }
        ]
    },

    // Other options...
};
```

### 导入导出

官方不建议用module导入导出模块，因为会出现找不到module的报错。官方建议用如下导出导出模块的方式。

```
//导入
import foo = require("foo");
foo.doStuff();

//导出
function foo() {
    // ...
}
export = foo;
```

### 其它

#### 连续添加参数

```
//官方不建议这么做
var options = {};
options.color = "red";
options.volume = 11;

//官方建议这么做
let options = {
    color: "red",
    volume: 11
};
```

如果非要按照第一种方法，就需要先添加一个接口(**任何这种没有定义的error都可以通过添加类型断言和接口来解决**)。

```
interface Options { color: string; volume: number }

let options = {} as Options;
options.color = "red";
options.volume = 11;
```

#### any，Object和{}

从适用范围上来讲，any > Object > {}。

所以一般使用any和{}。

#### 严格检查

严格检查通过如下方法设置：

```
"compilerOptions": {
    //启用严格检查
    "noImplicitAny": true,
    //启用严格的null和undefined检查
    "strictNullChecks": true
  }
```

!可以在严格的null和undefined检查中**忽略null和undefined检查**。

```
foo.length;  // 报错 - 'foo' is possibly 'null'

foo!.length; // 不报错 - 'foo!' just has type 'string[]'
```

如果函数中的this报错，就在函数参数中给this**加上类型断言**(一般来说只有在noImplicitThis为true时才会出现这种情况)。

```
Point.prototype.distanceFromOrigin = function(this: Point, point: Point) {
    return this.getDistance({ x: 0, y: 0});
}
```
