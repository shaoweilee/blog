# Redirect

渲染`<Redirect>`组件会跳转到一个新的location。在历史记录堆栈中，这个新的location会把当前location覆盖掉，就像服务端发送的重定向（HTTP 3xx）一样。

```jsx
import { Route, Redirect } from 'react-router'

<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard"/>
  ) : (
    <PublicHomePage/>
  )
)}/>
```

## to: string

要重定向至的URL。只要是[`path-to-regexp@^1.7.0`](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0)理解的合法URL就行。所有在to中使用的URL参数必须由`from`中的覆盖。

```jsx
<Redirect to="/somewhere/else" />
```

## to: object

要重定向的location。

```jsx
<Redirect
  to={{
    pathname: "/login",
    search: "?utm=your+face",
    state: { referrer: currentLocation }
  }}
/>
```

我们在`to`中定义了`state`对象，在重定向至的目标组件中，这个`state`对象可以通过`this.props.location.state`访问到（可以用来做路由传参，但不是路由传参的唯一做法）。

## push: bool

如果为`true`，重定向时，会在历史记录堆栈中是添加记录，而不是覆盖记录。

```jsx
<Redirect push to="/somewhere/else" />
```

## from: string

要重定向的路径名。所有 [`path-to-regexp@^1.7.0`](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0)理解的合法URL路径都行。所有匹配到的URL参数会传给`to`，必须包含在`to`中使用的所有参数，不被`to`使用的话会被忽略。

`from`只能在 `<Switch>`组件下渲染 `<Redirect>` 时用到，查看本系列的`<Switch children>`了解详细信息。

```jsx
<Switch>
  <Redirect from='/old-path' to='/new-path'/>
  <Route path='/new-path' component={Place}/>
</Switch>

// 带参数的<Redirect>
<Switch>
  <Redirect from='/users/:id' to='/users/profile/:id'/>
  <Route path='/users/profile/:id' component={Profile}/>
</Switch>
```

## exact: bool

精确匹配`from`，等于`<Route>`组件中的`exact`。

## strict: bool

严格匹配`from`，等于`<Route>`组件中的`strict`。