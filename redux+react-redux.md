redux库提供了createStore函数。createStore函数接收reducer函数，reducer函数用来初始化数据，或者根据不同的action.type，返回不同的数据。

```JavaScript
function createStore (reducer) {
  let state = null
  const listeners = []
  const subscribe = (listener) => listeners.push(listener)
  const getState = () => state
  const dispatch = (action) => {
    state = reducer(state, action)
    listeners.forEach((listener) => listener())
  }
  dispatch({}) // 初始化 state
  return { getState, dispatch, subscribe }
}
```

react-redux库提供了Provider组件和connect函数，因为他们内部包含了对context的存取操作。

```javascript
//Provider
export class Provider extends Component {
  static propTypes = {
    store: PropTypes.object,
    children: PropTypes.any
  }
  static childContextTypes = {
    store: PropTypes.object
  }
  getChildContext () {
    return {
      store: this.props.store
    }
  }
  render () {
    return (
      <div>{this.props.children}</div>
    )
  }
}
```

在一个smart组件中，编写两个函数，指明要求的数据和dispatch操作：

```JavaScript
const mapStateToProps = (state) => {
  const data = {comments: state.comments};
  return data;
}
```

```JavaScript
const mapDispatchToProps = (dispatch) => {
  const func = {
    onSubmit(comment){
      dispatch(addComment(comment));
    }
  };
  return func;
}
```



connect函数接受这两个函数作为参数，这两个函数在connect函数内部的Connect组件内部被调用，返回的是对象，Connect组件将这个对象作为props传递给被包裹的组件。

```javascript
export const connect = (mapStateToProps, mapDispatchToProps) => (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }
    constructor () {
      super()
      this.state = {
        allProps: {}
      }
    }
    componentWillMount () {
      const { store } = this.context
      this._updateProps()
      store.subscribe(() => this._updateProps())
    }
    _updateProps () {
      const { store } = this.context
      let stateProps = mapStateToProps
        ? mapStateToProps(store.getState(), this.props)
        : {} // 防止 mapStateToProps 没有传入
      let dispatchProps = mapDispatchToProps
        ? mapDispatchToProps(store.dispatch, this.props)
        : {} // 防止 mapDispatchToProps 没有传入
      this.setState({
        allProps: {
          ...stateProps,
          ...dispatchProps,
          ...this.props
        }
      })
    }
    render () {
      return <WrappedComponent {...this.state.allProps} />
    }
  }
  return Connect
}
```

