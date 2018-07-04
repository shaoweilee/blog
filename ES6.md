# ES6

## 一、let与const

### let

**声明的变量只在块级作用域里有效**，有以下几个特别需要注意的：

```javascript
for (let i = 0; i < 3; i++) //父级作用域
{//子级作用域
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```

对于for循环来说，如果用let定义变量，那么 定义循环变量的那部分是一个父作用域，而循环体内部是一个子作用域；

- **不存在变量提升**，即如果用let声明变量，那这个变量只能在let声明之后使用，参考以下代码：

```JavaScript
for (let i = 0; i < 3; i++) {
  console.log(i);//Reference Error: i is not defiend
  let i = 'abc';//我们会有i的，外面的滚粗！
  console.log(i);
}
//这说明了for循环特殊性，也说明了不存在变量提升，也验证了下面的“暂时性死区”。
```

- **暂时性死区**：只要块级作用域内存在let命令，它所声明的变量就绑定了这个区域，不再受外部的影响。

这也解释了为什么上面的for循环中，子作用域内的i不能取到父作用域中i的值。

- **不允许在相同作用域内，重复声明变量**：

```javascript
// Uncaught SyntaxError: Identifier 'a' has already been declared
function func() 
{
  let a = 10;
  var a = 1;
}
```

```javascript
for (let i = 0; i < 5; i++) 
{
    let i = 2;//不是同一个作用域。
}
//不报错
```

```JavaScript
for (let i = 0; i < 5; i++) 
{
    var i = 2;//我的猜想是，循环体内没有let，不形成 函数作用域 或 块级作用域 中的任何一个。let声明的i仍在整个循环中有效，被视为同一个作用域？
}
//报错重定义
```



- **let实际上为js新增了块级作用域**。

块级作用域对声明函数的影响体现在：

ES5规定函数只能在全局作用域或函数作用域中声明。ES6明确规定了函数可以在块级作用域中声明，块级作用域中的**函数声明语句**行为类似于let，在块级作用域之外不可引用。

```JavaScript
// ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  if (false) {
      function f() { console.log('I am inside!'); }
  }
  f();
}());
//理论上会输出'I am outside!'
//但实际上会报错：f is not a function
```

在ES6环境下，理论上来讲，if作用域内的函数声明对外部无影响，f()执行的实际上是外层的f。

但是实际上，在ES6环境下，浏览器实际上是这样对待块级作用域内的函数声明的：

- 允许在块级作用域内声明函数。
- 函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部。
- 同时，函数声明还会提升到所在的块级作用域的头部。

因此，在ES6环境下，浏览器实际上执行的是下面的代码：

```javascript
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }
  f();
}());
// Uncaught TypeError: f is not a function
```

因此，应该避免在块级作用域内用函数声明。最好用`let`写成函数表达式，例如下面的代码就不会报错：

```JavaScript
function f() { console.log('I am outside!'); }
(function () {
    if (false) {
        let f = function(){ console.log('I am inside!'); }
    }
    f();
}());
```

最后，考虑这个代码：

```JavaScript
if (true) {
    console.log(f)//undefined
    f();// TypeError: f is not a function
    if (true) {
        function f (){
            console.log(1);
        }
    }
}
```

**总结**：ES6允许在块级作用域内使用函数声明，但处理行为却类似`var`（仅把**变量**提升到函数作用域或全局作用域头部），所以最好别这样做。在块级作用域内，用`let`写成函数表达式更好。

### const

const命令其实是保证的保存变量的内存地址不变，因此：

对于原始值来说，不可变，等同于常量；

对于引用值来说，该内存地址不可指向新的数据结构，但可对现存的数据结构进行更改。

### ES6声明变量的六种方法

`var` `let` `const` `function` `import` `class`

### 额外的知识

在ES6中，`let` `const` `class`声明的全局变量，不再是window的属性。



## 二、变量的解构赋值

ES6允许按照一定模式，将右边的数据赋给左边的变量。左边的模式决定是哪种解构赋值。`[]`是数组的，会调用等号右侧的数据结构的遍历器接口，`{}`是对象的，不需要遍历器接口，只找同名属性。但是使用了扩展运算符`...`的对象解构赋值，需要浏览器支持ES2018，目前（2018年06月20日）babel也不支持转译。

```javascript
let [a, b, c] = [1, 2, 3];
```

右边类型必须是对象或数组，如果是其他的，会先转成对象。

为变量赋值的顺序是从左到右。

直接看一个比较复杂的例子：

```JavaScript
let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

对于两边不对等的情况，还分两种情况：

左边模式多，右边数据少：

```JavaScript
let [foo] = [];
let [bar, foo] = [1];
//foo undefined
```

左边模式少，右边数据多：

```JavaScript
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```

但是如果右边不是可遍历的结构，就会报错。

### 默认值

解构赋值 允许指定默认值。

如果解构赋值的结果是，一个变量 `===` `undefined`，那么该变量的默认值就会生效，`null`则不会。

默认值可以用解构赋值的其他变量，但该变量必须已经声明。

```JavaScript
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```

### 数组的解构赋值

只要某种数据结构具有Iterator接口，就可以用**数组**形式解构赋值。

### 对象的解构赋值

这个就很厉害了。对象解构赋值有两种写法：

**对象的解构赋值，是不需要遍历器接口的！**

- 提取对象中的变量（其实是第二种写法的简写）：

```JavaScript
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```

- 把对象中的属性赋给新的变量：

```javascript
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```

真正被赋值的是后面的变量`baz`。

但是要注意一点，如果左边是嵌套的对象，而这个子对象的模式不存在，就会报错，默认值也不生效。

```JavaScript
//Error
let {foo: {bar=1}} = {baz: 'baz'};
```

```JavaScript
//OK
let {foo: bar=1} = {baz: 'baz'};
```

对象解构赋值的妙用：

```JavaScript
let { log, sin, cos } = Math;
let { log } = console;
let { toString: objToString } = Object.prototype;
//or
let { toString: objToString } = {};
```

数组也行：

```JavaScript
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```

### 字符串的解构赋值

字符串也可以解构赋值。因为字符串被转化成了类数组的对象！

```JavaScript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

类数组对象还有`length`属性：

```JavaScript
let {length : len} = 'hello';
len // 5
```

### 数值和布尔值的解构赋值

同样是JS，它们就很秀：

```JavaScript
let {toString: numberToString} = 1;//or Number.prototype
numberToString === Number.prototype.toString // true

let {toString: boolToString} = true;//or Boolean.protptype
booleanToString === Boolean.prototype.toString // true
```

但是`null`和`undefined`就不行了，因为他们无法转为对象。

```JavaScript
let {undefined: a=1} = undefined;
//TypeError: undefined has no properties
```



### 函数参数的解构赋值

更秀了。。。：

```JavaScript
function toArray( {x = 'aaa', y = 0} = {} ) {
  return [x, y];
}
console.log( 
  toArray( {y: 2} ) 
); //['aaa', 2]
```

上面的函数为`x`和`y`指定了默认值，同时也为整体参数提供了默认值。

过程是，传参后函数执行前，就对传入的参数进行了解构：

```javascript
toArray( {y: 2} );
{x = 'aaa', y = 0} = {y: 2}
```

如果不传参，就对默认参数解构：

```JavaScript
toArray();
{x = 'aaa', y = 0} = {}
```

如果没有默认值，就会报错 `TypeError: (destructured parameter) is undefined`-->意思是 解构参数为`undefined`，而上面已经了解过`undefined`是不能被解构的。

### 用途

交换变量值

从函数返回多个值

函数传参时，不用担心参数顺序

函数参数直接指定默认值，不用合并参数了

提取JSON对象数据

遍历Map结构

加载模块指定的方法

## 三、字符串的扩展

### 字符串新增方法

先记这几个方法吧：

**includes()**：返回布尔值，表示是否找到了参数字符串。

**startsWith()**：返回布尔值，表示参数字符串是否在原字符串的头部。

**endsWith()**：返回布尔值，表示参数字符串是否在原字符串的尾部。

这三个方法都支持第二个参数，表示开始搜索的位置。 

**repeat()**：返回新字符串，表示将原字符串重复n次，参数是小数的话会被取整。记两个特殊值：`-1`报错，`0`表示清空。

### 模板字符串

它会保留空格和缩进！太棒了！

## 四、数值的扩展

### isNaN和isFinite

**Number.isNaN()**：检查一个值是否为`NaN`。

**Number.isFinite()**：检查一个值是否为`Infinity`。

它们与传统的全局`isNaN()`和`isFinite()`方法区别在于，传统方法先用Number()将非数值的值转为数值，再判断。

`Number.isFinite()`对于非数值一律返回false。

`Number.isNaN()`只有对于`NaN`才返回`true`，非`NaN`一律返回`false`。 

### parseInt和ParseFloat

ES6将全局下的parseInt和ParseFloat移植到了Number上，行为完全保持一致。

### Number.EPSILON

常量，等于2的-52次方，即`Math.pow(2, -52)`。

### 安全整数和Number.isSafeInteger()

JS可以精确表示的整数范围在`Math.pow(2, -53)`到`Math.pow(2, 53)`之间。超过这个范围的话，就无法准确表示这个值了，例如：

```JavaScript
Math.pow(2, 53) === Math.pow(2, -53) + 1;
//true
```

ES6引入了两个常量来表示这俩值：

```JavaScript
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1;
Number.MIN_SAFE_INTEGER === -(Math.pow(2, 53) - 1);
```

`Number.isSafeInteger()`就是判断一个数是不是在这两个整数之间的。

### 新增方法

先记下几个自己能理解的吧= =：

`Math.cbrt()`计算一个数字的立方根

`Math.hypot()`计算所有参数平方和的平方根

### 指数运算符

`**`

## 五、函数的扩展

### 函数参数的默认值

实际上，在上面解构赋值那节，就涉及到了。

ES6允许为函数的参数设置默认值，如下面一个极简的例子：

```JavaScript
function simple(x=0, y=0) {
    
}
```

有个比较容易混淆的知识点是**解构赋值的默认值**和**参数整体的默认值**。可以查看解构赋值那节。

### 默认值对函数length属性的影响

以前，length属性是函数期望参数的个数。ES6中为函数指定了某个参数的默认值后，该参数就不计入length中了。并且，如果设置默认值的参数不是尾参数，那么其后的所有参数都不计入length了。

```javascript
(function(a, b=1, c){}).length //1
```

### 默认值对参数作用域的影响

一旦为参数设置了默认值，在函数执行前，参数部分就形成了一个单独的作用域：

```Javascript
let x = 1;
function f (x, y=x) {
    
}
f(2);
```

看复杂的例子吧：

```JavaScript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}
foo() // 3
x // 1
```

```JavaScript
var x = 1;
function foo(x, y = function() { x = 2; }) {
  x = 3;
  y();
  console.log(x);
}
foo() // 2
x // 1
```

### rest参数...

ES6新增了极为方便的rest参数，写法是`...变量名`，爽歪歪：

```JavaScript
function add(...values) {
  let sum = 0;
  for (var val of values) {
    sum += val;
  }
  return sum;
}
add(2, 5, 3) // 10
```

`rest`参数的**两个区别**：

对比原来的`arguments`对象，rest参数的区别在于：

- 同样是实参集合，`arguments`是类数组，而`rest`是真正的数组；
- `arguments`是所有实参集合，而`rest`只是未被分配给形参的实参数组；

`rest`参数的**两个要点**：

- `rest`只能是最后一个参数，否则报错；
- 函数的`length`属性，不包括`rest`参数；

### 严格模式

ES6规定，只要函数使用了默认参数、解构赋值、扩展运算符，就无法在函数内部显示指定严格模式。

原因是，虽然是在函数内部指定了严格模式，但是严格模式同时作用于函数参数与函数体。这样就有一个不合理的地方，因为函数执行的时候才知道函数要不要用严格模式执行，但是参数应该优先于函数体执行。

有两种方法可以规避这种限制：

一是从严格模式指定的位置下手，设置为全局的严格模式；

二是从函数身上下手，把函数包裹在一个无参数的立即执行函数里面：

```JavaScript
const f = (function(){
    'use strict';
    return function(x = 42){
        return x;
    }
})();
```

其实也是改变了严格模式声明的位置而已。

### 函数的name属性

返回函数名，早就可用，不过在ES6中才把它写入了标准。

但ES6中的行为和之前有差异，在一个函数表达式中：

```JavaScript
var f = function(){}
//in ES5
f.name //""
//in ES6
f.name //"f"
```

如果把一个具名函数赋值给一个变量，那么ES5和ES6返回的都是原本的名字：

```Javascript
function f(){}
var g = f;
g.name //both "f"
```

### 箭头函数

终于到了箭头函数。

记住这个：箭头后面没有花括号，就代表是一个return语句。但箭头函数只是个函数，并不会立即执行。

先来个最简单的实现：

```JavaScript
var f = v => v;
//等同于
var f = function(v){return v;}
```

更简单的实现，连参数都省略了：

```JavaScript
var f = () => 5;
//等同于
var f = function(){return 5;}
```

稍微复杂一点的实现，有两个或以上的参数：

```JavaScript
var f = (x, y) => x + y
```

更复杂一点，如果函数体部分有多条语句，就需要用大括号括起来，但是如果要返回一个对象，就必须在对象外面加圆括号，否则报错：

```JavaScript
let getTempItem = id => ({ id: id, name: "Temp" });
```

还可以像下面这样和解构赋值一起用：

```JavaScript
let fullName = ({first, last}) => `my fullname is ${first} ${last}`
fullName( {first: lee, last: shaowei} );
```

#### 箭头函数的使用注意点

很重要！

1. 箭头函数不提供this绑定，使用this时依照作用域链规则寻找。
2. 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。因为没有this绑定。
3. 不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
4. 不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数。

## 六、数组的扩展

### 扩展运算符...

`...`是ES6新增的运算符。有两种用法：

- 可以将数组或类数组转为用逗号隔开的参数序列

```JavaScript
console.log(...[1, 2, 3])//1, 2, 3
//这么做，还可以将类数组转为数组
var arr = [...document.querySelectorAll('div')];//arr is Array，我认为其实相当于：
var arr = new Array(...document.querySelectorAll('div'));
```

- 可以将没有对应形参的实参转为数组

```JavaScript
function f(x, ...rest){console.log(rest)}
f(1, 2, 3);
```

扩展运算符的后面还可以是表达式，只要表达式返回的结果是数组就行：

```JavaScript
var arr = [...(true ? [1] : []), 2];//Array [1, 2]
var arr = [...(true ? [] : [1]), 2];//Array [2]
```

#### 扩展运算符的应用

复制数组、合并数组、与解构赋值结合、字符串转数组、实现了Iterator接口的对象转数组、Map Set结构、Generator函数。

复制数组：

```JavaScript
var a1 = [1, 2];
//把现有的数组展开后拿到参数再用字面量形式新建一个数组，但如果数组中有引用值，引用值仍是浅拷贝。
var a2 = [...a1];
//解构赋值，把a1所有项塞入a2中。在数组的解构赋值中，如果左边变量有...，那么这个变量就包含了匹配到的模式下的所有剩余数据。
var [...a2] = a1;
```

合并数组

```Javascript
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];
var combinedArr = [...arr1, ...arr2, ...arr3];
```

与解构赋值结合

按顺序和模式一个一个赋值。

```JavaScript
const [first, last, ...middle] = [1, 2, 3, 4, 5];
middle;//[3, 4, 5];
const [first, second, [...middle], last] = [1, 2, [3, 4], 5];
middle;//[3, 4];
```

字符串转数组

```JavaScript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

Iterator接口对象转数组

任何实现了Iterator接口的对象，都可以通过`...`转为数组。

```JavaScript
let nodeList = document.querySelectorAll('div');
let array = [...nodeList];
```

没有部署Iterator接口的对象，长得像是不行的：

```JavaScript
let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};
// TypeError: Cannot spread non-iterable object.
let arr = [...arrayLike];
//但是可以用Array.from()转换。
```

#### 扩展运算符真的不能用于原生对象吗？

其实是可以用的。但是需要浏览器支持ES2018。

对象的扩展运算符，用于取出参数对象**自身的**、**未被读取的**、所有**可遍历**属性，**浅拷贝**到当前对象中。

直接看复杂的例子：

```JavaScript
function Test({x=1, ...y} = {x: 8, y: 9}){
  this.x = x;
  this.y = y;
}

var test = new Test({
  x: 5,
  y: 6,
  z: 7,
});//整体参数默认值不生效
```

上例中，先对参数进行解构赋值，发现参数中的x被读取了，无视它。然后对`{y: 6, z: 7}`这个对象进行扩展再拷贝给y。本来对象的解构赋值是找同名属性，但是这里是把剩余属性全拷贝给了y。

更有趣的例子：

```JavaScript
function Test({x=1, ...y} = {x: 8, y: 9}){
  this.x = x;
  this.y = y;
}

var test = new Test();//整体参数默认值生效，对默认参数进行解构赋值。
```



### Array.from与三个参数

将两类对象转为真正的数组：类数组对象，可遍历对象。

```JavaScript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

同样可以转数组，`...`和`Array.from`的不同之处在于，`...`调用的是iterator接口，如果一个对象没有部署这个接口，就无法转换；Array.from方法只认对象的length属性，因此，只要有length属性的对象，都可以通过Array.from转为数组，而扩展运算符就不行了。

```JavaScript
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```

如果需要做兼容性的话：

```JavaScript
const toArray = (() => {
    return Array.from ? Array.from : obj => {
        return [].slice.call(obj);
    }
})();
```

`Array.from`的第二个参数可以设置为一个函数，作用类似于`map`，对第一个参数转为的数组的每一项进行操作，最终`Array.from`返回的数组是操作后的数组，例如：

```JavaScript
Array.from(
    document.querySelectorAll('div'), 
    ele => {return ele.id}
);
Array.from(
	{length: 3},
    ele => {return ele ? true : false}
);
//Array(3) [ false, false, false ]
```

如果`Array.from`的map函数用到了this，那么可以为`Array.from`提供第三个参数，来绑定this。

### Array.of

`Array.of`用于将一组值转换为数组。相当于加强版的数组构造函数`Array()`，因为`Array()`对不同数量的参数，处理行为不同：

```JavaScript
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```

而`Array.of`总是返回由参数构成的数组：

```JavaScript
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

`Array.of`仍可以用`slice`做兼容：

```JavaScript
function ArrayOf (){
    return [].slice.call(arguments);
}
```

### Array.prototype.copyWithin()

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

将数组内从`start`到`end`的数据复制到从`target`开始的位置。

- `target`（必需）：从该位置开始替换数据。如果为负值，表示倒数。
- `start`（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
- `end`（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

例子：

```JavaScript
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]
```

### Array.prototype.find()和Array.prototype.findIndex()

这两个函数都是寻找数组中符合条件的成员，接受一个回调函数作为参数，对数组所有成员执行这个回调函数，直到找出第一个返回值为true的成员。

```JavaScript
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

```JavaScript
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

另外，这两个方法都可以发现`NaN`，弥补了数组的`indexOf`方法的不足。 

```JavaScript
[NaN].indexOf(NaN)
// -1
[NaN].findIndex(y => Object.is(NaN, y))
// 0
```

### Array.prototype.fill()

### Array.prototype.entries(), keys(), values()

### Array.prototype.includes()

### 数组的空位



## 七、对象的扩展

### 属性和方法的简洁表示

### 属性名表达式

### 方法的name属性

### Object.is()

### Object.assign()

注意：浅拷贝可枚举属性；同名属性替换；把数组视为对象；

常见用途：为对象或this添加属性而不是用`=`，为对象或构造函数的原型添加方法而不是`=`；

### 属性的可枚举性 和 属性遍历

### Object.getOwnPropertyDescriptors()

### \_\_proto\_\_ Object.getPrototypeOf() Object.setPrototypeOf() Object.create() 

### super关键字

### Object.keys() Object.values() Object.entries()

## 八、Symbol

### 概述

Symbol值通过`Symbol()`函数生成，Symbol()可以传入字符串、对象、数字、函数、undefined、null、布尔值，只有对undefined和对象的操作不同：

```JavaScript
Symbol('string');//Symbol(string)
Symbol(NaN);//Symbol(NaN)
Symbol(Math.round);//Symbol(function round() {[native code]})
Symbol(null);//Symbol(null)
Symbol(true);//Symbol(true)

Symbol({obj: 1});//Symbol([object Object])
Symbol(undefined);//Symbol()
```

symbol值可以显示转为字符串，隐式转为布尔值，不能转为其他值。

### 作为属性名的Symbol

`[]`

### 消除魔术字符串

### 属性名的遍历

只有`Object.getOwnPropertySymbols`方法和 `Reflect.ownKeys` 方法可以访问到对象的`Symbol`属性名。

### Symbol.for(key) 和 Symbol.keyfor(symbol)

```javascript
Symbol('foo') === Symbol.for('foo') //false
```

`Symbol.for()`会被登记在全局中供搜索，`Symbol()`则不会。所以即使已经用`Symbol('foo')`生成了symbol，也不会被`Symbol.for('foo')`搜索到，所以`Symbol.for('foo')`返回了新的Symbol。

`Symbol.keyFor()`方法返回一个已登记的 Symbol 类型值的`key`。 

### 模块的Singleton模式



## 九、Set和Map

### Set

Set结构类似数组，但成员都是唯一的，没有重复值。内部使用的查重算法类似===，但认为NaN等于NaN。

Set()函数接受一个数组，或者有iterable接口的其他数据作为参数。

#### Set的属性和方法：

##### set属性

- `Set.prototype.constructor`
- `Set.prototype.size`

##### set操作方法

- `add(value)`：添加某个值，返回 Set 结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `clear()`：清除所有成员，没有返回值。

##### set遍历方法

- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `forEach()`：使用回调函数遍历每个成员

`keys`方法、`values`方法、`entries`方法返回的都是遍历器对象（Iterator）。由于Set对象没有键名，只有键值，或者说键名键值是同一个值，因此keys方法和values方法完全一致。注意Set结构不可用数字下标或字符串下标取值。

`entries`方法返回的遍历器是同时存在键名和键值的。

可以直接用`for..of`遍历Set。

如果想在遍历操作中同步改变原来的Set结构，目前只能通过set转数组，对数组进行操作，再把这个数组构建为set再赋给原来的set。如：

```JavaScript
// 方法一
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set的值是2, 4, 6

// 方法二
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set的值是2, 4, 6
```

### WeakSet

与Set的区别：

成员只能是对象；

垃圾回收机制；add delete has  不可遍历。

WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。 

### Map

#### 含义和基本用法

如果说Set结构扩展了数组，那么Map结构就扩展了对象。传统的对象提供了“字符串-值”或“symbol-值”的对应，而Map提供了“值-值”的对应。任意类型的数据，都可以当做Map的键。对键值的比较遵循类似`===`的精确比较算法，但NaN等于NaN，与Set结构内比较两个值是否相等的算法一样。

任何具有Iterator接口，且每个成员都是一个双元素数组的数据结构，都可以当做Map构造函数的参数。

#### Map的属性和方法：

##### map属性

`Set.prototype.size`返回成员总数。

##### map操作方法

- `set(key, value)`，返回整个Map结构；
- `get(key)`，读取对应的键值，找不到就返回undefined；
- `has(key)`，返回布尔值，表示某个键是否在当前Map对象中；
- `delete(key)`，返回布尔值表示是否删除成功；
- `clear()`，清除所有成员，没有返回值。

##### map遍历方法

- `keys()`：返回键名的遍历器。
- `values()`：返回键值的遍历器。
- `entries()`：返回所有成员的遍历器。
- `forEach()`：遍历 Map 的所有成员。

Map结构转数组：[...map]

### WeakMap

#### 与Map的区别：

- `WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。 
- `WeakMap`的键名所指向的对象，不计入垃圾回收机制。（只有键名是弱引用） 防止内存泄漏。
- 没有遍历方法和size属性，无法清空。
- 只有四个方法可用：`get()`, `set()`, `has()`, `delete()`。

#### WeakMap的用途

DOM节点作为键名；部署私有属性。

## 十、Proxy

Proxy是一个构造函数。

```javascript
var p = new Proxy(target, handler);
```

在目标对象`target`之上设置一层代理`p`，使得对`target`的所有操作都由代理`p`转发到`handler`提供的方法上，使用这些方法来处理。

### proxy可以做什么？

以下是`handler`支持的拦截操作：

#### `get(目标对象, 属性名[, proxy实例本身])`

拦截属性的读取操作。

```JavaScript
var dom = new Proxy({}, {
  get: function(target, nodeName){
    var el = document.createElement(nodeName);
    return function(attrs={}, ...children){
      for(let prop of Object.keys(attrs)){
        el.setAttribute(prop, attrs[prop]);
      }
      for(let child of children){
        if(typeof child === 'string'){
           child = document.createTextNode(child);
        }
        el.appendChild(child);
      }
      return el;
    }
  }
});

var el = dom.div({},
  'hello, world!',
  dom.ul({class: 'text'},
    dom.li({class: 'items'}, 'hello!'),
    dom.li({class: 'items'}, 'hello!'),
    dom.li({class: 'items'}, 'hello!')
  )
);
```

#### `set(目标对象, 属性名, 属性值[, proxy实例本身])`

拦截属性的赋值操作。

```JavaScript
//双向数据绑定的一种实现方式：
var o = {data: ''}
var doc = document.body;

var pO = new Proxy(o, {
  set: function(target, propName, propValue){
    target[propName] = propValue;
    doc.innerHTML = propValue;
  }
});
var pDoc = new Proxy(doc, {
  set: function(target, propName, propValue){
    if(propName === 'innerHTML'){
      o.data = propValue;
      target[propName] = propValue;
    }
  }
});
```



#### `apply(目标对象, 目标this, 目标对象的参数数组)`

拦截函数的调用、`call`和`apply`操作。

#### `has(目标对象, 需查询的属性名)`

拦截判断对象是否有某个属性的操作，最典型的就是`in`运算符。但对`for...in`循环不生效。

#### `construct(目标构造函数, 构造函数的参数们[, proxy实例本身])`

拦截`new`命令，返回的必须是个对象。

#### `deleteProperty()`

#### `defineProperty()`

#### `getOwnPropertyDescriptor()`

#### `getPrototypeOf()`

#### `setPrototypeOf()`

#### `isExtensible()`

#### `preventExtensions()`

#### `ownKeys()`





## 十一、Reflect



## 十二、Promise

Promise对象用于表示一个异步操作的最终状态（完成或者失败）。新建时立即执行。

与传统异步编程的区别：

1. 为异步编程提供了状态，使异步操作变得可控：

   `pending`/`fullfilled`/`rejected`.

   当resolve函数被调用时，promise状态变为fullfilled；当reject函数被调用时，promise状态变为rejected。

   回调函数是在一个异步操作完成后立即执行这个回调函数，而promise是异步操作完成后，后续操作的时机是then方法决定的。

2. 回调地狱变成了promise链。

### 基本用法

```JavaScript
var promise = new Promise(function(resolve, reject){//这个函数被称为executor函数。
    //异步操作完成时
    resolve(value);//value是要传出去的数据。
});
```

执行顺序：promise建立后立即执行，promise状态发生改变后执行then方法，then方法中的回调函数也是在当前同步任务执行完毕后执行。

### then

promise状态发生改变时，触发then的调用。then方法的参数是两个回调函数：`onfullfilled`，`onrejected`。

注意使用箭头函数使得流程清晰。

### catch

catch()用于捕捉promise运行过程中发生的错误，虽然then的第二个函数参数也是发生错误时的回调函数，但是更推荐用catch，使得then的第二个参数不必要了。

catch的返回值仍然是一个promise对象，如果catch的函数参数抛出错误或者返回一个本身失败的promise，那么catch返回的promise状态就是rejected，否则就是fullfilled。

### finally

### all

用于将多个promise实例数组，包装成一个promise实例，这个promise实例的状态由数组每个promise成员的状态决定：

- 只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会**异步**变成`fulfilled`，此时`p1`、`p2`、`p3`的resolve返回值组成一个数组，传递给`p`的回调函数。
- 只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就**异步**变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

返回的promise实例仍然会等所有同步任务完成之后改变状态，在那之前还是`pending`。

也就是说，Promise.all本身也是个异步操作。

### race

从多个promise对象组成的数组中选出一个确定状态最快的promise，将这个promise的状态和返回值都给`race()`方法返回的promise对象。

### Promise.resolve()

resolve()用于将一个对象转为Promise对象，等价于下面的写法：

```JavaScript
Promise.resolve('foo');
// 等价于
new Promise(function(resolve, reject){
    resolve('foo');
});
```

参数的不同，有四种情况：

- 参数是一个promise实例；

  返回这个实例，不作任何更改。

- 参数是一个thenable对象；

  异步执行thenable对象中的then方法，并且返回promise对象。

- 参数不是thenable对象，或者不是对象；

  返回一个fullfilled状态的promise对象，参数将被传递。


- 不带参数。

  返回一个fullfilled状态的promise对象。

### Promise.reject()

返回一个rejected状态的promise，参数作为reason被传递。

## 十三、Iterator

### 概念

Iterator是一种接口，为不同的数据结构提供相同的访问机制。Iterator接口主要供`for…of`循环消费。实际上，ES6很多语法都建立在Iterator接口之上。

手写：

```JavaScript
var iterator = makeIterator('???');
iterator.next();//{value: 1, done: false}

function makeIterator(array){
  var nextIndex = 0;
  return {
    next: function(){
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false}:
        {value: undefined, done: true}
    }
  }
}
```



### 默认Iterator接口

默认的Iterator接口部署在数据结构的`Symbol.iterator`属性上。也就是说，只要一个数据结构具有`Symbol.iterator`属性，该数据结构就被认为是可遍历的。`Symbol.iterator`属性指向一个当前数据结构的遍历器生成函数（不同数据结构的遍历器生成函数是不一样的，比如`[][Symbol.iterator]!==''[Symbol.iterator]`）。至于`Symbol.iterator`，只是一个表达式，表明返回Symbol对象的`Iterator`属性，这个属性值是`Symbol(Symbol.Iterator)`，如下：

```JavaScript
console.log(Symbol.iterator);
//Symbol(Symbol.iterator)
```

调用一个数据结构的`Symbol.iterator`遍历器方法可以得到一个遍历器对象，遍历器对象的特征是具有`next()`方法，执行这个方法，可以返回一个包含该数据结构成员的对象，这个对象包含`value`与`done`属性。

原生具备Iterator接口的数据结构如下：

- Array

  ```JavaScript
  var arr = [1, 2, 3];
  arr[Symbol.iterator] === arr.values //true
  arr.entries() //Array Iterator {  }
  ```

- Map

  ```javascript
  var map = new Map();
  map[Symbol.iterator] === map.entries //true
  map.entries() //Map Iterator {  }
  ```

- Set

  ```JavaScript
  var set = new Set();
  set[Symbol.iterator] === set.values //true
  set.entries() //Set Iterator {  }
  ```

- String

  ```javascript
  var s = 'abc';
  s[Symbol.iterator]() //String Iterator {  }
  ```

- TypedArray

- 函数的 arguments 对象

- NodeList 对象

原生对象不具备遍历器接口，需要自己在对象的`[Symbol.iterator]`属性部署。

```JavaScript
var obj = {
  name: 'shaoweili',
  age: 25,
  likes: ['eat', 'sleep'](为试验解构赋值新增的。)
}
```

```JavaScript
var iterator = {
    [Symbol.iterator]: function(){
    var keys = Object.keys(this);
    return {
      next: ()=>{//必须使用箭头函数保证this指向外层，也就是obj
        if(keys.length > 0){
          var key = keys.shift();
          return {
            value: [ key, this[key] ],
            done: false
          }
        }else{
          return {
            value: undefined,
            done: true
          }
        }
      }
    }
  }
}
```

```JavaScript
Object.assign(obj, iterator);
```

此时对象`obj`就已经是可遍历对象了。更厉害的来了：

```JavaScript
[...obj];//Array[['name', 'shaoweili'], ['age', 25]]
Array.from(obj);//Array[['name', 'shaoweili'], ['age', 25]]
new Set(obj);//Set [ (2) […], (2) […] ]
new Map(obj);//Map { name → "shaoweili", age → 25 }
```

所有依赖Iterator接口才能执行的操作，都可以了。

然而，对一个拥有Iterator接口的对象，运用数组的解构赋值会发生什么？我试验了下：

```JavaScript
var [a, b, c] = obj;
//a: Array [ "name", "shaoweili" ]
//b: Array [ "age", 25 ]
//c: Array [ "likes", (2) […] ]
//似乎是调用了遍历器对象的next()方法，把所有value压入了一个数组，然后对这个数组进行了解构赋值。
//但如果解构变量出现了name这个属性名，name所对应的就变成了键名和值组成的字符串，其他变量名不会发生这种情况。这是因为强大的window.name会把赋给它的值变为字符串，如果对非全局作用域下的name赋值就不会这样了：
var [a, b, name] = obj;
//a: Array [ "name", "shaoweili" ]
//b: Array [ "age", 25 ]
//name: likes,eat,sleep
```

### 调用Iterator接口的场合

- 数组和set、map的解构赋值；
- 扩展运算符`...`展开或转数组；
- yield；
- 接受数组为参数的场合
  - for...of
  - Array.from()
  - Map(), Set(), WeakMap(), WeakSet()（比如`new Map([['a',1],['b',2]])`）
  - Promise.all()
  - Promise.race()

### Iterator接口与Generator函数

### 遍历器对象的return()和throw()方法

### for…of循环

#### 数组

```javascript
arr[Symbol.iterator] === arr.values//true
```

数组的遍历器函数就是values函数，返回数组所有成员，也可以说是键值。键名用`keys()`获取，键值对用`entries()`获取。

可以用`for...of`代替`forEach()`，因为`for…of`是支持`break`和`continue`操作的：

```JavaScript
var arr = ['hello', 'world', 'memda']
for(var x of arr.entries()){
  if(x[1] === 'world'){
    continue;
  }
  console.log(x[0], x[1]);
}
```

`for…of`只会遍历具有数字索引的属性，这一点秒杀`for…in`。

#### set与map

对Set遍历返回的是一个值，因为：

```JavaScript
Set.prototype[Symbol.iterator] === Set.prototype.values//true
```

对Map遍历返回的是键值对数组，因为：

```JavaScript
Map.prototype[Symbol.iterator] === Map.prototype.entries//true
```

#### entries()、values()、keys()

数组、map、set都有这三个方法，调用后都返回遍历器对象。

需要说明的是`entries()`，它总是返回键值对组成的数组：

- 对于数组，键名是索引值；
- 对于set，键名与键值相同；
- 对于map，就是键值对啦。

#### 对象

除了上面手动写Iterator接口，还可以：

用Object.keys生成一个数组供遍历；

用Generator函数包装



## 十四、Generator函数

这样理解Generator函数：

- 状态机：内部通过`yield`表达式，封装了多个状态；
- 遍历器生成器：返回一个遍历器对象，可以依次遍历Generator函数的内部状态；
- 还是一个可以暂停执行的函数；
- 为异步编程提供了新思路。

```JavaScript
function* generator(){
    console.log('start running');
    yield 'hello';
    yield 'world';
    return 'ending';
}
var hwIterator = generator();
hwIterator.next();//start running  Object { value: "hello", done: false }
hwIterator.next();//Object { value: "world", done: false }
hwIterator.next();//Object { value: "ending", done: true }
hwIterator.next();//Object { value: undefined, done: true }
```

流程：`generator()`并没有开始执行函数，而是等待遍历器对象的`next()`方法执行。发现第一次`next()`执行，`generator`就开始执行，直到遇到第一个`yield`，执行完这个`yield`，就进入等待状态，等待下次`next()`命令。所以`generator()`会发现并没有输出start running。

调用生成的遍历器对象的`next()`方法，会返回一个对象，有value和done属性，value就是yield表达式的值。

### yield表达式

`yield`其实是一个暂停标志，只能用在`generator`函数中。yield表达式本身没有返回值，或者说总是返回undefined。

遍历器对象的`next`方法的运行逻辑如下。

1. 遇到`yield`表达式，就暂停执行后面的操作，并将**紧跟在`yield`后面的那个表达式**的值（不是yield表达式的返回值。它们是完全不同的。），作为返回的对象的`value`属性值。
2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

### 与Iterator接口的关系

任意对象的`[Symbol.iterator]`方法，等同于该对象的遍历器生成函数，调用该函数会返回一个遍历器对象。Generator函数就是遍历器生成函数，所以，可以把一个generator函数赋给一个对象的`[Symbol.iterator]`属性，使其具有Iterator接口。

```JavaScript
var obj = {
    [Symbol.iterator]: function * (){
        yield 1;
        yield 2;
        yield 3;
    }
}
[...obj]//Array(3) [ 1, 2, 3 ]
```

### next()方法的参数

重申：yield本身不具有返回值。但是在调用遍历器对象的next方法时，可以加参数，作为上一次yield表达式的返回值（不是紧跟在yield后面的那个表达式的值哦）。实际上实现了向generator函数内部输入值：

```JavaScript
function * gen (x){
    var y = yield (x + 1);
    var z = yield (y + 1);
    var b = yield (z + 1);
    return y + z + b;
}
var it = gen(1);
it.next(2);
it.next(3);
it.next(4);
```

### for…of循环

```JavaScript
for(var x of it){}//x其实是每次调用遍历器对象的next方法返回的对象中的value值。
//一旦那个对象的done为true，循环就终止了。
```

```JavaScript
function* fibonacci() {
  let [prev, curr] = [0, 1];
  for (;;) {
    yield curr;
    [prev, curr] = [curr, prev + curr];
  }
}

for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```

通过generator函数，使原生对象可被for…of循环遍历：

```JavaScript
//针对如下对象：
var obj = {
  names: 'lishaowei',
  age: 25,
  likes: ['eat', 'sleep']
}
```

```JavaScript
//写法1，为原生对象添加Iterator接口。依赖于for...of循环自动寻找并调用对象的遍历器接口
function * objEntries(){
  let keys = Object.keys(this);
  for(let key of keys){
    yield [key, this[key]];
  }
}

obj[Symbol.iterator] = objEntries;

for(var [x, y] of obj){
  console.log(x, y);
}
```

```JavaScript
//写法2，用generator函数包装原生对象，直接返回一个遍历器对象
function * iteratorWrapper(obj){
    var keys = Object.keys(obj);
    for(let key of keys){
        yield [key, obj[key]];
    }
}
for(var [x, y] of iteratorWrapper(obj)){
  console.log(x, y);
}
```

除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。

### yield*表达式

从语法角度看，如果`yield`表达式后面跟的是一个遍历器对象，需要在`yield`表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为`yield*`表达式。

```JavaScript
let delegatedIterator = (function* () {
  yield 'Hello!';
  yield 'Bye!';
}());

let delegatingIterator = (function* () {
  yield 'Greetings!';
  yield* delegatedIterator;
  yield 'Ok, bye.';
}());

for(let value of delegatingIterator) {
  console.log(value);
}
// "Greetings!
// "Hello!"
// "Bye!"
// "Ok, bye."
```

实际上，任何数据结构只要有 Iterator 接口，就可以被`yield*`遍历：

```JavaScript
let read = (function* () {
  yield 'hello';
  yield* 'hello';
})();

read.next().value // "hello"
read.next().value // "h"
```

一个复杂的例子：

```JavaScript
function * iterTree(tree){
  if(Array.isArray(tree)){
     for(var x of tree){
       yield* iterTree(x);
     }
  }else{
     yield tree;
  }
}

var tree = [1, [2, 3], [4, [5, 6]]]；
[...iterTree(tree)];//Array(6) [ 1, 2, 3, 4, 5, 6 ]
```

yield完全遍历二叉树：





### 作为对象属性的 Generator 函数

如果一个对象的属性是 Generator 函数，可以简写成下面的形式。

```JavaScript
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
```



### 含义

#### 状态机



#### 协程



### 应用

#### 异步操作的同步化表达：



## 十五、generator函数的异步应用

### 异步编程的传统方法

回调函数

事件监听

发布订阅/观察者模式

Promise对象



### generator管理异步

使用generator函数管理异步操作最大的问题就是：什么时候调用next()？也就是说怎么知道上一个异步任务完成了？

#### thunk函数

编程语言的函数参数何时求值？有传值调用和传名调用的区别。

传值调用是在进入函数体前，计算参数的值；

传名调用是直接将参数塞入函数体，等用到的时候求值；

编译器传名调用的实现，通常是把参数放入一个临时函数中，再将临时函数放入函数体，这个临时函数就叫thunk函数：

```JavaScript
function f(m){
    return m * 2;
}
f(x + 1);
//等同于
var thunk = function(){
    return x + 1;
}
function f(thunk){
    return thunk() * 2;
}
```



然而，JavaScript是传值调用。Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只接受回调函数作为参数的单参数函数：

```JavaScript
// 正常版本的readFile（多参数版本）
fs.readFile(fileName, callback);

// Thunk版本的readFile（单参数版本）
var Thunk = function (fileName) {
  return function (callback) {
    return fs.readFile(fileName, callback);
  };
};

var readFileThunk = Thunk(fileName);
readFileThunk(callback);
```

参数有回调函数的函数，都能写成Thunk函数的形式：

。。。

有点绕，先不研究Thunk。



#### Promise对象

总之，就是需要一种机制，在异步操作完成时，自动进入下一步。Promise对象可以做到这一点。

```JavaScript
var fs = reuqire('fs');
var readFile = function(fileName){
  return new Promise(function(resolve, reject){
    fs.readFile(fileName, function(err, data){
      if(err){reject(err)}
      resolve(data);
    });
  });
}

function gen = function * (){
  var f1 = yield readFile('path/to/file1');//第一次next执行到这里
  console.log(f1);//在第二次next调用后执行，因为是在then中执行的next，所以第一次异步操作已经完成了。
  var f2 = yield readFile('path/to/file2');//第二次next执行到这里
  console.log(f2);
}
//手动调用
var it = gen();
it.next().value.then(function(data){
  console.log(data);
  it.next().value.then(function(data){
    console.log(data);
  });
});
//自动执行
function run(gen){
  var it = gen();
  function next(data){
    var result = it.next(data);//第一次调用next传data没有效果。目的是执行异步任务。
    if(result.done){return result.value;}
    result.value.then(function(data){//异步操作执行完，进行后续任务
      next(data);
    });
  }
  next();
}
run(gen);
```

## 十六、async函数

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

async 函数是什么？一句话，它就是 Generator 函数的语法糖。如果把上面包含异步操作的遍历器函数写成async函数，就是：

```JavaScript
var fs = reuqire('fs');
var readFile = function(fileName){
  return new Promise(function(resolve, reject){
    fs.readFile(fileName, function(err, data){
      if(err){reject(err)}
      resolve(data);
    });
  });
}

async function asyncReadFile(){
    var f1 = await readFile('path/to/file1');
    var f2 = await readFile('path/to/file2');
}
//注意这里省去了手动调用或编写执行器的环节。
```

`async`函数把generator函数的`*`变成了`async`，`yield`变成了`await`。

改进体现在:

- 内置执行器。

  Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

  ```
  asyncReadFile();
  ```

  上面的代码调用了`asyncReadFile`函数，然后它就会自动执行，输出最后结果。这完全不像 Generator 函数，需要调用`next`方法，或者用`co`模块，才能真正自动执行，得到最后结果。

- 更好的语义。

  `async`和`await`，比起星号和`yield`，语义更清楚了。

  `async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

- `await`表达式有返回值了！

  相比于`yield`表达式要通过next()注入值，如果await后的Promise对象正常处理(fulfilled)，其回调的resolve函数参数作为 await 表达式的值，若 Promise 处理异常(rejected)，await 表达式会把 Promise 的异常原因抛出。


- 更广的适用性。

  `co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

- 返回值是 Promise。

  `async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。

### 最好的理解

返回的[Promise](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)对象会以async function的返回值进行resolve，或者以该函数抛出的异常进行reject。

如果await后的Promise对象正常处理(fulfilled)，其回调的resolve函数参数作为 await 表达式的值，若 Promise 处理异常(rejected)，await 表达式会把 Promise 的异常原因抛出。

### 基本用法

```JavaScript
function timeout(ms) {
  return new Promise(function (res, rej) {
    setTimeout(res, ms);
  });
}

async function asyncRun(){
  console.log('hello');
  var f = await timeout(2000);
  console.log('异步完成！',f);
}

asyncRun();
console.log('猜猜运行结果');
```

async函数返回一个promise对象。必须等到所有内部异步操作执行完，才会才会发生状态改变，除非遇到return语句或抛出错误。return的值会成为then方法的参数。

只要一个`await`语句后面的 Promise 变为`reject`，那么整个`async`函数都会中断执行。

```JavaScript
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}
```

如果想不中断，就得用`try…catch`了。

```JavaScript
async function f() {
  try{
    await Promise.reject('出错了');//可以没有return
  }catch(e){
//     throw '第一阶段有错！';
  }
  return await new Promise(reslove=>{//必须有return，then才能拿到reslove的参数。
    setTimeout(function(){
      console.log('后续操作被执行了')
      reslove(111);
    }, 2000);
  });
}
f()
  .then(v=>{console.log(v)})
  .catch(e=>{console.log(e)})
```

但是：

- 如果抛出错误，错误可以被`.catch()`捕捉，然而会导致promise变为reject，后续await不会执行；
- 如果不设`try…catch`，也是抛出错误，那么`reject`的参数会被外部catch捕捉，然而Promise变为reject，后续await也不会执行；
- 如果不抛出错误，后续await会执行，但不会`reject`。（但是只有这样才能保证后续await执行，错误可以在async内部处理。）



### 最佳实践

有没有既能够在外部处理错误，又能保证后续await执行？貌似没有。。。

试了下比较好的处理方式是直接在`async`内部捕捉可能出现的错误：

```JavaScript
//写法1，不用try...catch，用Promise的catch
async function f() {
  await Promise.reject('出错了').catch(e=>console.log(e)); //直接捕捉
  await Promise.reject('又出错了').catch(e=>console.log(e)); //直接捕捉
  return await new Promise(res=>{
    setTimeout(function(){
      console.log('后续操作被执行了')
      res(111);
    }, 2000);
  });
}
f()
  .then(v=>{console.log(v)})
  .catch(e=>{console.log(e)})//不会捕捉第二次了。
```



```JavaScript
//方法2，用try...catch
async function f() {
  try{
    await Promise.reject('出错了');//可以没有return
  }catch(e){
	console.log(e)
  }
    
  try{
    await Promise.reject('出错了');//可以没有return
  }catch(e){
	console.log(e)
  }
    
  return await new Promise(reslove=>{//必须有return，then才能拿到reslove的参数。
    setTimeout(function(){
      console.log('后续操作被执行了')
      reslove(111);
    }, 2000);
  });
}
f()
  .then(v=>{console.log(v)})
  .catch(e=>{console.log(e)})//不会捕捉第二次了。
```

这样错误能被内部处理，后续await也能执行。

### 并发与继发的异步

并发的异步任务可以这样写：

```JavaScript
function getFoo(){
  return new Promise((res, rej)=>{
    setTimeout(()=>res('2s'), 2000);
  });
}
function getBar(){
  return new Promise((res, rej)=>{
    setTimeout(()=>res('3s'), 3000);
  });
}
 
async function testInComplete(promises){
  promises.map(async promise => {
    console.log(await promise);
  });
}
testInComplete([getBar(), getFoo()]);//按完成顺序

async function testInOrder(promises) {
  for(var promise of promises){
    console.log(await promise);
  }
  //或者
  console.log( await Promise.all(promises) );
}
testInOrder([getBar(), getFoo()]);//按参数顺序

```

复杂的例子：

```JavaScript
//继发

async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}
logInOrder([1, 2, 3])；
```

```JavaScript
//并发 按参数顺序

async function logInOrder(urls) {
  // 并发读取远程URL
  const promises = urls.map( url=>{return fetch(url)} );
  var result = await Promise.all(promises);//Promise.all()的参数数组的排序，决定了返回的promise对象的resolve参数数组顺序，而不是由执行完毕的顺序决定。
  for (let textPromise of result) {
    console.log(await textPromise.text());//textPromise.text()返回的是promise对象，await方便取值，也是等待完成
  }
}
logInOrder([1,2,3]);
```

```javascript
//并发 按异步操作完成顺序

async function logInOrder(urls) {//这里的async不必要
  // 并发读取远程URL
  urls.map(async url => {
    const response = await fetch(url);
    console.log( await response.text() );
  });
}
logInOrder([1, 2, 3]);
```



## 十七、class语法

类的所有方法都定义在类的`prototype`属性上面。而且是不可枚举的。

contructor方法必须有。new调用的就是它。

类，必须用new执行。

class声明的变量不提升，与let和const一样。

### 私有方法与私有属性



### this的指向

指向类的实例。

### Class的存值函数set和取值函数get

在类的内部，可以使用存值函数和取值函数，拦截某个属性的存取行为，注意，这个属性会被定义在prototype上。

### Class的静态方法和静态属性

静态方法和静态属性，是类的方法和属性，而不是类实例的。

静态方法的定义，可以用static关键字：

```JavaScript
class F{
  static say(){
    return '123';
  }
}
//等同于
F.say = function(){
    //...
}
```

静态属性的定义，目前只能这样写：

```JavaScript
F.prop = propValue
```

静态方法中，this指向类本身。父类的静态方法，可以被子类继承。

```JavaScript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

静态方法可以在super对象上调用：

```JavaScript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```



### new.target属性

new.target是ES6新增的属性，指代被new的那个构造函数。

如果构造函数不是通过new调用的，new.target会返回undefined。因此这个属性可以用来确定构造函数是怎么调用的。

new.target只能用在函数内部，否则报错。

子类继承父类时，new.target会返回子类。

利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。

```JavaScript
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}

var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

上面代码中，`Shape`类不能被实例化，只能用于继承。



## 十八、Class的继承

Class使用extends关键字实现继承：

```JavaScript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

定义在父类上的静态属性和静态方法也会被继承。

虽然Class语法是ES5原型继承的语法糖，但是实现还是略有不同。子类必须先在constructor函数中调用super()，通过父类的构造函数，得到与父类同样的实力属性和实例方法（this），然后对其加工，加上子类的实例属性和方法。如果不调用super方法，子类就得不到this对象。

ES5的继承实际上是先构造子类的实例对象this，然后再将父类的实力属性和方法添加到这个this上。一句话说，ES6的继承是子类修改父类this，ES5的继承是父类修改子类this。

### Object.getPrototypeOf()

`Object.getPrototypeOf`方法可以用来从子类上获取父类。

```javascript
Object.getPrototypeOf(ColorPoint) === Point
// true
```

因此，可以使用这个方法判断，一个类是否继承了另一个类。

### super关键字

super可以当对象使用，也可以当做函数使用，但是不能单独使用。

MDN上提供了两种语法：

```javascript
super([arguments]); 
// 调用 父对象/父类 的构造函数

super.functionOnParent([arguments]); 
// 调用 父对象/父类 上的方法
```



- 当作为函数使用时，代表父类的构造函数，只能用在子类的构造函数中。注意，`super`虽然代表了父类`A`的构造函数，但是返回的是子类`B`的实例，即`super`内部的`this`指的是`B`，因此`super()`在这里相当于`A.prototype.constructor.call(this)`。

```JavaScript
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

- 当作为对象使用时：

  - 在普通方法中，super指向父类的原型对象；但是，我所不理解的一点是，super.prop这种语法，赋值的时候相当于赋值到了this上，取值的时候却是在父类的原型对象上取。在子类普通方法中通过super调用父类的方法时，方法中的this指向当前的子类实例；

    （总结一下：假设有父类A，子类B继承自A。在子类B的普通方法中使用super时，赋值语句`super.prop=xxx`等同于`this.prop=xxx`，取值语句`super.prop`等同于`A.prototype.prop`）

  - 在静态方法中，指向父类。在子类的静态方法中通过super调用父类的静态方法时，方法内部的this指向子类。

### 类的prototype属性和\__proto__属性

一般的对象，只有\__proto__属性，指向构造函数的prototype属性。

而任意一个函数，也是对象，因此构造函数有prototype属性，也有\__proto__属性。

- 子类的\__proto__属性，表示构造函数的继承，总是指向父类。
- 子类的prototype的\__proto__属性，表示方法的继承，总是指向父类的prototype属性。

```JavaScript
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

上面代码中，子类`B`的`__proto__`属性指向父类`A`，子类`B`的`prototype`属性的`__proto__`属性指向父类`A`的`prototype`属性。



### 原生构造函数的继承

ES5不可以继承原生构造函数，而ES6可以了。



## 十九、Module语法

### 概述+优势

在ES6以前，模块加载方案主要有commonJS和AMD，前者用于服务器，后者用于浏览器。

ES6的模块设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。commonjs和AMD只能在运行时确定这些。

```JavaScript
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

上面代码的实质是整体加载`fs`模块（即加载`fs`的所有方法），生成一个对象（`_fs`），然后再从这个对象上面读取 3 个方法。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

ES6 模块不是对象，而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。

```JavaScript
// ES6模块
import { stat, exists, readFile } from 'fs';
```

上面代码的实质是从`fs`模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。



### 严格模式

ES6的模块自动采用严格模式。



### export命令

模块功能主要由export和import命令构成。export规定对外输出的接口，import输入其他模块提供的功能。

一个模块就是独立的文件，文件内部的所有变量，外部无法获取。只有export命令输出的变量才可供外部读取，写法只有两种：

```JavaScript
//写法1
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;

//写法2(推荐)
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

默认情况下，输出的变量就是本来的名字，但是可以用`as`重命名：

```JavaScript
function v1() { ... }
function v2() { ... }

export {v1 as streamV1, v2 as streamV2,v2 as streamLatestVersion};
//export {原名字 as 新名字};
```

另外，`export`语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

```JavaScript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```

上面代码输出变量`foo`，值为`bar`，500 毫秒之后变成`baz`。

这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是值的缓存，不存在动态更新。



### import命令

使用export定义了对外接口后，其他js文件就可以用import命令加载这个模块：

```JavaScript
// main.js
import {firstName, lastName, year} from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName; 
}
```

上面的代码：import后面要跟大括号，大括号里面的变量名必须与模块`profile.js`export导出的名称相同。from后面是模块路径，可以是绝对路径，也可以是相对路径。模块的`.js`后缀可以省略。但如果只跟模块名而不写路径，那么必须有配置文件，告诉js引擎模块的位置。

如果想为导入的变量重新命名，也可用`as`关键字：

```JavaScript
import { lastName as surname } from './profile.js';
```

一般情况下，我们都是在文件开头写import，其实可以写在顶层的任意位置，因为import是在编译阶段就执行的（静态执行，会提升到整个模块的头部，首先执行），也因为静态执行，所以import所处的环境和自身的语句，都不能带有表达式。

```JavaScript
foo();
import { foo } from 'my_module';
//OK
```

```javascript
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

import导入的接口是只读的，不能被重新赋值，但是如果导入的是对象，是可以修改属性的。最好不要这么干。

### 模块的整体加载

除了指定加载某个输出值，还可以使用整体加载：

```JavaScript
// 指定分散加载
import {firstName, lastName, year} from './profile.js';

//整体加载
import * as profile from './profile.js';
console.log(profile.firstName);

```

注意整体加载是没有大括号的。上述代码用星号代表`profile.js`模块输出的全部接口，然后把星号重命名为`profile`对象，全部接口变为了profile对象的属性或方法。对这个对象进行属性添加或修改都是不允许的：

```JavaScript
import * as profile from './profile.js';
profile.foo = '123';//error
profile.firstName = '123';//error
```

**整体加载貌似是不加载默认接口的，以后试试。**



### export default

懒人福音！从上面可以看出，如果想引入模块，必须知道模块所导出的接口名字，不然无法使用。如果我不想知道导出的名字，自己随便指定一个名字就可以使用呢？只要在导出模块的时候，设置一下默认输出的接口就行了：

```JavaScript
// export-default.js
export default function () {
  console.log('foo');
}
```

上面这个模块的默认输出是一个函数，其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字。

```JavaScript
// import-default.js
import customName from './export-default';//注意引入默认接口时没有加大括号。
customName(); // 'foo'
```

上面代码的`import`命令，可以用任意名称指向`export-default.js`输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是，这时`import`命令后面，不使用大括号。

export default用在非匿名函数前也是可以的，只不过函数名字会被模块外部所忽略：

```JavaScript
// export-default.js
export default function foo() {
  console.log('foo');
}
// 或者写成
function foo() {
  console.log('foo');
}
export default foo;
```

`export default`命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此`export default`命令只能使用一次。所以，import命令后面才不用加大括号，因为只可能唯一对应`export default`命令。

本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的。

```JavaScript
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as foo } from 'modules';
// 等同于
// import foo from 'modules';
```

正是因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句。

```JavaScript
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```

上面代码中，`export default a`的含义是将变量`a`的值赋给变量`default`。所以，最后一种写法会报错。

同样地，因为`export default`命令的本质是将后面的值，赋给`default`变量，所以可以直接将一个值写在`export default`之后。

```JavaScript
// 正确
export default 42;

// 报错
export 42;
```

上面代码中，后一句报错是因为没有指定对外的接口，而前一句指定外对接口为`default`。

如果一个模块既有默认接口，又有具名接口呢？如下所示：

```JavaScript
export default function (obj) {
  // ···第一个，默认接口(匿名接口)
}
export function each(obj, iterator, context) {
  // ···第二个，具名接口
}
export { each as forEach };//第三个，重命名接口，实际上也是each接口，不过该模块外部只认forEach。
```

