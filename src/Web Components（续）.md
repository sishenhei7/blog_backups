## 概述

之前我们介绍了Web Components的基本概念，现在我们给出一个使用Web Components的实例代码，并且对**组件化进行一些思考**。记录下来，供以后开发时参考，相信对其他人也有用。

### 实例代码

参考资料：[web-components-examples](https://github.com/mdn/web-components-examples/blob/master/popup-info-box-web-component/index.html)

html:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Pop-up info box — web components</title>
  </head>
  <body>
    <h1>Pop-up info widget - web components</h1>

    <form>
      <div>
        <label for="cvc">Enter your CVC <popup-info img="img/alt.png" data-text="Your card validation code (CVC) is an extra security feature — it is the last 3 or 4 numbers on the back of your card."></label>
        <input type="text" id="cvc">
      </div>
    </form>

    <script src="main.js"></script>

  </body>
</html>
```

js:

```
// Create a class for the element
class PopUpInfo extends HTMLElement {
  constructor() {
    // Always call super first in constructor
    super();

    // Create a shadow root
    const shadow = this.attachShadow({mode: 'open'});

    // Create spans
    const wrapper = document.createElement('span');
    wrapper.setAttribute('class', 'wrapper');

    const icon = document.createElement('span');
    icon.setAttribute('class', 'icon');
    icon.setAttribute('tabindex', 0);

    const info = document.createElement('span');
    info.setAttribute('class', 'info');

    // Take attribute content and put it inside the info span
    const text = this.getAttribute('data-text');
    info.textContent = text;

    // Insert icon
    let imgUrl;
    if(this.hasAttribute('img')) {
      imgUrl = this.getAttribute('img');
    } else {
      imgUrl = 'img/default.png';
    }

    const img = document.createElement('img');
    img.src = imgUrl;
    icon.appendChild(img);

    // Create some CSS to apply to the shadow dom
    const style = document.createElement('style');
    console.log(style.isConnected);

    style.textContent = `
      .wrapper {
        position: relative;
      }
      .info {
        font-size: 0.8rem;
        width: 200px;
        display: inline-block;
        border: 1px solid black;
        padding: 10px;
        background: white;
        border-radius: 10px;
        opacity: 0;
        transition: 0.6s all;
        position: absolute;
        bottom: 20px;
        left: 10px;
        z-index: 3;
      }
      img {
        width: 1.2rem;
      }
      .icon:hover + .info, .icon:focus + .info {
        opacity: 1;
      }
    `;

    // Attach the created elements to the shadow dom
    shadow.appendChild(style);
    console.log(style.isConnected);
    shadow.appendChild(wrapper);
    wrapper.appendChild(icon);
    wrapper.appendChild(info);
  }
}

// Define the new element
customElements.define('popup-info', PopUpInfo);
```

### 组件化思考

在MV*架构出现之前，组件主要分为两种：一种是**狭义上的组件**，比如UI组件；另一种是**广义上的组件**，即带有业务含义和数据的UI组件组合。

对于UI组件，一定有这3个部分：**结构、样式和交互行为**，分别对应HTML，CSS和Js。

在js中，我们利用class对组件进行封装，然后在使用的时候，我们只需实例化一个组件对象然后传入必要的参数即可。

对于组件的js结构，有以下3个标准：
1. 基本的封装性。我们利用**class**进行封装。
2. 简单的生命周期呈现。比如**constructor和destroy**。
3. 明确的数据流动。这里的数据指的参数，一旦确定参数的值，就能够进行渲染。

上面的组件化会遇到一个问题，就是js里面存在**大量的show，hide和toggle等方法**，当逻辑一旦复杂，就会出现大量的DOM操作，严重影响性能，也加大了维护的难度。

这个时候就出现了**模板引擎**，它使我们在js里面拼装html代码，然后一起插入到html里面去，并且把数据独立出来，也解决了数据与界面耦合的问题。

再然后就是Web Components和MV*的组件化，他们开辟了新的组件建立方式~
























