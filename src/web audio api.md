## 概述

研究**Web Audio Api**的主要原因是：工作中需要在ios中实现声音的淡出效果，主要是通过setInterval来改**audio标签的volume属性**实现的，但是ios上面volume属性是**只读**的，所以在ios上面改volume属性无效。

这个时候只能使用H5的Audio Api或者一些封装了Audio Api的库比如soundJs来解决。这篇博文记录了我学习**原生Audio Api**的心得，记录下来供以后开发时参考，相信对其他人也有用。

参考资料：
[努力翻译一篇中文最友好的，Web Audio API的使用相关的文章](https://segmentfault.com/a/1190000005715615)
[mdn Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)

### 简介

Web Audio API是对```<audio>标签```功能上的补充，它的强大之处在于：
1. 它可以一秒内同时发出**多个声音**，甚至1000多种声音也可以不经过压缩直接播放。
2. 它可以把音频流独立出来，进行**复杂的处理**。
3. 它能将音频流**输出到多个地方**，比如canvas，从而实现音频的可视化与立体化。

使用Web Audio API的**基本流程**如下：
1. 创建一个AudioContext对象。
2. 给AudioContext对象添加声音源，声音源可以是```<audio>```里面的，也可以是通过网址自行下载的，也可以是利用oscillator模拟的。
3. 创建需要使用的效果节点，比如reverb, biquad filter, panner, compressor, gainNode等。
4. 选择音频的最终输出节点。比如电脑的扬声器。
5. 把声音源和节点连接起来，并且把节点和最终输出节点连接起来。
6. 播放声音。

### 代码示例

下面是一个使用```<audio>源```播放的代码，主要实现声音的**淡出效果**：

```
//初始化音频api
window.AudioContext = window.AudioContext || window.webkitAudioContext;
var audioCtx = new AudioContext();
//设置音频的源，使用id为Jaudio2的<audio>标签
var myAudio = document.querySelector("#Jaudio2");
var source = audioCtx.createMediaElementSource(myAudio);
//初始化音量控制节点
var gainNode = audioCtx.createGain();
//初始化音量，为1
gainNode.gain.setValueAtTime(1, audioCtx.currentTime);
//把节点连接起来。audioCtx.destination就是最终输出节点。
source.connect(gainNode);
gainNode.connect(audioCtx.destination);
//播放
myAudio.play();
//设置淡出效果，在2秒内音量递减到0
gainNode.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 2);
//暂停声音
audioCtx.suspend();
```

### 问题

在具体的使用中，碰到一个问题，就是在移动端ios设备上利用上述代码**播放不了音频**，但是在PC端的chrome浏览器上可以正常播放并且实现淡出。

最后在看soundJs插件的时候，它的文档里面说，在ios上**需要用户操作来解锁Web Audio**！我们都知道在ios上面需要用户操作来解锁```<audio>```或者```<video>标签```，难道说Web Audio的初始化也需要用户操作来解锁吗？

等我找个时间试试看了。如果不行的话只能用封装了Web Audio的库比如soundJs了。



































