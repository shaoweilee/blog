# Redux Integration

Redux是React生态系统中重要的一部分，我们力求React Router和Redux无缝结合。

## 阻止更新

一般来说，React Router和Redux一起用很不错。但有时，app会有那种当location改变时不会更新的组件（子路由组件或激活的导航链接不会更新）。

这种情况发生在：

1. 组件通过`connect()(Comp)`的方式链接redux；
2. 组件不是“路由组件”，这意味着不是这样渲染的：`<Route component={SomeConnectedThing}/>`

原因在于，Redux实现了`shouldComponentUpdate` ，如果从路由器组件那儿没有接收到props，就表明一切都没变。解决方式是在`connect` 的地方用`withRouter`包裹一下。

```js
// before
export default connect(mapStateToProps)(Something)

// after
import { withRouter } from 'react-router-dom'
export default withRouter(connect(mapStateToProps)(Something))
```

## 深层整合

有些人想：

1. 将路由数据和store（redux中存储数据的对象）同步，并从store中访问；
2. 可以通过dispatch actions（派发事件，redux中对存储的数据进行修改的方式，进而重新渲染UI）来导航；
3. 在Redux开发工具中提供对路由变化的调试。

以上所有，需求React Router和Redux的深层整合。

我们的建议是，**完全不要把路由存储在Redux的store中**，理由如下：

1. 路由数据已经是你组件的一个prop了，无论它来自store还是路由器组件，你组件的大部分代码都是相同的；
2. 大多数情况下，你可以使用`Link`，`NavLink` 和 `Redirect`来导航。有时，在某些异步任务后，需要程序自己跳转或导航。比如，在用户提交一个登陆表单之后，经过程序异步验证，假如验证通过，那么是需要跳转到一个新的页面的。解决方法是把`history` 对象（提供给所有路由组件）包含在action的payload中（通常，antion是自定义的对象，可以塞很多东西），异步处理函数可以用它来跳转；
3. 路由变化貌似对调试不是问题。最明显的问题出现在调试router和store同步时，但如果你不同步它们就不会出现这种问题了……

但如果你强烈想同步路由和store，那就试试[Connected React Router](https://github.com/supasate/connected-react-router)。