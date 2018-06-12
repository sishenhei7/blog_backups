## 概述

之前看[redux官方文档](https://redux.js.org/basics/usage-with-react)真是看得一脸懵逼，现在自认为会用了，于是来总结一下用法，供以后开发时参考，相信对其他人也有用。

不得不说，用了redux之后感觉挺爽的，有如下优点：
1. 组件大多是函数组件非常**方便测试**。
2. **免去了一层层传递props**的困扰，如果想要数据，直接建一个容器组件即可，不需要对原组件做任何改动。
3. **扩展性很强**，有新动作的时候，只需在action.js里面添加，然后在reducer.js里面注册即可。

### index.js

首先把index.js改成下面这个样子，用一个有store属性的Provider包裹。

```
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'
​
const store = createStore(todoApp)
​
render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

注意：以后添加路由，redux-thunk的时候也是在这里改。当规模大了的时候会**独立出去**一个root.js组件。

### action.js

然后当然是注册动作了。

```
//有参数的动作
export const ADD_TODO = 'ADD_TODO'
export function addTodo(text) {
  return { type: ADD_TODO, id: todoId++, text}
}

//没有参数的动作
export const SHOW_ALL = 'SHOW_ALL'
export function showAll() {
  return { type: SHOW_ALL }
}
```

动作其实就是**指令**，告诉redux我需要进行某种改变。一般是没有参数的动作，不需要传递数据给store，另外一种是传递参数的动作，需要传递数据给store。

这里涉及到redux的一种理念，就是ui的改变不是通过各组件用它们的方法各自处理数据进行改变的，而是通过store处理数据，然后组件只负责渲染即可。这就是react是**声明式框架**而不是**命令式框架**的原因（各组件只是一种声明，并没有命令）。

### reducer.js

注册动作之后，我们就有了指令了，但是**store接收到了指令之后要怎么做**？这就是reducer.js的功劳了。一般的reducer是类似下面这个样子的：

```
const post = (state = '无数据', action) => {
  switch(action.type) {
    case RECEIVE_POSTS:
      return action.title
    case RECEIVE_ING:
      return action.text
    case RECEIVE_ERROR:
      return action.error
    default:
      return state
  }
}
```

它有如下特点：
1. 一个state的初始值。
2. 一个switch处理action。
3. 通过处理带参数或者不带参数的action，返回新的state。

从**store这个层面**上来说，经过了reducer之后，里面的数据得到了更新，做出了响应，它的任务**全部完成**了。但是从**组件这个层面**来说，它却又要处理这3个问题：
1. 它如何拿到store里面的数据。
2. 它如何发出指令（action）。
3. store数据的改变怎么导致它的重新渲染。

这三个问题利用容器可以解决。

### container.js

容器就是组件和store之间的中介。为什么需要容器？因为**关注点分离**的思想，在组件层面，我们只关注怎么渲染的，在容器层面，我们只关注数据怎么传递的。

一般的容器需要拿到store的数据，并且发出指令（action），下面的一个一般的容器的写法：

```
const mapStateToProps = state => ({
  list: state
})

const mapDispatchToProps = dispatch => ({
  toggleState: (id) => dispatch(toggleState(id))
})

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```

这样，我们就解决了前2个问题。然后，由于我们把store里面的数据通过props传递给了组件，所以如果state的相关数据更新的话，传递给组件的props也会更新，于是组件重新渲染，这就解决了第三个问题。

有时候，我们并不需要传递store里面的数据进来，**只需要发布几个命令**即可，这个时候只需要把mapStateToProps换成null即可，示例如下：

```
const mapDispatchToProps = dispatch => ({
  addTodo: (text) => dispatch(addTodo(text))
})

export default connect(
  null,
  mapDispatchToProps
)(AddTodo)
```

### 组件

组件可以不作任何改动，只改成**箭头函数**就行了。如下所示：

```
import React from 'react'

const Posts = ({ title, nextText, nextPost }) => {
  return (
    <div>
      <span>{ title }</span>
      <button
        onClick={() => {
          nextText();
          nextPost();
        }}> next </button>
    </div>
  )
}

export default Posts
```















