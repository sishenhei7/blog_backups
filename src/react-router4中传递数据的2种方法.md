# react-router4中传递数据的2种方法

## 概述

不传递数据叫什么单页面应用，渲染模块还需要http请求算什么单页面应用。

本文总结了react-router4中使用BrowserRouter时传递数据的两种方法，供以后开发参考，相信对其他人也有用。

### 使用Link

Link是react-router4中很常见的一个类，很多人在页面跳转的时候都会用到它。在用Link的时候传递数据的方法如下：

```
import { Link } from 'react-router-dom';

//不传递数据
<Link to={模块路径}>{内容}</Link>

//传递数据,在目标模块用this.props.location.state调用数据。
    <Link to={{
        pathname: {模块路径},
        state: {数据}
    }}>{内容}</Link>
```

### 使用history.push

history是H5中引入的，以前人们都用hash。

react-router4中有好几种方法使用history.push。下面我介绍**使用BrowserRouter时**使用的方法。

```
import { withRouter } from 'react-router-dom';

//不传递数据
this.props.history.push({目标模块路径});

//传递数据,在目标模块用this.props.location.state调用数据。
this.props.history.push({
    pathname:{目标模块路径},
    state:{数据}
    }）

export default withRouter(自身模块名)
```

### 区别

在**点击的时候跳转**并传递数据：既可用Link方法，也可以用history.push方法(需要结合Onclick方法，在点击事件的回调函数里面调用history.push)。

用**js控制跳转**并传递数据：只能用history.push方法。(直接在js中使用history.push)

另外说下，在模块中获取路由的/:id中的id：在```this.props.match.params.id```中获取。(其中id可换为其它参数。)







