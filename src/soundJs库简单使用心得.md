## 概述

由于工作需要，学习了一下[soundJs库](http://www.createjs.cc/soundjs/)，把心得记录下来，供以后开发时参考，相信对其他人也有用。

**soundJs**是createJs的一部分，它提供了强大的API来处理音频，是**音频类H5**的一个比较好的解决方案。

### 使用方法

我们主要想利用**soundJs**来实现一个音频的**淡出效果**。

对于soundJs，有两个非常重量级而且常用的类：
1. **Sound类**：通过createjs.Sound直接使用；用来创建声音。
2. **AbstractSoundInstance类**：它是通过play方法或者createInstance方法返回的；用来控制声音。

首先我们注册声音源。"../../media/bgm3.mp3"表示**声音源**；"myID"是**声音id**；3是**声音频道**，主要用于多种声音混合播放，我们只播放一种声音，所以这里随便用一个频道3。

```
createjs.Sound.registerSound("../../media/bgm3.mp3", "myID", 3);
```

然后我们给声音源**注册插件**。我们优先使用createjs.WebAudioPlugin插件即Web Audio Api；其次是createjs.HTMLAudioPlugin即html的audio标签功能；最后是createjs.FlashAudioPlugin即flash的audio功能。

```
createjs.Sound.registerPlugins([createjs.WebAudioPlugin, createjs.HTMLAudioPlugin, createjs.FlashAudioPlugin]);
```

我们**标记声音源**为mp3：

```
createjs.Sound.alternateExtensions = ["mp3"];
```

注册的音频是**需要加载**的，我们在它加载完成之后加入一个**回调**进行一些音频处理，在回调中我们可以控制音频的播放，调节音量等。

```
createjs.Sound.on("fileload", handleLoad);
```

在回调函数里面，我们把AbstractSoundInstance类引出来以实现后续的**控制效果**：

```
var myInstance;
function handleLoad() {
  myInstance = createjs.Sound.play("myID", {loop:-1});
  myInstance.volume = 1;
}
```

在某个适当的时机，我们执行**audioFadeOut函数**来修改它的volume以实现音频的淡出效果：

```
function audioFadeOut() {
  var count = 50;
  var interval = setInterval(function() {
    if(count < 0) {
      myInstance.paused = true;
      clearInterval(interval);
    } else {
      myInstance.volume = count/50;
    }
  }, 70);
}
```




























