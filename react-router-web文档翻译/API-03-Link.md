# Link

为app提供声明式的、可访问的导航链接：

```jsx
import { Link } from 'react-router-dom'

<Link to="/about">About</Link>
```

## to: 可用为string

代表跳转链接目标的字符串，由location的pathname，search，hash属性组合而成：

```jsx
<Link to="/courses?sort=name" />
```

## to: 也可用object

可有以下任意属性的对象：

- `pathname`: String，表示链接目标；
- `search`: String，query 参数；
- `hash`: 要放在URL中的hash，例如 `#a-hash`；
- `state`: 要保存在 `location`中的state，一个可以自定义的对象；

```jsx
<Link
  to={{
    pathname: "/courses",
    search: "?sort=name",
    hash: "#the-hash",
    state: { fromDashboard: true }
  }}
/>
```

## replace: bool

如果为`true`，那么点击这个链接将从历史记录中替换当前的，而不是添加一个新的历史记录：

```jsx
<Link to="/courses" replace />
```

## innerRef: function

允许访问渲染后的`<a>`元素：

```jsx
const refCallback = node => {
  //`node`指向<Link>渲染后的a元素，在<Link>组件加载后获取。
}
<Link to="/" innerRef={refCallback} />
```

## others

其他`props`，可以放在`<a>`上的，例如 `title`， `id`，`className`等。