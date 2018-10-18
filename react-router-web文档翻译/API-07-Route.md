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

当你使用`component`而不是下面要介绍的`render`或`children`时，路由器会从给定的组件中，使用`React.createElement`来创建一个新的React元素。这意味着，如果你为`component`属性提供一个函数，那么在每次渲染时，都会创建一个新的组件。这就导致，现有的组件卸载后，新的组件会被挂载，而不是更新现有的组件。