# React学习笔记

[TOC]



## 一、React开发环境搭建

### 1、安装开发环境依赖

`babel-core`

`babel-loader`

`babel-preset-es2015`

`babel-preset-react`

#### 快捷安装

`npm i babel-core babel-loader babel-preset-es2015 babel-preset-react --save-dev`

### 2、安装react

`npm i react react-dom --save`

### 3、配置构建命令与babel编译

```javascript
module: {
    rules: [{
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
    }, {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
            loader: 'babel-loader',
            options: {
                presets: ['es2015', 'react']
            }
        }
    }]
}
```

## 二、jsx语法

render函数中仅包含一个容器div；

使用{}模板语法；

jsx-->js代码的流程：jsx通过babel编译为js，js通过ReactDom.render渲染进DOM树。

```jsx
//一个jsx示例。

import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './style.css';

class App extends Component {//注意这个 'App'

	render() {//函数
		const hello = 'hello jsx!';
		return (//return+圆括号
			<div className="app">//只能包含一个父容器，这里用div
				<h1>{hello}</h1>//可以用模板语法{}
                <h1>fuck world</h1>//这里为了方便就用text node
			</div>
		);
	}
}

ReactDOM.render(
    <App />,//又出现了 'App'
    document.getElementById('root')//好像是固定写法，必须为root
)
```

上面的jsx中这部分代码：

```jsx
//jsx
<div className="app">
	<h1>fuck world</h1>
</div>
```

经过babel编译为js：

```javascript
//js
React.createElement(
	'div',//容器元素标签，这里用的是div.
	{//元素特性，注意使用了className而不是class.
		className: 'app',
	},
	React.createElement(//创建子元素
		'h1',//子元素标签名
		null,//元素特性. 'null' 代表没有特性.
		'fuck world',//一个文本节点（子元素h1的内容）.
	),
);
```

通过ReactDOM.render()方法，把js创建的名为App（注意不是className定的app）的DOM插入到root元素中。

```html
<div id="root">
    <div data-reactroot="" class="app">
        <h1>fuck world</h1>
    </div>
</div>
```

## 三、编写&引用一个组件

在React中，一个组件就是一个文件夹。在另一个组件中，就可以引用这个组件。

使用了ES6语法的一个例子：

```jsx
//Input组件(单独的一个input文件夹，包含index.jsx, index.css)

//index.jsx
import React, {Component} from 'react';
import 'index.css';

class Input extends Component {
    render(){
        return (//注意这里的'('不能另起一行！
        	<div className='input-container'>
            	<input />
            </div>
        );
    }
}
export default Input;
```

在另一个组件中引用Input：

```jsx
import React, {Component} from 'react';
import ReactDom from 'react-dom';
import Input from './input';//注意这里。
import './style.css';

class App extends Component {
    render(){
        return (
        	<div className='app'>
            	<h1>hello?</h1>
                <Input />//注意这里的组件名称。
            </div>
        );
    }
}
ReactDom.render(
	<App />,
    document.getElementById('root');
);
```

## 四、事件

以上面的Input组件为例，继续做事件绑定。

```jsx
class Input extends Component {
    handleKeyUp(e){
        console.log(this);//注意回调函数中this的指向。
        console.log(e.target.value);
    }
    render(){
        return (
        	<div className='input-container'>
            	<input onKeyUp = {this.handleKeyUp.bind(this)}/>
            </div>
        );
    }
}
```

有以下几点需要注意：

- React事件使用驼峰命名，而不是js那样全部小写；
- 传递函数而不是字符串，事件用花括号包围，且是this的事件；
- 要阻止默认事件，不能同return false，必须用e.preventDefault()；
- 注意回调函数中this的指向，默认是没有绑定的，需要在绑定事件时，手动bind(this)；
- 用bind修正this指向后，事件对象e被隐式传递了，在事件处理函数中可以直接被调用，且不用担心e的兼容性问题。

## 五、列表渲染

上实例：

```jsx
//index.jsx
import React, {Component} from 'react';
import './index.css';

const todos = ['吃饭', '睡觉', '打豆豆'];
class List extends Component {
    render(){
        return(
        	<ul className='list'>
                {
                    todos.map(function(value, index){
                        return(
                            <li key={index}>
                                <input type='checkbox'/>
                                <span>{value}</span>
                            </li>
                        )
                    })
                }
            </ul>
        )
    }
}
export default List;
```

注意：

- 要为列表元素提供一个`key`，通常情况下，`key`是数组元素的id的字符串表示，没有id也可以用元素下标index，但这是最差的选择。经验主义：用到`map`了，就要用`key`。

## 六、props

假如父级组件想为子组件传参，继续拿列表作为实例：

```jsx
//父组件List
import React, {Component} from 'react';
import Item from '../item';
import './index.css';

const todos = ['吃饭', '睡觉', '打豆豆'];
class List extends Component {
    render(){
        return(
        	<ul>
                {
                    todos.map(function(value, index){
                        return(
                        	<Item key={index} value={value} index={index}/>//value和index是将传给子组件的参数。
                        )
                    })
                }
            </ul>
        )
    }
}
export default List;
```

```jsx
//子组件
import React, {Component} from 'react';
import './index.css';

class Item extends Component {
    render(){
        return(
        	<li>{this.props.value}{this.props.index}</li>//子组件使用this.prop变量得到父组件传来的参数。
        )
    }
}
export default Item;
```

## 七、state

`state`是组件内部控制状态的对象。与`props`不同，`props`是父组件定义的给子组件拿来用，对子组件来说，`props`是只读的。

一个实例：

```jsx
//Item
import React, {Component} from 'react';
import './index.css';

class Item extends Component {
    constructor(){
        super();
        this.state = {
            isChecked: false,
        };
    }
    clickHandler(){
        this.setState({
            isChecked: !this.state.isChecked,
        });
    }
    render(){
        return(
        	<li>
            	<input type="checkbox" onClick={this.clickHandler.bind(this)} />
                <span className={this.state.isChecked ? 'checked' : ''}>{this.props.value}</span>
            </li>
        )
    }
}
export default Item;
```

注意设置`state`只能通过`setState()`方法，直接赋值是无效的。

## 八、组件间通信

提起通信，观察者模式是一个很好的选择，但我们用“更React”的方式来做。

把父组件作为通信的中转站：

在父组件中，通过state存储数据，然后构造props把数据或方法传入子组件。

子组件调用props的方法来改变父组件中存储的数据。

重新写实例，这大概是最简单的了：

```jsx
//父组件
import React, { Component } from "react";
import ReactDom from "react-dom";
import './style.css';

import Input from "./input";
import List from "./list";

class App extends Component {
    constructor(){
        super();
        this.state = {
            keywords: '',
        }
    }
    onChange(value){
        this.setState({
            keywords: value,
        });
    }
    render(){
        return(
            <div className='app'>
                <h1>let's react!</h1>
                <Input onChange={this.onChange.bind(this)}/>
                <List keywords={this.state.keywords}/>
            </div>
        )
    }
}

ReactDom.render(
    <App />,
    document.getElementById('root'),
);
```

```jsx
//Input组件
import React, { Component } from "react";
import './index.css';

class Input extends Component {
    onKeyUp(e){
        this.props.onChange(e.target.value);//注意这里不是this.value。
    }
    render(){
        return(
            <div className='input-container'>
                <input type="text" onKeyUp={this.onKeyUp.bind(this)}/>
            </div>
        )
    }
}
export default Input;
```

```jsx
//List组件
import React, { Component } from "react";
import Item from '../item';
import fruits from '../fruits';//提供数据的文件
import './index.css';
class List extends Component {
	render(){
		const keywords = this.props.keywords;//注意要在这里取this.props。
		const matched = fruits.filter(function(item, index){
			return item.includes(keywords);
		});
		return(
			<ul className='list'>
				{
					matched.map(function(item, index){
						return <Item  key={index} item={item} className='item'/>
					})
				}
			</ul>
		)
	}
}
export default List;
```

## 九、ref与DOM

到目前为止，我们操作的是组件对象，或者叫React对象。当然了，上面的`e.target`还是DOM对象。React已经把DOM操作隔离了，如果要操作DOM元素，就需要用到`ref`。

参考以下实例：

```jsx
import React, { Component } from "react";
import './index.css';

class Input extends Component {
    onKeyUp(e){
        console.log(this.input);//here
    }
    render(){
        return(
            <div className='input-container'>
                <input type="text" 
                    onKeyUp={this.onKeyUp.bind(this)} 
                    ref={input=>{this.input = input}}//and here~
                />
            </div>
        )
    }
}
export default Input;
```

**注意，请用箭头函数实现。**



## 十、生命周期

`constructor`初始化

`componentWillMount`插入前

`render`插入到DOM中

`componentDidMount`插入后

`componentWillUpdate`组件更新前

`componentDidUpdate`组件更新后

`componentWillUnmount`从DOM移除前

...等等



## 十一、一些资源

[官网翻译版](https://doc.react-china.org/)

[React.js 小书](http://huziketang.com/books/react/)

[虚拟 Dom](http://www.alloyteam.com/2015/10/react-virtual-analysis-of-the-dom/)

[PropTypes](https://doc.react-china.org/docs/typechecking-with-proptypes.html)

[高阶组件](https://doc.react-china.org/docs/higher-order-components.html)



## 二十、虚拟DOM

## 二十一、render方法