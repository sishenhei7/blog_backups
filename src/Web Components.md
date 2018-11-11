## 概述

偶然发现了一个叫**Web Components**的东东，貌似是W3C自己定义的一个组件化写法，通过Web Components我们可以写一个组件而不影响其它代码，从而实现了代码的复用。我简单地学习了一下，记录下来，供以后开发时参考，相信对其他人也有用。

参考资料：
[mdn Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)

### Custom elements

**Custom elements**扩展了html里面的标签，一方面允许我们修改html里面的标签，另一方面允许我们**自定义html里面的标签**。

html里面的标签可以转换为**json格式**，比如下面的html代码可以转换为json的格式：

```
<button class="btn btn-blue">
    <em>Confirm</em>
</button>
```

转换成的json格式如下所示：

```
{
    type: 'button',
    props: {
        className: 'btn btn-blue',
        children: [{
            type: 'em',
            props: {
                children: 'Confirm'
            }
            }]
    }
}
```

类似地，利用Custom elements提供的相关API，我们可以用js的格式来**定义html里面的标签**。示例如下：

```
//首先定义一个组件类
class PopUpInfo extends HTMLElement {
    constructor() {
        super();

        //在这里用js写html代码，顶层为shadow
        const shadow = this.attachShadow({mode: 'open'});
        const wrapper = document.createElement('span');
        wrapper.setAttribute('class', 'wrapper');
        //然后写组件的其它html元素和属性等。

        //在这里写样式
        const style = document.createElement('style');
        style.textContent = `
            .wrapper {
                position: relative;
            }
            //然后写其它样式
        `;

        //最后把它们整合在一块儿
        shadow.appendChild(style);
        shaow.appendChild(wrapper);
        //然后整合其它元素
    }
}

//最后把标签放在自定义标签为popup-info的标签那里
customElements.define('popup-info', PopUpInfo);
```

有了上面的js，我们就可以在html里面使用**popup-info标签**了，实例如下：

```
<popup-info img="img/alt.png" text="Your card validation code (CVC)
  is an extra security feature — it is the last 3 or 4 numbers on the
  back of your card.">

<popup-info img="img/alt2.png" text="hahahaha.">
```

想写多少个就写多少个，里面的参数用js里面定义的属性来填充。

类似的，也可以改造现有的html标签，比如p标签，这里就不写实例了。

### Shadow DOM

Shadow DOM的意思是，虽然我们在html里面写的是XX标签，但是这个标签在被解析的时候，会被加上一些**额外的属性或者dom元素**，比如我们给video标签加上一个controls属性，它在被浏览器解析的时候就会自动加上一些控制条，这些控制条的结构时浏览器额外生成的。

Shadow DOM的API允许我们自定义一些额外生成的元素。比如上面的例子里面有这么一段代码：

```
const shadow = this.attachShadow({mode: 'open'});
```

它的作用就是生成一个名字为**shadow的Shadow DOM**，然后我们可以在这个shadow DOM里面用js添加一些额外html标签，如上面的例子所示。

上面代码中的mode有2个值，一个是**open**，表示可以在外部用js获取这个shadow元素；另一个是**closed**，表示不能在外部用js获取这个shadow元素。

### Templates and Slots

有很多模板语言比如Handlebars，artTemplate等等，它们可以为我们打造一个html模板，还有其它一些附加的渲染功能。

而浏览器中本身就内置**template和slot标签**来给我们定义模板的。缺点是只能定义模板而已，而且功能有限。下面来看看怎么用这个标签。

首先我们在html里面写上**template标签**：

```
<template id="my-paragraph">
  <p>My paragraph</p>
</template>
```

这个模板本身不会渲染。但是我们在js里面能够获取这个模板，然后修改里面的内容，最后插入到DOM结构里面去。

```
let template = document.getElementById('my-paragraph');
let templateContent = template.content;
document.body.appendChild(templateContent);
```

用template标签制作模板有很大的局限性，因为我们只能修改里面的文字内容。如果我们想在里面替换标签怎么办呢？这个时候就用到**slot标签**了。

和template标签的写法一样，先在html里面引入：

```
<slot name="my-text">My default text</slot>
```

然后我们可以在自定义组件里面这么用：

```
<my-paragraph>
  <ul slot="my-text">
    <li>Let's have some different text!</li>
    <li>In a list!</li>
  </ul>
</my-paragraph>
```

当然，template和slot标签的神奇之处必须结合自定义标签才能体现出来，比如上面slot的例子。














