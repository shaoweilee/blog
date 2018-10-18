# Basic Components

在React Router中，有三种类型的组件：路由器组件，路由匹配组件，导航组件。

在web应用中，上述三种组件都应当从`react-router-dom`中引入：

```js
import { BrowserRouter, Route, Link } from "react-router-dom";
```

## Routers（路由器组件）

在每个React Router应用中，核心应当是路由器组件。对于web项目，`react-router-dom`提供了`<BrowserRouter>`和`<HashRouter>`两种路由器组件。它俩都会为你创建一个包含浏览历史的`history`对象。一般来说，如果你有服务器来响应请求，那就用`<BrowserRouter>`，如果用静态文件服务，那就用 `<HashRouter>` 。

```
import { BrowserRouter } from "react-router-dom";
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  holder
);
```

## Route Matching（路由匹配组件）

有两种路由匹配组件：`<Route>` 和 `<Switch>`。

路由匹配通过比较 `<Route>`的`path` prop和当前页面的`location.pathname`来发挥作用。当 `<Route>`匹配上了，它会渲染它对应的内容，如果不匹配了，它渲染一个`null`。不带path的 `<Route>`总是匹配。

```js
import { Route, Switch } from "react-router-dom";
// 当 location = { pathname: '/about' }时：
<Route path='/about' component={About}/> // 渲染 <About/>组件
<Route path='/contact' component={Contact}/> // 渲染 null
<Route component={Always}/> // 渲染 <Always/>组件
```

无论何时，如果你想根据location来渲染对应组件，就可以包一个`<Route>`。当然了，把`<Route>`们列在一起更有意义。 `<Switch>`组件就是用来把 `<Route>`们整合在一起的。

```
import { Route, Switch } from "react-router-dom";
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Switch>
```

并不是非得用 `<Switch>`组合 `<Route>`们，但它的确有用。`<Switch>`会在所有其包含的`<Route>`中遍历，然后只渲染第一个匹配当前location的组件。当有好几个`<Route>`的`path`都匹配到了相同的`pathname`，或者在路由切换时使用动画，或者确认当前location不匹配任何一个`<Route>`（就可以渲染“404”组件啦）， `<Switch>`就很有用了。

```
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
  {/* 当上面的都不匹配, <NoMatch> 就会被渲染 */}
  <Route component={NoMatch} />
</Switch>
```

### 渲染组件时用的props

如果`<Route>`匹配到了，你可以在三种渲染方式中选一种：`component`，`render`和`children`。可以查看本系列的API部分的翻译查看详细信息，但是目前我们先说说`component` 和`render`，因为这些是你大部分情况下总会用到的。

当你有一个写好的组件想要渲染出来，比如React组件或者无状态的函数式组件都行，就应该用`component` 。

当你不得不用函数的方式，传递一个作用域内的变量给想渲染的组件时，你应该用`render`。**不要用**`component`来给组件函数式地传递变量！

```jsx
const Home = () => <div>Home</div>;

const App = () => {
  const someVariable = true;

  return (
    <Switch>
      {/* 要这样 */}
      <Route exact path="/" component={Home} />
      <Route path="/about" 
        render={props => <About {...props} extra={someVariable} />}
      />
      {/* 不要这样 */}
      <Route path="/contact"
        component={props => <Contact {...props} extra={someVariable} />}
      />
    </Switch>
  );
};
```

## Navigation（导航组件）

React Router提供 `<Link>`组件，来为你的app创建导航链接。当你渲染 `<Link>`组件，其实就是渲染了一个`<a>`。

```jsx
<Link to="/">Home</Link>
// <a href='/'>Home</a>
```

 `<NavLink>`是一种特殊的`<Link>`，当它的`to`属性匹配了当前的location，它可以把自己的样式设为“active”的。

```jsx
// location = { pathname: '/react' }
<NavLink to="/react" activeClassName="hurray">
  React
</NavLink>
//最终渲染为：
// <a href='/react' className='hurray'>React</a>
```

当你想强制导航，可以用`<Redirect>`组件。`<Redirect>`渲染时，会导航到它的`to`属性所指向的地址。

```jsx
<Redirect to="/login" />
```

