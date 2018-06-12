## 概述

最近腾讯云播放器进行了**更新**，增加了**TCplayer**，支持点播播放。由于工作需要，了解了一下TCplayer，把心得记录下来，供以后开发时参考，相信对其他人也有用。

参考文档：
[TCPlayer开发文档](https://cloud.tencent.com/document/product/266/14603)
[TCPlayer使用文档](https://cloud.tencent.com/document/product/266/14424)

### TCplayer

TCplayer可以播放发布在腾讯云点播中的视频。所以要先上传视频到腾讯云点播，然后它会给你一个fileID和appID。前端就利用这个**fileID和appID**来播放视频。

由于 MP4 和 HLS（m3u8）是目前在 PC 浏览器和手机浏览器上支持程度最广泛的格式，所以腾讯云的视频点播平台最终会把上传的视频发布为 **MP4** 和 **HLS（m3u8）** 格式。

### 引入播放器

首先在页面合适的地方引入文件：

```
<link href="//imgcache.qq.com/open/qcloud/video/tcplayer/tcplayer.css" rel="stylesheet">

<!-- 在 Chrome Firefox 等现代浏览器中通过 HTML5 播放 hls -->
<script src="//imgcache.qq.com/open/qcloud/video/tcplayer/lib/hls.min.0.8.8.js"></script>
<script src="//imgcache.qq.com/open/qcloud/video/tcplayer/tcplayer.min.js"></script>
```

然后放置播放器容器，注意**必须为video标签**。

```
<video id="player-container-id" preload="auto" playsinline webkit-playinline x5-playinline>
</video>
```

最后在页面初始化的代码中加入以下初始化脚本，传入获取到的fileID和appID。

```
var player = TCPlayer('player-container-id', {
    // player-container-id 为播放器容器ID，必须与html中一致
    fileID: '4564972818956091133', // 请传入需要播放的视频filID 必须
    appID: '1253668508' // 请传入点播账号的appID 必须
  });
```

### 自定义宽高

像这种第三方视频组件，难点就是自定义它的宽高。我们有2种方法，一种方法是在脚本中用js去取它的宽高，代码如下所示：

```
var player = TCPlayer('player-container-id', {
    // player-container-id 为播放器容器ID，必须与html中一致
    fileID: '4564972818956091133', // 请传入需要播放的视频filID 必须
    appID: '1253668508', // 请传入点播账号的appID 必须
    width: $('player-container-id').width(),
    height: $('player-container-id').height()
  });
```

上面这种方法有一个缺点就是视频容易被拉伸。所以我们更推荐用第二种方法：**设置视频的宽高自适应**。我们在css里面设置视频的宽高自适应，代码如下：

```
/* 通过 css 设置播放器尺寸 这时<video>中的宽高属性将被覆盖*/
#player-container-id {
  width: 100%;
  max-width: 100%;
  height: 0;
  padding-top: 56.25%; /* 计算方式：播放器以16：9的比率显示，这里的值为 9/16 * 100 = 56.25  */
}

/* 外部容器也需要是自适应的*/
#wrap {
  width: 80%;
  margin: 0 auto;
}
```

### 切换fileID播放

我们常常有这种需求，就是需要更换视屏播放内容。TCPlayer有一个API, **loadVideoByID(args) 方法**，可以更换视屏内容，代码如下：

```
//其中player就是实例化的TCPlayer
player.loadVideoByID({
  fileID: '', // 请传入需要播放的视频 filID 必须
  appID: '' // 请传入点播账号的 appID 必须
});
```

### 暂停与继续

对于视频，我们常常有这种需求，就是在暂停视频后，点击任何地方，都能够继续播放视频。这个可以利用TCPlayer的**图片贴片功能**实现。

TCPlayer支持配置片头、片中暂停、片尾显示图片贴片,并且可以绑定事件，也可以添加超链接。也就是说，不仅可以实现上述需求，还可以添加超链接跳转等。

方法是**在【控制台】>【Web 播放器管理】选定某个播放器配置，单击贴片栏目进行设置贴片信息**。

示例请看：[图片贴片](https://imgcache.qq.com/open/qcloud/video/tcplayer/examples/vod/tcplayer-vod-image-patch.html)




