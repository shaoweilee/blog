# BrowserRouter

路由器组件使用HTML5的history API（pushState，replaceState和popState事件）使UI和URL保持同步。

```jsx
import { BrowserRouter } from 'react-router-dom'

<BrowserRouter
  basename={optionalString}
  forceRefresh={optionalBool}
  getUserConfirmation={optionalFunc}
  keyLength={optionalNumber}
>
  <App/>
</BrowserRouter>
```

## basename: string

所有location的基础URL。开头应该有个“/”，末尾没有斜杠：

```jsx
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // 渲染的实际链接是 <a href="/calendar/today">
```

## getUserConfirmation: func

跳转前确认的函数，默认使用window.confirm：

```jsx
// 默认行为：
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message)
  callback(allowTransition)
}
<BrowserRouter getUserConfirmation={getConfirmation}/>
```

## forceRefresh: bool

如果为`true`，那么页面跳转的时候就会刷新整个页面。你可能只需要在不支持HTML5 history API的浏览器中才会用到：

```jsx
const supportsHistory = 'pushState' in window.history
<BrowserRouter forceRefresh={!supportsHistory}/>
```

## keyLength: number

location.key的长度，默认为6：

```jsx
<BrowserRouter keyLength={12} />
```

## children: node

要渲染的单个子元素。