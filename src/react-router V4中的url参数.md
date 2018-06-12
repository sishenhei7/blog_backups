## 概述

之前写过[react在router中传递数据的2种方法](http://www.cnblogs.com/yangzhou33/p/8495459.html)，但是有些细节没有理清楚，现在补上，记录下来，供以后开发时参考，相信对其他人也有用。

参考资料：[stackoverflow react router redux url](https://stackoverflow.com/questions/45537722/react-router-redux-url)

### match

如果使用下面这种方式切换路由，那么参数可以通过props.match.params.filter拿到。

```
<Route path='/:filter' component={App} />

<Link to='active'> Active </Link>
```

不过要注意2点：
1. filter可以变成**其它参数**，这个时候props.match.params.filter里面的filter也要变成相应的参数。
2. 只能在component={App}里面的**App组件中**通过props拿到filter这个参数，在其它组件中拿不到。（即不是组件自身渲染时通过解析url拿到参数的，而是通过props传递过来的。）

### location

如果使用下面这种方式切换路由，那么参数data可以通过props.location.data.name拿到。

```
const linkActive = {
    pathname: 'active',
    data: {name: 'haha'}
}

<Route path='/:filter' component={App} />

<Link to={ linkActive }> Active </Link>
```

注意：
1. 如果要拿**filter**还是通过props.match.params.filter拿到。
2. 只能在component={App}里面的**App组件中**通过这种方式拿参数。

### 其它

那么我们怎么在App之外的组件中获得这个参数呢？
1. 一个办法是让App组件通过**传递props**给这个组件。
2. 另一个办法是让App组件把这个参数存入**redux**里面。
3. 还有一个办法是在这个组件前面**加一个路由**。（猜想的，没试过）比如这么使用：

```
<Route path='/:filter' component={List} />
<List />
```
