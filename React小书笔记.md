# React小书笔记

一个组件类必须要实现一个 `render` 方法，这个 `render` 方法必须要返回一个 JSX 元素。

但这里要注意的是，必须要用一个外层的 JSX 元素把所有内容包裹起来。返回并列多个 JSX 元素是不合法的。



我们在组建类中用render方法返回的jsx元素，经过编译，转化成这样的结构，一个JavaScript对象描述：

```jsx
//编译前
class Header extends Component {
  render () {
    return (
      <div>
        <h1 className='title'>React 小书</h1>
      </div>
    )
  }
}

ReactDOM.render(
  <Header />,
  document.getElementById('root')
)

//编译后
class Header extends Component {
  render () {
    return (
     React.createElement(
        "div",
        null,
        React.createElement(
          "h1",
          { className: 'title' },
          "React 小书"
        )
      )
    )
  }
}

ReactDOM.render(
  React.createElement(Header, null), 
  document.getElementById('root')
);
```

一个HTML元素只包含三个内容：标签名，特性，子元素。上面的结构很清晰地描述了这三点。ReactDOM.render()方法拿到这个结构对象以后，就可以构造真正的DOM元素，然后插入root下。



## 事件

事件需要bind(this)或者用箭头函数包一下。



## State

修改state可以传对象，也可以传函数。

## props

默认配置props，写成静态属性的样子（推荐）：

```JavaScript
class LikeButton extends Component {
  static defaultProps = {
    like: '取消',
    unlike: '点赞'
  }
}
```

或者利用ES6的特性：

```JavaScript
class LikeButton extends Component {
    getProps({like='zan', unlike='quxiaozan'}={like:'👍', unlike: '👎'}){
        return {like, unlike}
    }
}
```

后面的默认值是应对组件内部调用getProps时忘记传this.props的情况，前面的默认值是应对组件调用者忘记传props的情况。

props传入一个子组件内部，该子组件是不能对传入的props进行更改的。

子组件必须在constructor里传入任意参数，才能在constructor中使用

## 列表

不要忘记key。熟练运用解构赋值。

## 生命周期函数

执行顺序：

**React.js 将组件渲染，并且构造 DOM 元素然后塞入页面的过程称为组件的挂载**

```JavaScript
-> constructor()
-> componentWillMount()//进行组件的启动工作，例如 Ajax 数据拉取、定时器的启动；
-> render()
// 然后构造 DOM 元素插入页面
-> componentDidMount()
// ...
// 即将从页面中删除
-> componentWillUnmount()//组件从页面上销毁的时候，有时候需要一些数据的清理，例如定时器的清理，
// 从页面中删除
```

更新阶段组件生命周期：

1. `shouldComponentUpdate(nextProps, nextState)`：你可以通过这个方法控制组件是否重新渲染。如果返回 `false` 组件就不会重新渲染。这个生命周期在  React.js 性能优化上非常有用。
2. `componentWillReceiveProps(nextProps)`：组件从父组件接收到新的 `props` 之前调用。
3. `componentWillUpdate()`：组件开始重新渲染之前调用。
4. `componentDidUpdate()`：组件重新渲染并且把更改变更到真实的 DOM 以后调用。

## ref

获取ref:

```javascript
<div ref={div=>{this.divDom=div}></div>
```

## props.children

太有用了！

http://huziketang.mangojuice.top/books/react/lesson22

