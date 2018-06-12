## 概述

最近学习redux，打算用redux-thunk给todo添加异步获取数据组件。记录下来，供以后开发时参考，相信对其他人也有用。

**注意：**
1. 在todo**下方**，我异步获取我的react博客的标题，点击**next按钮**获取下一个标题。
2. 感觉redux-thunk只是允许一个action返回一个异步函数，这个异步函数在一段时间获取数据后会**dispatch另一个action**从而达到更新store的state的效果。仅此而已。

### 代码

代码请见[我的github](https://github.com/sishenhei7/blog_server/tree/master/demo/my-todo-async-data)

组织架构如下图：

![react_todo图](http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_todo_thunk.png)
