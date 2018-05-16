## 概述

前几天找react的技术书籍看，找到[《react精粹》](https://book.douban.com/subject/26809233/)和[《深入浅出React和Redux》](https://book.douban.com/subject/27033213/)。由于《react精粹》是**外国人**写的，再加上译者**奇舞团**我也比较喜欢，所以就读这本书了。不过老实说，书有些过时了，但是里面的**基础思想和方法**讲的很有意思，正是我所需要的。所以我把它们记录下来，供以后开发时参考，相信对其它人也有用。

PS：第一章就不要看了，太老了！！！

### 关注点分离

我们之所以要用html，css和js这三种不同的技术来编写web应用程序，是因为想分离出三个不同的**关注点**：
- 内容（html）
- 样式（css）
- 逻辑（js）

### web页面和单页应用

web页面和单页应用在于单页应用会**在加载完文档对象模型（DOM）之后使用web浏览器提供的Javascript DOM API 来操纵DOM以创建额外的元素**。

然而，通过javascript操纵DOM有2个问题：
1. **编程风格**很重要，不同的编程风格会导致代码库变得难以维护。
2. DOM的更改是**很慢**的。

那么，在什么情况下我们会操纵DOM呢？
1. **用户事件**：用户按下键盘、点击鼠标、滚动屏幕、缩放页面尺寸等。
2. **服务端事件**：应用程序从服务器接收到数据或错误等。

### 一些内置函数

React.createElement有一个**children参数**，描述了这个元素应当含有的子元素（如果子元素存在的话）。

react有一些内置的函数来创建**通用的HTML标签**，比如React.DOM.ul()、React.DOM.li()、React.DOM.div()等。

ReactDOM.render()就是react里面把虚拟DOM渲染到真实DOM的函数，它的第三个参数能够接收一个回调函数。

### 客户端渲染和服务端渲染

一般的react页面是客户端渲染页面，它初次打开时很慢的，原因如下：
1. 它需要在浏览器中**渲染**虚拟DOM，并把它更新到真实DOM上面。
2. 它常常需要等待服务端的一些**数据**才能更新DOM。

所以，从技术上讲，我们可以在服务端使用React库，并且创建**ReactNode树**，然后把它渲染为一个字符串，最后发送给客户端。

```
//服务端渲染成字符串
var ReactDOMServer = require('react-dom/server');
ReactDOMServer.renderToString(ReactElement);

//服务端渲染成静态页面
var ReactDOMServer = require('react-dom/server');
ReactDOMServer.renderToStaticMarkup(ReactElement);
```

使用服务端渲染的另一个好处是，*方便SEO*。

### 事件处理

React其实并**不会将事件处理函数绑定到DOM节点本身**，相反，它仅在最顶层使用一个事件监听器来监听所有事件，并把它们代理到相对应的事件处理函数。

### state

我们要问自己这么一个问题：**我们不应该在state里面放什么？**

我们可以从组件的state中移除并且不影响用户页面更新的数据有哪些？边问自己边删除数据，直到你完全确定在不影响用户界面正常更新的前提下没有任何数据可删了。

### 规划React

规划React应用时应遵循下面两条简单的原则：
- 每个React组件应该代表一个用户界面元素。它应该封装*最小的可复用元素*。
- 多个React组件应该组成独立的React组件。最终，整个用户页面应该封装成一个React组件。

### 内置样式

可以把一些不需要改动的样式做成**内置样式**，但是要使用驼峰式命名，并且class要改为className。

```
var headerStyle = {
    fontSize: '16px',
    fontWeight: '300',
    display: 'inline-block',
    margin: '20px 10px'
};

<h2 style={headerStyle}>fasdfdf</h2>
```

### 验证React组件的属性

使用React.PropTypes来**验证**React组件的属性。(出于性能原因，PropTypes仅在React的开发版中被使用)

### 改动数据

我们如何在子组件中**改变父组件的数据**？

父组件给子组件传递一个回调函数，子组件通过调用这个回调函数来修改父组件的数据。

### ref

React组件有一个**ref属性**，通过它可以在render函数外面引用该组件。

```
<input
    ref="collectionName" />

componentDidMount: function() {
    this.refs.collectionName.focus();
}
```



