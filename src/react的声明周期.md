# react的生命周期

## 概述

前几天和同事讨论react，发现对生命周期还是了解的不够深入，于是今天翻看了[react关于生命周期的官方文档](https://reactjs.org/docs/react-component.html)，刚好发现react发布了新版本，并且对生命周期做了修改，于是认真阅读了一下，并记录下来，供以后开发时参考，相信对其他人也有用。

### 总述

以前觉得生命周期的英文太多了，要记很麻烦。现在看文档才发现，有些小窍门。

带有will的都是在动作发生前执行，带有did的都是在动作发生之后执行。然后再记住几个关键词：mounting，updating，unmounting，error handling。

### Mounting阶段

#### constructor()

constructor方法在组件建立前被调用。
1. 在这个方法里面，应该先调用super(props)，否则this.props就会未定义。
2. 这个方法的主要用途是，初始化props，state和绑定方法。
3. 如果在里面用props为state赋值，那么最好把state提升，也可以在getDerivedStateFromProps()里面用props为state赋值。

#### static getDerivedStateFromProps()

在下列三种情况下，会调用getDerivedStateFromProps方法：
1. 组件实例化。
2. 组件的props发生变化。
3. 父组件重新渲染。

this.setState()不会触发getDerivedStateFromProps()，但是this.forceUpdate()会。

#### UNSAFE_componentWillMount()

这个方法在render方法执行前被调用。官方不建议用这个方法，所以给它加了一个UNSAFE前缀。官方建议把要在这里面写的内容放到constructor()或者componentDidMount()里面。

另外，这个方法是唯一的服务端渲染钩子。

#### render()

当调用render的时候，组件会检查props和state并返回下列类型中的一个：
1. react元素。
2. 字符串或者数字。
3. Portals。
4. null。(不渲染)
5. Booleans。(不渲染)
6. react.Fragment。

#### componentDidMount()

这个方法会在组件建立之后立即调用。需要DOM节点的初始化应该放在这里。

需要注意的是，在这里调用setState()会发生第二次render，但是这第二次render会发生在浏览器渲染之前，所以用户往往看不到第二次渲染，即使这样，也要小心使用这个方法，因为它会造成性能问题。

### Update阶段

#### UNSAFE_componentWillReceiveProps()

在下列三种情况下，会调用UNSAFE_componentWillReceiveProps方法，但是官方不建议使用这个方法，官方建议使用static getDerivedStateFromProps方法。
1. 组件的props发生改变。
2. 父组件发生重新渲染。

需要注意的是，在初始化props的时候并不会调用这个方法，this.setState()也不会触发这个方法。

#### static getDerivedStateFromProps

在update阶段也会调用一次这个方法。

#### shouldComponentUpdate()

这个方法的默认行为是每当state发生改变的时候就重新渲染组件。

当初始化的时候，这个方法不会被调用，当使用forceUpdate()的时候，这个方法也不会调用。

如果要提升性能的话，建议使用React.PureComponent，它在shouldComponentUpdate()的默认行为中使用了浅比较。你也可以在里面自己写比较方法。

#### UNSAFE_componentWillUpdate()

这个方法会在接受新props和state之后调用。官方不建议在里面调用setState()，要使用的话，建议在getDerivedStateFromProps方法里面使用。

#### render()

在update阶段也会调用一次这个方法。

#### getSnapshotBeforeUpdate()

这个方法会在把渲染结果提交到DOM之前被调用。它可以返回一个参数，这个参数被componentDidUpdate(prevProps, prevState, snapshot)方法的第三个参数接收。

#### componentDidUpdate()

这个方法会在组件更新前被调用，所以最好在这里面操作DOM。

### Unmounting阶段

#### componentWillUnmount

这个方法会在组件被销毁时调用，所以在这里面做一些必要的清理，比如计时器，网络请求，订阅等等。

### Error阶段

#### componentDidCatch()

这个方法在组件catch到各种error之后调用，并进行处理。不要在里面改变数据流，它也不能处理本身抛出的错误。















