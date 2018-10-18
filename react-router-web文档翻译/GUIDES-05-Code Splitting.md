# Code Splitting

我们web应用的一个巨大特征就是用户在使用之前没必要下载一整个app。你可以把代码分割想象成“app的渐进式下载”。我们可以通过[webpack](https://webpack.js.org/), [`@babel/plugin-syntax-dynamic-import`](https://babeljs.io/docs/plugins/syntax-dynamic-import/)和 [`react-loadable`](https://github.com/thejameskyle/react-loadable)实现代码分割。

webpack有内置的对 [dynamic imports](https://github.com/tc39/proposal-dynamic-import)（动态引入）的支持；然而，如果你正在使用Babel，那么你需要使用[`@babel/plugin-syntax-dynamic-import`](https://babeljs.io/docs/plugins/syntax-dynamic-import/)插件，它只转换语法，这个插件允许Babel分析动态引入，因此webpack就可以用代码分割的方式来打包。你的`.babelrc`应当是这样的：

```json
{
  "presets": ["@babel/react"],
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```

[`react-loadable`](https://github.com/thejameskyle/react-loadable) 是一个通过动态引入来加载组件的高阶组件。它自动处理各种情况，使得代码分割变的很简单！以下是使用它的例子：

```jsx
import Loadable from "react-loadable";
import Loading from "./Loading";

const LoadableComponent = Loadable({
  loader: () => import("./Dashboard"),
  loading: Loading
});

export default class LoadableDashboard extends React.Component {
  render() {
    return <LoadableComponent />;
  }
}
```

这就完啦！在你的app中，当需要用到`LoadableDashboard` 组件（或者随便起名字）时，它会自动被加载、渲染。`loader` 选项是一个函数，是实际加载的组件；`loading` 是一个加载实际组件时渲染的占位符组件。

## 代码分割和服务端渲染

[`react-loadable`](https://github.com/thejameskyle/react-loadable)包含了一个[服务端渲染的指导](https://github.com/thejameskyle/react-loadable/tree/6902cc87f618446c54daa85d8fecec6836c9461a#server-side-rendering)，你只需要在`.babelrc`中引入[`babel-plugin-import-inspector`](https://github.com/thejameskyle/react-loadable/tree/6902cc87f618446c54daa85d8fecec6836c9461a#babel-plugin-import-inspector)插件：

```json
{
  "presets": ["@babel/react"],
  "plugins": [
    "@babel/plugin-syntax-dynamic-import",
    [
      "import-inspector",
      {
        "serverSideRequirePath": true
      }
    ]
  ]
}
```

