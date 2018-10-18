# NavLink

特殊版本的`<Link>`，可以在匹配当前URL时添加style：

```jsx
import { NavLink } from 'react-router-dom'

<NavLink to="/about">About</NavLink>
```

## activeClassName: string

当链接激活时的class。默认的class为`active`，会和`className`属性合并在一起。

```jsx
<NavLink to="/faq" activeClassName="selected">
  FAQs
</NavLink>
```

## activeStyle: object

当链接激活时的style。

```jsx
<NavLink
  to="/faq"
  activeStyle={{
    fontWeight: "bold",
    color: "red"
  }}
>
  FAQs
</NavLink>
```

## exact: bool

如果为`true`，激活时的class或者style只有在location严格匹配时有效：

```jsx
<NavLink exact to="/profile">
  Profile
</NavLink>
```

## strict: bool

如果为`true`，location的`pathname`中的尾部斜杠会有影响，查看本系列`<route>`部分的文档查看更多信息。

```jsx
<NavLink strict to="/events/">
  Events
</NavLink>
```

## isActive: func

为决定链接是否active添加额外的判断逻辑。当你想判断链接的`pathname`是否匹配URL的`pathname`时会用到。

```jsx
// 为是否激活添加额外限定条件：
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink to="/events/123" isActive={oddEvent} >Event 123</NavLink>
```

### location: object

上述`isActive`函数的参数。`isActive`函数比较`<NavLink>`的`to`和当前location（通常是浏览器URL）。如果要比较其他的location，就传一个`location`。