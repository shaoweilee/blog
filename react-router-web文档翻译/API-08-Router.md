# Router

Router是所有路由器组件的通用底层接口，通常，我们经常用下列高级路由器中的一种：

- <BrowserRouter>
- <HashRouter>
- <MemoryRouter>
- <NativeRouter>
- <StaticRouter>

使用底层`<Router>`最典型的场景是在用状态管理库（Redux或Mobx）同步自定义的history时，注意这并不是要求一定要用状态管理库，而是为了和React Router做深整合。

```jsx
import { Router } from 'react-router'
import createBrowserHistory from 'history/createBrowserHistory'

const history = createBrowserHistory()

<Router history={history}>
  <App/>
</Router>
```

## history: object

一个用来做导航的history对象：

```jsx
import createBrowserHistory from 'history/createBrowserHistory'

const customHistory = createBrowserHistory()
<Router history={customHistory}/>
```

## children: node

要渲染的单一子元素：

```jsx
<Router>
  <App />
</Router>
```

