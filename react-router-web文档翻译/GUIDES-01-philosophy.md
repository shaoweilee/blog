# philosophy

这部分的目的是，解释使用React Router时应有的构思模型。我们称之为“动态路由”——与你可能更熟悉的“静态路由”不同。

## 静态路由

如果你曾用过Rails，Express，Ember，或者Angular，那你肯定就用过静态路由。在上述框架中，你在app的渲染工作发生前，就把“声明路由”作为了app初始化的一部分。React Router pre-v4大部分也是静态的。我们先看看在express（nodejs框架）中是如何定义路由的：

```javascript
app.get("/", handleIndex);
app.get("/invoices", handleInvoices);
app.get("/invoices/:id", handleInvoice);
app.get("/invoices/:id/edit", handleInvoiceEdit);

app.listen();
```

注意，在`app.listen()`之前（可以先把它理解为服务器启动，服务器开始监听路由了），路由就被定义了。客户端路由嘞，不同的框架之间大概是相同的：

在Angular中，你先定义路由，然后在页面渲染之前，把路由引入顶级模块AppModule中：

```
const appRoutes: Routes = [
  {
    path: "crisis-center",
    component: CrisisListComponent
  },
  {
    path: "hero/:id",
    component: HeroDetailComponent
  },
  {
    path: "heroes",
    component: HeroListComponent,
    data: { title: "Heroes List" }
  },
  {
    path: "",
    redirectTo: "/heroes",
    pathMatch: "full"
  },
  {
    path: "**",
    component: PageNotFoundComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(appRoutes)]
})
export class AppModule {}
```

Ember有一个传统的routes.js文件，同样的，这发生在app渲染工作开始前：

```
Router.map(function() {
  this.route("about");
  this.route("contact");
  this.route("rentals", function() {
    this.route("show", { path: "/:rental_id" });
  });
});

export default Router;
```

虽然API不同，但他们都遵循“静态路由”的模型。在v4之前，React Router也是这样做的。

要想迅速学会React Router，你需要把上面的全忘了！:O

## 幕后故事

坦率滴来说，在v2版本的大方向选取上，我们（Michael and Ryan）很丧，因为感觉被API限制了，而且意识到重复写了部分的React，像生命周期函数等等，并不符合React给出的“组合UI”的信仰。

## 动态路由

我们说的“动态路由”，其实指的是，当你的app渲染时发生的“路由”，而不是在一个正在运行的app之外有一个配置文件或者惯例。这意味着在React Router中，一切皆组件。先来个60秒回顾API的环节，看看它是怎么工作的：

首先，为你当前的环境选择一个Router组件，然后把它渲染到app顶层：

```
// react-native环境下用这个
import { NativeRouter } from "react-router-native";

// react-dom 环境下用这个
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  el
);
```

其次，用`Link`组件来链接到一个新的地址，就像`<a>`标签那样：

```
const App = () => (
  <div>
    <nav>
      <Link to="/dashboard">Dashboard</Link>
    </nav>
  </div>
);
```

最后，当用户访问`/dashboard`（主控面板，整合显示重要信息）时，渲染一个`Route`组件来展示对应的UI。

```
const App = () => (
  <div>
    <nav>
      <Link to="/dashboard">Dashboard</Link>
    </nav>
    <div>
      <Route path="/dashboard" component={Dashboard} />
    </div>
  </div>
);
```

`Route`组件会渲染`<Dashboard {...props}/>`组件。`props`是一些路由指定的东西，如`{match, location, history}`。如果用户没有访问`/dashboard`，`Route`啥也不会渲染。

## 嵌套路由

很多router都有“嵌套路由”的概念，如果你在v4之前就用过React Router的某些版本，你应该知道它也能这么做！当你从静态路由配置转到动态路由阵营，你该怎么“嵌套路由”？好吧，怎么嵌套一个`<div>`？

```
const App = () => (
  <BrowserRouter>
    {/* 这是个 div */}
    <div>
      {/* 这是个 Route，路由组件 */}
      <Route path="/tacos" component={Tacos} />
    </div>
  </BrowserRouter>
);

// 当路由匹配到了 `/tacos` ，下面这个组件就会被渲染
const Tacos = ({ match }) => (//这里用到了ES6的解构赋值，其实就是props.match
  // 这是一个嵌套的div
  <div>
    {/* 这是一个嵌套的 Route, match.url 能帮我们造一个相对路径 */}
    <Route path={match.url + "/carnitas"} component={Carnitas} />
  </div>
);
```

看见了不，router没有“嵌套”API，`Route`只是一个组件，就像`div`。

接下来，看个更复杂的例子。

## 响应式路由

设想一下，用户导航到了`/invoices`（清单），而且你的app能够适配不同的屏幕大小——如果用户的视口窄，你可以只给他们展示一个列表和一个导航至dashboard的链接：

```asciidoc
小屏幕
url：/invoices

+----------------------+
|                      |
|      Dashboard       |
|                      |
+----------------------+
|                      |
|      Invoice 01      |
|                      |
+----------------------+
|                      |
|      Invoice 02      |
|                      |
+----------------------+
|                      |
|      Invoice 03      |
|                      |
+----------------------+
|                      |
|      Invoice 04      |
|                      |
+----------------------+
```

在大点的屏幕上，可以左边展示导航，右边展示主详情：

```
大屏幕
url：/invoices/dashboard（注意这里的地址）
+----------------------+---------------------------+
|                      |                           |
|      Dashboard       |                           |
|                      |   Unpaid:             5   |
+----------------------+                           |
|                      |   Balance:   $53,543.00   |
|      Invoice 01      |                           |
|                      |   Past Due:           2   |
+----------------------+                           |
|                      |                           |
|      Invoice 02      |                           |
|                      |   +-------------------+   |
+----------------------+   |                   |   |
|                      |   |  +    +     +     |   |
|      Invoice 03      |   |  | +  |     |     |   |
|                      |   |  | |  |  +  |  +  |   |
+----------------------+   |  | |  |  |  |  |  |   |
|                      |   +--+-+--+--+--+--+--+   |
|      Invoice 04      |                           |
|                      |                           |
+----------------------+---------------------------+
```

现在，暂停一下，考虑一下针对两种屏幕大小的URL：`/invoices`。对大屏幕来说，这是一个恰当的路由吗？我们该在右边放些什么？

```
大屏幕
url: /invoices（回到了invoices）
+----------------------+---------------------------+
|                      |                           |
|      Dashboard       |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 01      |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 02      |             ???           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 03      |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 04      |                           |
|                      |                           |
+----------------------+---------------------------+
```

对大屏幕来说，`/invoices`不是一个恰当的路由，但是对于小屏幕是恰当的。想象一下有人拿着一个巨大的手机，他们一开始盯着`/invoices`页面看呢，然后横屏了，既然横屏了，那么我们就应该有足够的空间来展示主详情UI了！所以应当立即重定向（到上例中的`/invoices/dashboard`）。

早些的React Router版本的静态路由并没有组合式的解决方案。当使用动态路由时，你可以显式地组合出这种功能。如果你开始把路由想象成UI，而不是静态配置，就能很自然地想出下面的代码：

```
const App = () => (
  <AppLayout>
    <Route path="/invoices" component={Invoices} />
  </AppLayout>
);

const Invoices = () => (
  <Layout>
    {/* 总是展示 nav */}
    <InvoicesNav />

    <Media query={PRETTY_SMALL}>
      {screenIsSmall =>
        screenIsSmall ? (
          // 小屏幕没有重定向
          <Switch>
            <Route exact path="/invoices/dashboard" component={Dashboard} />
            <Route path="/invoices/:id" component={Invoice} />
          </Switch>
        ) : (
          // 大屏有重定向
          <Switch>
            <Route exact path="/invoices/dashboard" component={Dashboard} />
            <Route path="/invoices/:id" component={Invoice} />
            <Redirect from="/invoices" to="/invoices/dashboard" />
          </Switch>
        )
      }
    </Media>
  </Layout>
);

```

当用户把手机从竖屏改为横屏模式，上面的代码会自动把页面重定向至dashboard——路由根据设备的动态属性而改变。

这只是一个小栗子。总结一下：使用React Router时，想象组件，而不是静态路由。考虑如何用React的声明（组件）组合，因为几乎每个“React Router问题”都是“React问题”。