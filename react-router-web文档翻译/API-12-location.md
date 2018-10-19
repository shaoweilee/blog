# location

location，代表目前app过去、现在、未来所处的位置，看起来像这样的一个对象：

```js
{
  key: 'ac3df4', // HashHistory不可用！
  pathname: '/somewhere'
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
```

某些场景下，路由器也会提供location对象：

- [Route component](https://reacttraining.com/web/api/Route/component) as `this.props.location`
- [Route render](https://reacttraining.com/web/api/Route/render-func) as `({ location }) => ()`
- [Route children](https://reacttraining.com/web/api/Route/children-func) as `({ location }) => ()`
- [withRouter](https://reacttraining.com/web/api/withRouter) as `this.props.location`

在`history.location`中也会找到它，但是不应该使用`history.location`，因为它是可变的。

location对象是不变的，所以你可以在生命周期函数中使用它，来确定跳转的时机，对于获取数据和动画来说，很有用。

```js
componentWillReceiveProps(nextProps) {
  if (nextProps.location !== this.props.location) {
    // navigated!
  }
}
```

在以下这些情况下，你可以用location对象代替字符串：

- Web [Link to](https://reacttraining.com/web/api/Link/to)
- Native [Link to](https://reacttraining.com/native/api/Link/to)
- [Redirect to](https://reacttraining.com/web/api/Redirect/to)
- [history.push](https://reacttraining.com/web/api/history/push)
- [history.replace](https://reacttraining.com/web/api/history/push)

普通情况下，你只会用到字符串，但是如果你需要添加一些当app回到某个location可用的“location state”时，就可以用location对象了。如果你想依据导航历史而不仅仅是path来确定UI时，就会用到这一点：

```jsx
// 一般情况用这个就行了
<Link to="/somewhere"/>

// 但是也可以用对象
const location = {
  pathname: '/somewhere',
  state: { fromDashboard: true }
}

<Link to={location}/>
<Redirect to={location}/>
history.push(location)
history.replace(location)
```

最后，你也可以把location传给下列组件：

- Route
- Switch

这会阻止上面两个组件利用真实的location做路由，在做动画和推迟跳转时会用到，或者任何时候，你都可以用“虚假location”，让组件以为自己在匹配的路由下，从而渲染这个组件。