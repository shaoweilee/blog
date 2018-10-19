# StaticRouter

一个不会改变location的路由器（Router）组件。

在服务端渲染（server-side rendering）脚本中可以用到。在服务端代码中，用户并不是真正的在点击跳转，所以location是不会发生真实的改变，因此就有了这么个名字：静态（static）。而且可以用在简单的测试中。

下面是一个简单的例子，nodejs服务端针对`<Redirect>`发送302状态码，对其它请求发送正常的HTML：

```jsx
import { createServer } from "http";
import React from "react";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router";

createServer((req, res) => {
  //context对象包含了渲染的结果
  const context = {};

  const html = ReactDOMServer.renderToString(
    <StaticRouter location={req.url} context={context}>
      <App />
    </StaticRouter>
  );
	//如果（前端）用了<Redirect>组件，那么context.url会包含要重定向的目标URL
  if (context.url) {
    res.writeHead(302, {
      Location: context.url
    });
    res.end();
  } else {
    res.write(html);
    res.end();
  }
}).listen(3000);
```

## basename: string

所有location的基础URL。正经的basename应当以斜杠“/”开头，但是尾部没有斜杠：

```jsx
<StaticRouter basename="/calendar">
  <Link to="/today" /> // renders <a href="/calendar/today">
</StaticRouter>
```

## location: string

服务端收到的URL，在node服务端，很可能是`req.url`:

```jsx
<StaticRouter location={req.url}>
  <App />
</StaticRouter>
```

## location: object

形如 `{ pathname, search, hash, state }`的一个location对象：

```jsx
<StaticRouter location={{ pathname: "/bubblegum" }}>
  <App />
</StaticRouter>
```

## context: object

一个原生JavaScript对象。在渲染过程中，组件可以给这个对象添加属性，以保存与渲染相关的信息：

```jsx
const context = {}
<StaticRouter context={context}>
  <App />
</StaticRouter>
```

（在客户端）当一个`<Route>`匹配上了，它会把context对象作为`staticContext`props传递给它所渲染的组件，查看本系列GUIDES-04-Server Rendering获得具体用法。

在渲染后，这些属性可被用来定义服务端响应：

```js
if (context.status === "404") {
  // ...
}
```

## children: node

要渲染的单一子组件。