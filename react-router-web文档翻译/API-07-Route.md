# Route

Route组件大概是在React Router中最重要的了。它最基础的功能是：当`location`匹配到route的`path`时，渲染一些UI。

看看下面的代码：

```jsx
import { BrowserRouter as Router, Route } from 'react-router-dom'

<Router>
  <div>
    <Route exact path="/" component={Home}/>
    <Route path="/news" component={NewsFeed}/>
  </div>
</Router>
```

如果app目前的location是“/”，那么UI结构会类似这样：

```html
<div>
  <Home/>
  <!-- react-empty: 2 -->
</div>
```

如果目前的location是“/news”，那么UI结构会类似这样：

```html
<div>
  <!-- react-empty: 1 -->
  <NewsFeed/>
</div>
```

上述例子中的“react-empty”注释只是React的`null`渲染的实现细节。但对我们的目的来说是有益处的= =。只要app的location匹配到了`route`的`path`，组件就会被渲染，即使它渲染了一个`null`。

## Route render methods

在`<Route>`组件中，有三种渲染的方式：

- `<Route component>`
- `<Route render>`
- `<Route children>`

在不同情境下，每个都有用。但是在每个`<Route>`中，只能用一个。下面会解释为啥会提供三种方式。大多数情况下，用`component`就行了。

## Route props

以上三种渲染方式，都会被传入相同的三个路由props：

- match
- location
- history

查看本系列接下来的部分可获得上述三个props的详细信息。

## component

只有当location匹配时，React 组件才会被渲染。渲染时，会携带路由信息（route props）。

```jsx
<Route path="/user/:username" component={User} />;

const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>;
};
```

当你使用`component`而不是下面要介绍的`render`或`children`时，路由器会从给定的组件中，使用`React.createElement`来创建一个新的React元素。这意味着，如果你为`component`属性提供一个函数，那么在每次渲染时，都会创建一个新的组件。这就导致，现有的组件卸载后，新的组件会被挂载，而不是更新现有的组件。如果要用函数，不要用`component`，用`render`或者`children`。

## render: func

使用`render`，允许你进行方便的“行内渲染”（使用函数），而不会发生上面说的重新加载。

与使用`component`prop时创建一个新的React元素不同，你可以传入一个函数，location匹配到的时候这个函数就会执行。和上面说过的一样，`render`会与`component`接收到同样的`route props`：

```jsx
// 便利的“行内渲染”
<Route path="/home" render={() => <div>Home</div>}/>

// wrapping/composing
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props}/>
    </FadeIn>
  )}/>
)

<FadingRoute path="/cool" component={Something}/>
```

**警告：**`<Route component>`的优先级比 `<Route render>` 高，所以不要在同一个`<Route>`中同时使用两者。

## children: func

有时，无论location是否匹配，你都需要渲染某些组件。在这些情况下，你可以使用`children`prop。它的作用和`render`一样，只不过无论location是否匹配，函数都会执行。

`children`接收的`route props`和`component`以及`render`一样，当route组件没能匹配上URL，`match`是null。看下面这个例子，如果route匹配上了，我们就加一个active的className：

```jsx
<ul>
  <ListItemLink to="/somewhere" />
  <ListItemLink to="/somewhere-else" />
</ul>;

const ListItemLink = ({ to, ...rest }) => (
  <Route
    path={to}
    children={({ match }) => (
      <li className={match ? "active" : ""}>
        <Link to={to} {...rest} />
      </li>
    )}
  />
);
```

也可应用到动画上：

```jsx
<Route children={({ match, ...rest }) => (
  {/* Animate总会渲染，然后你就可以使用生命周期函数来对它的子组件做进/出的动画了。*/}
  <Animate>
    {match && <Something {...rest}/>}
  </Animate>
)}/>
```

**警告：** `<Route component>` 和`<Route render>` 优先级都要高于 `<Route children>` ，所以在同一个 `<Route>`中不要混用。

## path: string

任何 [`path-to-regexp@^1.7.0`](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0)理解的URL路径都行：

```jsx
<Route path="/users/:id" component={User} />
```

没有`path`的`<Route>`总是匹配。

## exact: bool

如果为`true`，那么`path`要精确匹配`location.pathname`：

```jsx
<Route exact path="/one" component={About} />
```

| path   | location.pathname | exact   | matches? |
| ------ | ----------------- | ------- | -------- |
| `/one` | `/one/two`        | `true`  | no       |
| `/one` | `/one/two`        | `false` | yes      |

## strict: bool

如果为`true`，末尾有斜杠的`path`只会匹配末尾有斜杠的`location.pathname`，不同于`exact`，如果`location.pathname`中有其它的URL片段，不会影响匹配：

```jsx
<Route strict path="/one/" component={About} />
```

| path    | location.pathname | matches? |
| ------- | ----------------- | -------- |
| `/one/` | `/one`            | no       |
| `/one/` | `/one/`           | yes      |
| `/one/` | `/one/two`        | yes      |

**警告：**`strict`可被用来限制`location.pathname`没有末尾斜杠，但是要做到这一点，必须拿`strict`和`exact`搭配。

```jsx
<Route exact strict path="/one" component={About} />
```

| path   | location.pathname | matches? |
| ------ | ----------------- | -------- |
| `/one` | `/one`            | yes      |
| `/one` | `/one/`           | no       |
| `/one` | `/one/two`        | no       |

## location: object

 `<Route>` 会尝试用`path`匹配当前location（通常是当前的URL）。然而，也可以传入一个带有不同`pathname`的`location`，代替`path`进行匹配。

如果你想用 `<Route>` 匹配一个不是当前URL的location，这很有用。查看例子 [Animated Transitions](https://reacttraining.com/react-router/web/example/animated-transitions) 。

如果一个 `<Route>` 元素被包裹在 `<Switch>` 内，并且匹配到了传入 `<Switch>` 的location（或者当前历史记录堆栈中的location），那么传递给 `<Route>` 的`location`prop就会被 `<Switch>` 所用的location覆盖掉。

## sensitive: bool

如果为`true`，匹配时会区分大小写：

```jsx
<Route sensitive path="/one" component={About} />
```

| path   | location.pathname | sensitive | matches? |
| ------ | ----------------- | --------- | -------- |
| `/one` | `/one`            | `true`    | yes      |
| `/One` | `/one`            | `true`    | no       |
| `/One` | `/one`            | `false`   | yes      |