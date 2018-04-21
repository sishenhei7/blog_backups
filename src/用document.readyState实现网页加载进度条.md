# 用document.readyState实现网页加载进度条

## 概述

之前以为给网页设置加载进度条很麻烦，今天一学真是超级简单，记录下来供以后开发时参考，相信对其他人也有用。

### readyState

主要运用了document.readyState和nprogress插件。

document.readyState有三种状态：
1. loading：document仍在加载。
2. interactive：互动，文档已经完成加载，文档已被解析，但是诸如图像，样式表和框架之类的子资源仍在加载。
3. complete：完成。

### 代码

引入如下Nprogress文件：

```
<script src='nprogress.js'></script>
<link rel='stylesheet' href='nprogress.css'/>
```

然后放入如下代码即可：

```
<script>
    NProgress.start();
    console.log(NProgress);
    document.onreadystatechange = function () {
        if (document.readyState === "interactive") {
            NProgress.set(0.5);
        } else if(document.readyState === "complete") {
            NProgress.done();
        }
    };
</script>
```
