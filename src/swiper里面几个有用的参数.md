## 概述

这是我自己用swiper和看别人官网源码用swiper总结出来的，供以后开发时参考，相信对其他人也有用。

### observeParents

有时我们会改变swiper的**父级元素**，比如页面的resize方法；也有时我们会动态给swiper**添加一些子元素**。这个时候，需要如下设置swiper，才能正常运行：

```
<script language="javascript">
var mySwiper = new Swiper('.swiper-container',{
pagination : '.swiper-pagination',
observer:true,
observeParents:true,
})
</script>
```

### paginationBulletRender回调函数

这个回调函数用于**完全自定义分页器的指示点**，接受指示点索引和指示点类名作为参数。常用于分页器里面要填充一些文字内容的情形。

```
<script>
var swiper = new Swiper('.swiper-container', {
  pagination: '.swiper-pagination',
  paginationClickable: true,
  paginationBulletRender: function (swiper, index, className) {
      return '<span class="' + className + '">' + (index + 1) + '</span>';
  }
});  
</script>
```

### lazyLoading懒加载

swiper有一个参数是**preloadImages**，它的默认值为true，默认会加载所有图片。如果想懒加载图片，就可以使用**lazyLoading**参数开启图片的懒加载。

这个时候需要将图片img标签的src改写成**data-src**，并且增加类名**swiper-lazy**。背景图的延迟加载则增加属性**data-background**。并且给div增加一个**swiper-lazy-preloader类**。实例如下：

```
<div class="swiper-container">
    <div class="swiper-wrapper">
        <div class="swiper-slide">
            <img data-src="path/to/picture-1.jpg" class="swiper-lazy">
            <div class="swiper-lazy-preloader"></div>
        </div>
        <div class="swiper-slide">
            <img data-src="path/to/picture-2.jpg" class="swiper-lazy">
            <div class="swiper-lazy-preloader"></div>
        </div>
        <div class="swiper-slide">
            <div data-background="path/to/picture-3.jpg" class="swiper-lazy">slide3</div>
        </div>
    </div>
</div> 
<script> 
    var mySwiper = new Swiper('.swiper-container',{
    lazyLoading : true,
})
</script>
```

### fade效果

swiper还支持**各种切换效果**，比如"fade"（淡入）"cube"（方块）"coverflow"（3d流）"flip"（3d翻转）。实例如下：

```
<script language="javascript"> 
var mySwiper = new Swiper('#swiper-container1',{
effect : 'fade',
})
var mySwiper2 = new Swiper('#swiper-container2',{
effect : 'cube',
})
var mySwiper3 = new Swiper('#swiper-container3',{
effect : 'coverflow',
slidesPerView: 3,
centeredSlides: true,
})
var mySwiper4 = new Swiper('#swiper-container4',{
effect : 'flip',
})
</script>
```

### superslide

在PC端建议使用更加人性化的**superslide插件**，因为它支持**hover切换**。

