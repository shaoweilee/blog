# Testing

（这里翻译的很水……）

React Router依赖于React context来工作，当你使用我们的组件时，这对测试组件有影响。

## Context

如果你尝试对你其中一个使用了`<Link>` 或者 `<Route>`的组件进行单元测试，会报一些关于context的错误。我们推荐你把单元测试包裹在`<StaticRouter>`或者 `<MemoryRouter>`组件中，例如：

```jsx
class Sidebar extends Component {
  // ...
  render() {
    return (
      <div>
        <button onClick={this.toggleExpand}>expand</button>
        <ul>
          {users.map(user => (
            <li>
              <Link to={user.path}>{user.name}</Link>
            </li>
          ))}
        </ul>
      </div>
    );
  }
}

// broken
test("it expands when the button is clicked", () => {
  render(<Sidebar />);
  click(theButton);
  expect(theThingToBeOpen);
});

// fixed!
test("it expands when the button is clicked", () => {
  render(
    <MemoryRouter>
      <Sidebar />
    </MemoryRouter>
  );
  click(theButton);
  expect(theThingToBeOpen);
});
```

## 从特定路由开始

`<MemoryRouter>` 支持`initialEntries` 和`initialIndex` 两个props，这样你就可以从特定的location启动了。

```jsx
test("current user is active in sidebar", () => {
  render(
    <MemoryRouter initialEntries={["/users/2"]}>
      <Sidebar />
    </MemoryRouter>
  );
  expectUserToBeActive(2);
});
```

## 导航

如果你必须测试路由，下面是比较聪明的做法：

```jsx
import { render, unmountComponentAtNode } from "react-dom";
import React from "react";
import { Route, Link, MemoryRouter } from "react-router-dom";
import { Simulate } from "react-addons-test-utils";

// a way to render any part of your app inside a MemoryRouter you pass it a list of // steps to execute when the location changes, it will call back to you with stuff // like `match` and `location`, and `history` so you can control the flow and make // assertions.
const renderTestSequence = ({
  initialEntries,
  initialIndex,
  subject: Subject,
  steps
}) => {
  const div = document.createElement("div");

  class Assert extends React.Component {
    componentDidMount() {
      this.assert();
    }

    componentDidUpdate() {
      this.assert();
    }

    assert() {
      const nextStep = steps.shift();
      if (nextStep) {
        nextStep({ ...this.props, div });
      } else {
        unmountComponentAtNode(div);
      }
    }

    render() {
      return this.props.children;
    }
  }

  class Test extends React.Component {
    render() {
      return (
        <MemoryRouter
          initialIndex={initialIndex}
          initialEntries={initialEntries}
        >
          <Route
            render={props => (
              <Assert {...props}>
                <Subject />
              </Assert>
            )}
          />
        </MemoryRouter>
      );
    }
  }

  render(<Test />, div);
};

// our Subject, the App, but you can test any sub
// section of your app too
const App = () => (
  <div>
    <Route
      exact
      path="/"
      render={() => (
        <div>
          <h1>Welcome</h1>
        </div>
      )}
    />
    <Route
      path="/dashboard"
      render={() => (
        <div>
          <h1>Dashboard</h1>
          <Link to="/" id="click-me">
            Home
          </Link>
        </div>
      )}
    />
  </div>
);

// 真实的测试！
it("navigates around", done => {
  renderTestSequence({
    // 提供正在测试的项目
    subject: App,

    // and the steps to execute each time the location changes
    steps: [
      // initial render
      ({ history, div }) => {
        // assert the screen says what we think it should
        console.assert(div.innerHTML.match(/Welcome/));

        // now we can imperatively navigate as the test
        history.push("/dashboard");
      },

      // second render from new location
      ({ div }) => {
        console.assert(div.innerHTML.match(/Dashboard/));

        // or we can simulate clicks on Links instead of
        // using history.push
        Simulate.click(div.querySelector("#click-me"), {
          button: 0
        });
      },

      // final render
      ({ location }) => {
        console.assert(location.pathname === "/");
        // you'll want something like `done()` so your test
        // fails if you never make it here.
        done();
      }
    ]
  });
});
```