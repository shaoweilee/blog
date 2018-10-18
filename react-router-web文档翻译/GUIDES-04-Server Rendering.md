# Server Rendering

服务端渲染稍微有点不同，因为它全是无状态的。我们应当把app包裹在一个无状态的路由器组件`<StaticRouter>`中，而不是`<BrowserRouter>`。我们把请求的url以及一个context属性传入，然后路由组件就可以进行匹配了。

```jsx
// 客户端
<BrowserRouter>
  <App/>
</BrowserRouter>

// 服务器 (不完整的示例)
<StaticRouter location={req.url} context={context} >
  <App/>
</StaticRouter>
```

当你在客户端渲染了一个`<Redirect>`组件，浏览器的历史状态会改变，我们也可以跳转到新的页面。但是在服务器环境下，我们不能改变app的状态。取而代之的是，我们使用`context`属性来弄清楚渲染的结果是什么。如果有`context.url`，我们就知道，客户端重定向了，这允许我们从服务器发送一个恰当的重定向。

```jsx
const context = {};
const markup = ReactDOMServer.renderToString(
  <StaticRouter location={req.url} context={context}>
    <App />
  </StaticRouter>
);

if (context.url) {
  // `<Redirect>`组件被渲染了！
  redirect(301, context.url);
} else {
  // 虚惊一场，发送响应！
}
```

## 添加app指定的context信息

路由器组件只添加`context.url`。但是你可能想发送不同的重定向，或者在某些UI渲染时想发送404响应。你可以任意改变context属性。下面是一种辨别301和302重定向的方式：

```jsx
// app中
const App = () => (
  <Switch>
    {/* 省略其他的 route */}
    <RedirectWithStatus status={301} from="/users" to="/profiles" />
    <RedirectWithStatus status={302} from="/courses" to="/dashboard" />
  </Switch>
);
const RedirectWithStatus = ({ from, to, status }) => (
  <Route
    render={
      ({ staticContext }) => {
        // 客户端没有“staticContext”, 所以要预防这种情况：
        if (staticContext) staticContext.status = status;
        return <Redirect from={from} to={to} />;
    	}
    }
  />
);

// 服务端
const context = {};
const markup = ReactDOMServer.renderToString(
  <StaticRouter context={context}>
    <App />
  </StaticRouter>
);

if (context.url) {
  // 服务端可以使用我们在RedirectWithStatus组件中添加的 `context.status`。
  redirect(context.status, context.url);
}
```

## 404，401，或者其他状态码

可以仿效上面的做法。创建一个添加过context的的组件，然后在app中渲染它。

```jsx
const Status = ({ code, children }) => (
  <Route
    render={({ staticContext }) => {
      if (staticContext) staticContext.status = code;
      return children;
    }}
  />
);
const NotFound = () => (
  <Status code={404}>
    <div>
      <h1>Sorry, can’t find that.</h1>
    </div>
  </Status>
)
// somewhere else
<Switch>
  <Route path="/about" component={About}/>
  <Route path="/dashboard" component={Dashboard}/>
  <Route component={NotFound}/>
</Switch>
```

## 相对完整的例子

这不是一个真实的app，但是它阐释了一个“通用做法”：

```jsx
//nodejs服务端
import { createServer } from "http";
import React from "react";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router";
import App from "./App";

createServer((req, res) => {
  const context = {};

  const html = ReactDOMServer.renderToString(
    <StaticRouter location={req.url} context={context}>
      <App />
    </StaticRouter>
  );

  if (context.url) {
    res.writeHead(301, {
      Location: context.url
    });
    res.end();
  } else {
    res.write(`
      <!doctype html>
      <div id="app">${html}</div>
    `);
    res.end();
  }
}).listen(3000);

//客户端
import ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById("app")
);
```



## 加载数据

有很多方式来加载数据，但尚且没有明晰的最佳实践。所以我们在所有方法中寻找组合，并且不倾向于任何一种方法。我们自信于可以满足你的app所要求的任何条件。

有时你也许想在render之前加载数据，React Router对外暴露了它自己内部使用的`matchPath`静态方法，这个静态方法可以通过匹配location来做路由。你可以在服务器使用这个方法，在渲染前确定数据的依赖项是什么……

此方法的要点依赖于静态路由配置，该配置用于渲染路由和在渲染之前进行匹配以确定数据依赖性。

```js
//首先配置静态路由
const routes = [
  {
    path: "/",
    component: Root,
    loadData: () => getSomeData()
  }
  // etc.
];
```

```jsx
//然后使用上面的静态路由配置，在app中渲染路由
import { routes } from "./routes";
const App = () => (
  <Switch>
    {routes.map(route => (
      <Route {...route} />
    ))}
  </Switch>
);
```

```js
//然后在服务器端，这么干：
import { matchPath } from "react-router-dom";

// 在一个请求中
const promises = [];
// 使用数组的`some` 来仿效 `<Switch>` 组件的表现——只选择第一个匹配项
routes.some(route => {
  // 这里使用`matchPath`
  const match = matchPath(req.path, route);
  if (match) promises.push(route.loadData(match));
  return match;
});

Promise.all(promises).then(data => {
  //向客户端发送数据
});
```

最终，客户端拿到了数据。我们并非为你的app挑选加载数据的模式，而是指出需要实现的要点。

我们的 [React Router Config](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config)包可以帮助你通过静态路由配置来加载数据和服务端渲染。