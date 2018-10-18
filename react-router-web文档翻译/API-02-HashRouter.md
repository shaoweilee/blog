# HashRouter

这个路由器组件使用URL的hash部分（例如`window.location.hash`）来使UI和URL保持同步。

**重要提示：**hash history不支持`location.key`和`location.state`。在之前的版本，我们试图为这种行为提供垫片，但是出现了我们解决不了的问题。因为这种技术只是为了兼容老旧浏览器，所以我们鼓励你使用 `<BrowserHistory>` 。

## basename: string

所有URL的基础，斜杠开头，末尾无斜杠：

```jsx
<HashRouter basename="/calendar"/>
<Link to="/today"/> // 最终渲染出 <a href="#/calendar/today">
```

## getUserConfirmation: func

跳转前确认的函数，默认使用window.confirm：

```jsx
// 默认行为：
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message)
  callback(allowTransition)
}

<HashRouter getUserConfirmation={getConfirmation}/>
```

## hashType: string

`window.location.hash`使用的编码方式，可选值为：

- `"slash"` -  `#/` and `#/sunshine/lollipops`
- `"noslash"` -  `#` and `#sunshine/lollipops`
- `"hashbang"` - 创造 [“ajax crawlable”](https://developers.google.com/webmasters/ajax-crawling/docs/learn-more) (deprecated by Google) hashes，像这样 `#!/` and `#!/sunshine/lollipops`

默认值为“hash”。

## children: node

要渲染的单一子元素。