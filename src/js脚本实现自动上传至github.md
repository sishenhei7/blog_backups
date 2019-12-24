# js脚本实现自动上传至github

## 概述

如果要进行多次上传，使用git gui也会不方便，所以我总结了一下用npm的simple-git实现自动上传至github的方法。供以后开发时参考，相信对其他人也有用。

### 前提条件

1. 需要安装node, npm, git。
2. 把git的安装目录C:\Program Files (x86)\Git\cmd添加到环境变量中。(确保能在命令行中运行git)

### 代码

(1)在上传目录下用npm安装simple-git.

```
npm install simple-git
```

(2)新建一个git-commit.js文件，加入如下代码：

```
const git = require('simple-git')
const path = '上传的路径'
const commitMessage = '提交时的说明'
const repo = 'https://github.com/[github名字]/[github目录].git'

git(path)
  .init()
  .add('./*')
  .commit(commitMessage)
  .addRemote('origin', repo)
  .push(['-f', 'origin', 'master'], () => {
    console.log("Push to master success");
  })
```

注意：这是强制提交，如果非强制提交则把'-f'去掉。

(3)在上传目录下用node运行git-commit.js文件：

```
node git-commit.js
```
