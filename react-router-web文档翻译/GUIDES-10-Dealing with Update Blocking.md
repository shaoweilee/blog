# Dealing with Update Blocking

React Router有很多“location感知”的组件，这些组件能够根据当前location对象来决定渲染啥。默认情况下，当前的location对象被隐式地传递给使用React context模型的组件。当location变化时，那些组件可以从context中取到新的location对象来重新渲染。

React提供两种方法来优化渲染性能：`shouldComponentUpdate` 生命周期函数和`PureComponent`。两种方式都可以阻止组件重新渲染，除非满足重新渲染的条件。不幸的是，阻止重新渲染意味着React Router的“location感知”组件与当前location失去同步。

下面是一个出现这种问题的例子（使用`PureComponent`）：

```js
class UpdateBlocker extends React.PureComponent {
  render() {
    return (
      <div>
        <NavLink to="/about">About</NavLink>
        <NavLink to="/faq">F.A.Q.</NavLink>
      </div>
    );
  }
}
```

当`<UpdateBlocker>`组件加载时，所有“location感知”组件会使用当前location对象和match对象来渲染：

```jsx
// location = { pathname: '/about' }
<UpdateBlocker />
// <div>
//   <a href='/about' class='active'>About</a>
//   <a href='/faq'>F.A.Q.</a>
// </div>
```

当location变化， `<UpdateBlocker>`组件没有监听到任何的prop或state变化，因此它的子组件不会重新渲染：

```jsx
// location = { pathname: '/faq' }
<UpdateBlocker />
// 下面的a链接不会重新渲染，所以它们保持之前的状态
// <div>
//   <a href='/about' class='active'>About</a>
//   <a href='/faq'>F.A.Q.</a>
// </div>
```

使用`shouldComponentUpdate`情况下：

为了让一个实现了`shouldComponentUpdate` 的组件知道location变化时应该渲染，该组件的`shouldComponentUpdate` 方法应该检测location的变化。如果你实现`shouldComponentUpdate` ，你应当比较当前的location和下一个`context.router`对象。然而，作为一个框架使用者，你不应当直接使用context，理想状态下，你应当比较当前location和下一个location，不碰context。

使用第三方代码情况下：

尽管你没有调用`shouldComponentUpdate` ，在location变化时也可能会遇到组件不更新的情况，最有可能的原因是`shouldComponentUpdate` 被第三方代码调用了，比如 `react-redux`的 `connect` 和`mobx-react`的`observer`。

```js
// react-redux
const MyConnectedComponent = connect(mapStateToProps)(MyComponent);

// mobx-react
const MyObservedComponent = observer(MyComponent);
```

使用第三方代码时，你不大可能控制`shouldComponentUpdate`的实现，因此，你必须强化你的代码对location变化的感知能力。

无论是Redux的`connect`还是mobx的`observer`，它们创建的组件的`shouldComponentUpdate`方法都会对当前props和下一个props作一个浅比较，这些组件只会在prop变化时重新渲染。这意味着要保证组件能够更新，location变化时传入的prop也得变化。

使用`PureComponent`的情况下：

React的`PureComponent`没有实现`shouldComponentUpdata`，但是采取了类似的方法来阻止组件更新。当一个“pure”组件更新时，它会浅比较当前的props、state和下一个props、state。如果没有检测到任何更改，组件就不会更新了。跟处理`shouldComponentUpdata`情况相似，要使“pure”组件能够在location变化时更新，它需要改变prop或state。

## 解决方法

### 快速解决方案

如果你在使用像Redux的`connect`或Mobx的`observer`时遇到了上述问题，你只需要把组件包裹在`withRouter`中就行了。

```javascript
// redux before
const MyConnectedComponent = connect(mapStateToProps)(MyComponent);
// redux after
const MyConnectedComponent = withRouter(connect(mapStateToProps)(MyComponent));

// mobx before
const MyConnectedComponent = observer(MyComponent);
// mobx after
const MyConnectedComponent = withRouter(observer(MyComponent));
```

这不是最高效的解决方式，但它的确解决了问题。阅读[Redux guide](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/redux.md#blocked-updates)来获取更多关于这种解决方式的信息，阅读 [这里](https://github.com/ReactTraining/react-router/pull/5552#issuecomment-331502281)来了解为啥这不是最优解决方式。

### 推荐的解决方式

避免“location变化后重新渲染被阻止”这一问题的关键在于，把location当做prop传给组件，这样location变化时prop也会改变，所以组件就可以检测到当前location和下一个location不同了。

```jsx
// location = { pathname: '/about' }
<UpdateBlocker location={location} />
// <div>
//   <a href='/about' class='active'>About</a>
//   <a href='/faq'>F.A.Q.</a>
// </div>

// location = { pathname: '/faq' }
<UpdateBlocker location={location} />
// <div>
//   <a href='/about'>About</a>
//   <a href='/faq' class='active'>F.A.Q.</a>
// </div>
```

#### 获取location

为了把`location`当做prop传递给component，咱得先获取到location。一个组件得到`location`的主要方式是通过 `<Route>` 组件。当一个 `<Route>` 组件匹配到了location（如果使用`children`就会总是匹配），它把当前location传给它渲染的子组件：

```jsx
<Route path='/here' component={Here}/>
const Here = (props) => {
  // props.location = { pathname: '/here', ... }
  return <div>You are here</div>
}

<Route path='/there' render={(props) => {
  // props.location = { pathname: '/there', ... }
  return <div>You are there</div>
}}/>

<Route path='/everywhere' children={(props) => {
  // props.location = { pathname: '/everywhere', ... }
  return <div>You are everywhere</div>
}}/>
```

这意味着，假如组件重渲染被阻止了，你可以很简单地把location通过下列几种方式传入：

```jsx
// 下面的Blocker 是一个 "pure" 组件， 所以它只会在收到新的props时更新
class Blocker extends React.PureComponent {
  render() {
    return (
      <div>
        <NavLink to="/oz">Oz</NavLink>
        <NavLink to="/kansas">Kansas</NavLink>
      </div>
    );
  }
}
```

1. 如果一个组件直接通过`<Route>`被渲染，那么完全不用担心重渲染被阻止，因为`<Route>`已经把`location`当做prop注入了该组件：

   ```jsx
   // 当location变化时，<Blocker>的location prop就会变化。
   <Route path="/:place" component={Blocker} />
   ```

2. 一个被 `<Route>`直接渲染的组件可以把location prop传递给它创建的任意子组件：

   ```jsx
   <Route path="/parent" component={Parent} />;
   
   const Parent = props => {
     // <Parent> receives the location as a prop. Any child
     // element it creates can be passed the location.
     return (
       <SomeComponent>
         <Blocker location={props.location} />
       </SomeComponent>
     );
   };
   ```

假设有一组件A，如果组件A不是直接被 `<Route>` 渲染，而且在渲染A组件的上层组件作用域内没有`location`，怎么办？你可以采取下面两种方式来自动把location作为prop传入你的组件：

1. 渲染一个没有`path`的`<Route>`。虽然`<Route>`专门被用来匹配特定的path，但一个没有`path`的`<Route>`总会匹配任意path，所以它总会渲染它的组件：

   ```jsx
   // 无path的 <Route> = <Blocker> 将会被渲染
   const MyComponent = () => (
     <SomeComponent>
       <Route component={Blocker} />
     </SomeComponent>
   );
   ```

2. 你可以用高阶组件`withRouter`把你的组件包裹，它会把当前location当做props之一传递给你的组件：

   ```jsx
   // 本质上, withRouter 只是渲染了一个无path的<Route>
   const BlockAvoider = withRouter(Blocker);
   
   const MyComponent = () => (
     <SomeComponent>
       <BlockAvoider />
     </SomeComponent>
   );
   ```

