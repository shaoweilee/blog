# history

在这个系列中，术语“history”和“history对象”指的是 [the `history` package](https://github.com/ReactTraining/history)，是React Router中两个主要依赖的其中之一（除了React本身），这个包为不同环境下JavaScript管理会话历史提供了几种不同的实现。

以下术语也被使用了：

- “browser history” - DOM独有的实现，在支持HTML5 history API的浏览器中可用
- “hash history” - DOM独有的实现，支持老旧的浏览器
- “memory history” - 内存中的history实现，在测试环境和非浏览器环境下（比如React Native）有用。

history对象通常有以下属性和方法：

- `length` - (number) 历史记录堆栈中存储的数量
- `action` - (string) The current action (`PUSH`, `REPLACE`, or `POP`)
- `location` \- (object) The current location. 可有以下属性: 
  - `pathname` - (string) The path of the URL
  - `search` - (string) The URL query string
  - `hash` - (string) The URL hash fragment
  - `state` - (object) 原文是“location-specific state that was provided to e.g. `push(path, state)` when this location was pushed onto the stack. Only available in browser and memory history.”我的理解是这个location所携带的额外信息。例如当这个location对象被`push(path, state)`方法压入历史记录堆栈中时，可以把这个state提供给`push`方法。只在浏览器history和内存history中可用。
- `push(path, [state])` - (function) 把一项新纪录压入历史记录堆栈
- `replace(path, [state])` - (function) 在历史记录堆栈中替换当前记录。
- `go(n)` - (function) Moves the pointer in the history stack by `n` entries
- `goBack()` - (function) 等于 `go(-1)`
- `goForward()` - (function) 等于 `go(1)`
- `block(prompt)` - (function) 阻止跳转/导航 (see [the history docs](https://github.com/ReactTraining/history#blocking-transitions))

## history is mutable

history对象是可变的。因此推荐通过`<Route>`的props访问location，而不是通过history.location。这可以保证在生命周期函数中，你对React的某些猜测是正确的，例如：

```jsx
class Comp extends React.Component {
  componentWillReceiveProps(nextProps) {
    // 正确做法，结果为true。
    const locationChanged = nextProps.location !== this.props.location;

    // 错误的, 由于history易变，结果总是false。
    const locationChanged =
      nextProps.history.location !== this.props.history.location;
  }
}

<Route component={Comp} />;
```

