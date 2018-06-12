## 概述

以前听别人说抓包抓包的，听起来**很神秘高大上**的样子，想入门又不知道从何学起。今天偶然在工作中遇到了以下2个需求：
1. 改线上的代码，特别是PC端**js代码**。
2. 写了一个移动端页面，由于跨域，改了host地址，**把ip地址映射为另一个地址**，导致不能在手机端直接用ip地址+端口访问。

借着实现这2个需求的机会，我学习了一下fiddler4抓包，并且写下心得，供以后开发时参考，相信对其他人也有用。

### fiddler4介绍

之前用charles抓包，总是有一些不能解决的问题，现在用fiddler4抓包，完美满足需求。

fiddler4是**免费的**，下载地址：[fiddler4官网](https://www.telerik.com/fiddler)

注意：安装之后一定要**用管理员身份打开fiddler4**，否则可能抓不到包。

### 改线上的代码

参考这篇博文：[fiddler修改线上的内容](https://www.cnblogs.com/xianyulaodi/p/5785868.html)

首先打开fiddler右侧栏，选择 AutoResponder按钮，然后把下面的三个选项勾选上。
- Enable rules
- Unmatched requests passthrough
- Enable latency

然后点击Add Rule，在下面输入你想“屏蔽”的url，并且点击下拉框，替换为本地文件，再点击save即可。

最后刷新网页，fiddler就会拦截这个url，并且把它替换为你输入的本地文件。

一般来说，如果想改动线上的html和css，那么可以直接在开发者工具里面调试。但是如果要**改js**，或者**大范围的改动html和css**，就可以用上面的方法，拦截要改动文件的url，替换为你本地的文件，很方便。

### 手机抓包

为了实现第二个需求，需要进行**手机抓包**，具体流程可以参考：[Fiddler4入门——手机抓包](https://blog.csdn.net/shimengran107/article/details/78644862)

首先启动Fiddler，打开菜单栏中的 Tools > Fiddler Options，打开“Fiddler Options”对话框。

然后在Fiddler Options”对话框切换到“Connections”选项卡，勾选“Allow romote computers to connect”后面的复选框，点击“OK”按钮。

然后在手机端的wifi里面设置手动代理，主机名为你的ip地址，端口为8888。

然后打开手机浏览器，输入ip:端口号=172.18.53.93:8888会打开一个下载证书的页面，点击最下方的“FiddlerRoot certificate”按钮，安装证书。（**非常重要，一定要下载证书**）

**注意**：有时在这一步会卡住，打不开这个下载证书的页面，这个时候可通过**重启fiddler**解决。

最后用手机上网，就可以在fiddler里面抓到包了。

**注意**：这里不仅可以抓包，还能实现下面2个需求：

1. **访问**电脑上修改了host的**映射地址**。（因为实际上fiddler在本地电脑**实现了一个代理**，所以可以通过手机直接访问电脑上的ip端口。）
2. **修改手机端收到的包**，原理和步骤同上面的“改线上的代码”。



