# git分支管理

## 概述

*git bash*配合*git gui*进行分支管理。

供以后开发时参考，相信对其他人也有用。

### 主要内容

利用git bash拉取分支，创建分支，切换分支，删除分支。

利用git gui对特定分支进行提交等操作。

### git bash 操作

(1)分支是和git的default分支绑定在一起的，在创建分支的那一刻，创建的分支下**并不是没有内容**，而是自动**有此刻default分支的所有内容**。

(2)如果直接用```git clone 仓库地址```则会拉取**default分支**，**不会拉取其它分支**。

```
//拉取分支
git clone -b 分支名 仓库地址

//查看分支
git branch

//创建分支(新建本地分支+上传本地分支)
git branch 分支名
git push origin 分支名

//切换分支
git checkout 分支名

//删除分支
git push origin --delete 分支名

```

### git gui 操作

(1)用上面的查看分支命令查看分支个数。

(2)如果只有一个分支，则git gui的操作**就是**在这个分支上进行的；

(3)如果有多个分支，先用git bash切换分支，然后git gui的操作就**自动**在你切换的分支上进行的。




