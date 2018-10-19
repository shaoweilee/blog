# Switch

渲染第一个匹配到location`<Route>`或`<Redirect>`组件。

这和只用一堆`<Route>`有什么区别呢？

`<Switch>` 独特之处在于它的渲染具有唯一性。对照来说，像下面的代码，每个匹配到location的 `<Route>`都会渲染：

```jsx
<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```

假如，URL是“/about”，那么 `<About>`， `<User>`， 和 `<NoMatch>`都会渲染（不带`path`的`<Route>`匹配所有URL），因为它们都匹配了path。这样设计的原因是，我们想让`<Route>`组件能和app通过多种方式组合起来，像侧边栏、面包屑导航、bootstrap的选项卡之类的。

可有时候，我们只想挑一个 `<Route>` 来渲染。如果我们目前在“/about”，但不想匹配“/:user”，也不想展示“404”页面，那就需要用`<Switch>`改进一下了：

```jsx
import { Switch, Route } from 'react-router'

<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  <Route component={NoMatch}/>
</Switch>
```

现在，如果我们在“/about”，那么 `<Switch>` 就会查找一个能匹配的 `<Route>`，发现`<Route path="/about" />` 匹配到了， `<Switch>` 就停止查找，接着渲染 `<About>`组件。类似地，如果我们在 “/michael”下，那么就会渲染 `<User>` 组件。

也可用在动画过渡上，因为匹配的 `<Route>` 会和之前的 `<Route>` 在同一位置渲染：

```jsx
//good
<Fade>
  <Switch>
    {/* 这里只会有一个子组件 */}
    <Route/>
    <Route/>
  </Switch>
</Fade>


//bad
<Fade>
  <Route/>
  <Route/>
  {/* 这里会一直有两个组件，即使其中一个可能只渲染了个null，这种写法会拖慢过渡的效率 */}
</Fade>
```

## location: object

一个用来代替现存history location（通常是浏览器URL）的对象，一旦给`<Switch>`传入了这个对象，那么其下的`<Route>`就会用这个对象中的`pathname`进行匹配。并且这个对象也会被传给匹配的组件。

## children: node 

 `<Switch>`组件的子组件应当是 `<Route>` 或`<Redirect>` ，只有第一个匹配当前location的才会被渲染。

`<Route>` 使用它的`path`prop来做匹配， `<Redirect>` 使用`from`prop来做匹配。一个没有`path`的 `<Route>` 或者一个没有 `from` 的 `<Redirect>` 总是与当前location匹配。

如果 `<Redirect>` 在 `<Switch>`下，那么 `<Redirect>` 就能够使用 `<Route>`的那些prop：`path`， `exact`和 `strict`。 `from` 只是`path`的别称。

如果给 `<Switch>`一个`location`属性，那么这个location就会覆盖掉匹配的子组件的location。

```jsx
<Switch>
  <Route exact path="/" component={Home} />

  <Route path="/users" component={Users} />
  <Redirect from="/accounts" to="/users" />

  <Route component={NoMatch} />
</Switch>
```

