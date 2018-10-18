# MemoryRouter

一个能够把你的“URL”的历史记录保存在内存中的路由器组件，并不会读写地址栏。在测试和非浏览器环境下有用，例如React Native。

```jsx
import { MemoryRouter } from 'react-router'

<MemoryRouter>
  <App/>
</MemoryRouter>
```

## initialEntries: array

在历史记录堆栈中，由location组成的数组。可以是对象`{ pathname, search, hash, state }`，也可以是单纯的URL字符串：

```jsx
<MemoryRouter
  initialEntries={["/one", "/two", { pathname: "/three" }]}
  initialIndex={1}
>
  <App />
</MemoryRouter>
```

## initialIndex: number

下标数字，指示初始的location从 `initialEntries`数组中的哪一项开始。

## getUserConfirmation: func

用来确认跳转的函数。如果直接使用 `<MemoryRouter>` 和 `<Prompt>`，就必须启用此项。

## keyLength: number

`location.key`的长度，默认值为6：

```jsx
<MemoryRouter keyLength={12} />
```

## children: node

要渲染的单一子元素。