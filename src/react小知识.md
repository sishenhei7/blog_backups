# react小知识

## 概述

有句话说得很好，**代码是写给人看的，顺便让机器执行而已**。所以我总结了一些写react不太注意的地方，供以后开发时参考，相信对其他人也有用。

### 组件封装

由于组件其实就是React.createElement()函数的语法糖，所以如果一个单一模块要导出多个组件时，可以把它**封装**在一个类里面。

```
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

### 默认props(属性)

如果没有给prop传值，那么他**默认为true**。比如说下面的例子是等价的。

```
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

antd里面有很多组件的属性都是这种默认写法的，但是react官方并**不建议**使用这种类型，因为这会与ES6中的对象shorthand混淆 。ES6 shorthand 中 {foo} 指的是 {foo: foo} 的简写，而不是 {foo: true} 。

### props.children

jsx表达式中可以包含开放标签和闭合标签，**标签中的内容**会被传递给props.children。这个props.children可以是字符串，也可以是js函数，还可以是组件。如果是this.props.children，则表示在渲染的时候**传递给组件的所有子节点**。

比如下面的react-router中的例子，详情请看[Index Routes](https://github.com/reactjs/react-router-tutorial/tree/master/lessons/08-index-routes)。

```
import React from 'react'
import { Link } from 'react-router'

export default React.createClass({
  render() {
    return (
      <div>
        <h2>Repos</h2>
        <ul>
          <li><Link to="/repos/reactjs/react-router">React Router</Link></li>
          <li><Link to="/repos/facebook/react">React</Link></li>
        </ul>
        {this.props.children}
      </div>
    )
  }
})
```

### 组件为函数

在一个module里面，不exports的组件最好**写在外面**。

```
//推荐写法，写在外面，很清晰
function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}

//不推荐
function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <li key={message}> {message} </li>)}
    </ul>
  );
}
```

### 条件渲染的优雅写法

由于false，null，undefined，和 true在渲染的时候会被忽略，所以可以如下**优雅**的写条件渲染。

```
//第一种
<div>
  {showHeader && <Header />}
  <Content />
</div>

//第二种
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

需要注意的是，数值 0会被打印，所以一般写成如下形式：

```
//第一种
<div>
  {!!showHeader && <Header />}
  <Content />
</div>

//第二种
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

### propTypes

可以用propTypes来指定props的**属性的类型**，注意这只在开发模式中有效。

```
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这里必须是一个元素，否则会发出警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
```

### defaultProps

可以用defaultProps给props设置**默认值**，这样如果没有值的时候就不会报错了。

```
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};

// 渲染为 "Hello, Stranger":
ReactDOM.render(
  <Greeting />,
  document.getElementById('example')
);
```





