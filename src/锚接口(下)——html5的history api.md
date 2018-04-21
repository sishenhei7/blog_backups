# 锚接口(下)——html5的history api

## 概述

虽然html5的history api是H5专门用来解决记录历史记录和单页面的方法，但是很多老式的浏览器并不支持它，所以一般遇到老式的浏览器会做一个polyfill使用之前的hashchange方法。

另一方面，html5的history api在实际使用的时候会和之前的hashchange方法的用法有稍微的不同。

### 说明

1. 主要使用history.pushState改变url，它的调用格式是```history.pushState(data, title, path);```，其中data是一个自己定义的数据对象，title是标题，现在大部分浏览器还不支持，path是路径。
2. 用这个方法改变url的时候，页面并不刷新。
3. 用这个方法改变url的时候，并**不会**触发onpopstate事件，只有在使用history.go或者history.back等方法的时候才会触发onpopstate事件，比如说浏览器按倒退键。(*这一点和hashchange方法不同*)
4. 触发onpopstate事件的时候可以用event.state获得data这个对象。

### 解决方案

#### SPA怎么路由

具体和hashchange方法类似，唯一不同的地方是，在用history.pushState改变url的时候，由于不会触发onpopstate事件，所以一些函数要放在history.pushState改变url之后处理(以前是直接由hashchange事件处理)。

#### 复制url并在另一个页面打开

如果是用#符号的话，那么方法类似hashchange。

但是新的history api可以*摒弃#字符*，比如说react-router里面就是这么做的，*具体怎么解决我还没有弄清楚*。

### 例子

下面是我编写的一段测试代码供大家参考。直接复制并存为html文件，然后在浏览器打开即可。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
    <script type="text/javascript">
    //hashchange事件，如果有hash值，则输出到oDiv。
    function onPopstate(event) {
      if(history.state) {
        console.log(history.state.next);
      }
      if(history.state && history.state.next === 1){
          $('#div1').hide();
          $('#div2').show();
          $('#div3').hide();
        } else if(history.state && history.state.next === 2) {
          $('#div1').hide();
          $('#div2').hide();
          $('#div3').show();
        } else {
          $('#div1').show();
          $('#div2').hide();
          $('#div3').hide();
        }
    }
    //页面加载
    window.onload=function (){
        //加载时触发一次hashchange事件
        $(window)
            .bind( 'popstate', onPopstate )
            .trigger( 'popstate' );
        //点击事件，把数组装在hash里面
        document.getElementById("input1").onclick=function(){
            history.pushState({next: 1}, null, 'next1.html');
            $('#div1').hide();
            $('#div2').show();
            $('#div3').hide();
        };
        document.getElementById("input2").onclick=function(){
            history.pushState({next: 2}, null, 'next2.html');
            $('#div1').hide();
            $('#div2').hide();
            $('#div3').show();
        };
        document.getElementById("input3").onclick=function(){
            history.pushState('', null, 'index.html');
            $('#div1').show();
            $('#div2').hide();
            $('#div3').hide();
        };
    }
    </script>
</head>
<body>
    主页=模块1，模块2页=模块2，模块3页=模块3
    <input type="button" value="去模块2页" id="input1" />
    <input type="button" value="去模块3页" id="input2" />
    <input type="button" value="去主页" id="input3" />
    <div id="div1">模块111111111111111111</div>
    <div id="div2">模块222222222222222222</div>
    <div id="div3">模块333333333333333333</div>
</body>
</html>

```

### 其它

1. 单页面路由实际上和我的demo不一样，不太运用history.pushState里面的data参数，而是直接分析url做正则匹配，不过这个data参数在别的地方有很大用处。
2. 一般的路由和我的demo里面不一样，不是用按钮而是用a链接，那个时候直接在click的时候阻止默认跳转即可。
3. history.pushState里面的data参数储存在sessionStorage里面。












