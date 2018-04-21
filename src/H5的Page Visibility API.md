# H5的Page Visibility API

## 概述

哈哈，又学了一个H5的API。今天突然对动态获取网页的选中状态很感兴趣，然后去查了下，发现真的有个API控制它——Page Visibility API。于是把学到的东西记录下来，供以后开发时参考，相信对其他人也有用。

具体可以参考：[MDN Page Visibility API](https://developer.mozilla.org/zh-CN/docs/Web/API/Page_Visibility_API)

### 示例

立刻把这个API用到了我的博客上面了。怎么查看效果呢？
1. 确保浏览器是**最新版本**(IE要IE10以上)。
2. 打开我的博客，然后点击浏览器的**其它标签**，就可以看到我的博客网页的标签的标题变成了(●—●)喔哟，崩溃啦！
3. **回到**我的博客的网页，就可以看到(/≧▽≦/)馒头加梨子！

### 代码

原理：当用户最小化网页或者浏览到其他标签的网页时，API将发送一个关于当前页面的可见信息的事件**visibilitychange**。通过监测这个事件，可以做出响应。然后根据**document.hidden**获取页面的可见状态，通过这个状态来动态修改标题。

**注意：**这是原生js，不需要jQuery等插件。

```
<script>
(function() {
    'use strict';
    // Set the name of the "hidden" property and the change event for visibility
    var hidden, visibilityChange;
    if (typeof document.hidden !== "undefined") {
      hidden = "hidden";
      visibilityChange = "visibilitychange";
    } else if (typeof document.mozHidden !== "undefined") { // Firefox up to v17
      hidden = "mozHidden";
      visibilityChange = "mozvisibilitychange";
    } else if (typeof document.webkitHidden !== "undefined") { // Chrome up to v32, Android up to v4.4, Blackberry up to v10
      hidden = "webkitHidden";
      visibilityChange = "webkitvisibilitychange";
    }

    // If the page is hidden, change the title;
    function handleVisibilityChange() {
      if (document[hidden]) {
        document.title = '(●—●)喔哟，崩溃啦！';
      } else {
        document.title = '(/≧▽≦/)馒头加梨子！';
      }
    }

    // Warn if the browser doesn't support addEventListener or the Page Visibility API
    if (typeof document.addEventListener === "undefined" || typeof document[hidden] === "undefined") {
      console.log("This demo requires a modern browser that supports the Page Visibility API.");
    } else {
      // Handle page visibility change
      document.addEventListener(visibilityChange, handleVisibilityChange, false);
    }
})();
</script>
```











