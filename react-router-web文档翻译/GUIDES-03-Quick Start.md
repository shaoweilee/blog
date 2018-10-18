# Quick Start

最容易开始一个React项目的方式就是用Create React App了，它是一个使用了众多社区资源的Facebook项目。

如果没有它，那就先安装一下，然后开始吧！

```sh
npm install -g create-react-app
create-react-app demo-app
cd demo-app
```

## 安装

React Router DOM被放到了npm上，所以你可以用npm或者yarn安装。Create React App使用yarn，所以我们也用yarn。

```sh
yarn add react-router-dom
# 或者，使用npm
npm install react-router-dom
```

现在，你可以把这个基本示例复制粘贴到`src/App.js`：

```jsx
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const Home = () => (
  <div>
    <h2>Home</h2>
  </div>
);

const About = () => (
  <div>
    <h2>About</h2>
  </div>
);

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
);

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>Rendering with React</Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>Components</Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
      </li>
    </ul>

    <Route path={`${match.path}/:topicId`} component={Topic} />
    <Route exact path={match.path}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div>
);

const BasicExample = () => (
  <Router>
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/topics">Topics</Link>
        </li>
      </ul>

      <hr />

      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/topics" component={Topics} />
    </div>
  </Router>
);
export default BasicExample;
```

开始干吧，Happy routing！