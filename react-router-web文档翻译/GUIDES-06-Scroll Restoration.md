# Scroll Restoration

在React Router的早些版本中，我们提供了对“滚动条位置恢复”的开箱即用的支持，这篇文档帮你get到要点。

浏览器通过`history.pushState`来插手滚动恢复，它在chrome中得到了很好的支持，[这是指导文献](https://majido.github.io/scroll-restoration-proposal/history-based-api.html#web-idl)。

## 滚动至顶

大多数情况下，如果你页面内容很长，你需要的是“滚动至顶”的功能。可以像下面一样写一个`<ScrollToTop>`组件，在每次导航时都可以滚动至顶，确保它被包在`withRouter` 中，让它可以访问到路由组件的props：

```jsx
class ScrollToTop extends Component {
  componentDidUpdate(prevProps) {
    if (this.props.location !== prevProps.location) {
      window.scrollTo(0, 0);
    }
  }

  render() {
    return this.props.children;
  }
}

export default withRouter(ScrollToTop);
```

然后在app顶级渲染，但是要在路由器组件之中：

```jsx
const App = () => (
  <Router>
    <ScrollToTop>
      <App/>
    </ScrollToTop>
  </Router>
)

// 或者在哪儿渲染都行，但是只渲染一次哦
<ScrollToTop/>
```

如果你有一个链接到路由器组件的选项卡接口，那么用户并不希望在每次切换选项卡的时候都滚动至顶。像下面这样写一个`<ScrollToTopOnMount>`就可以解决：

```jsx
class ScrollToTopOnMount extends Component {
  componentDidMount() {
    window.scrollTo(0, 0);
  }
  render() {
    return null;
  }
}

class LongContent extends Component {
  render() {
    <div>
      <ScrollToTopOnMount />
      <h1>长内容页面</h1>
    </div>;
  }
}

// 整个app中某处的代码指定了路由
<Route path="/long-content" component={LongContent} />;
```

## 普遍适用的解决方式

这里，有种普遍的解决方式，也是浏览器正在原生实现的，有两种情况：

1. 导航时（如点击`<a>`标签），触发滚动。（假设看一篇文章滚动到了页面底部，第二次打开时，并不希望从底部开始）
2. 在前进或者后退页面时，恢复滚动位置。

我们想提供一种类似这样的解决方式：

```jsx
<Router>
  <ScrollRestoration>
    <div>
      <h1>App</h1>

      <RestoredScroll id="bunny">
        <div style={{ height: "200px", overflow: "auto" }}>I will overflow</div>
      </RestoredScroll>
    </div>
  </ScrollRestoration>
</Router>
```

首先，`ScrollRestoration` 会在导航时滚动页面；其次，它使用`location.key`保存页面滚动位置和`RestoredScroll` 组件的滚动位置，再把位置信息存到`sessionStorage`中。然后，当`ScrollRestoration` 或者`RestoredScroll` 组件加载时，组件在`sessionsStorage`中查找滚动条位置信息。

下面几段的意思大概是浏览器已经开始着手这方面的工作了，我们React很忙，没时间，不做了。但是你做的话我们会帮你！