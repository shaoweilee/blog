# ESLint每日一讲系列

（无授权偷偷搬运2333）

来自吕大豹的分享，感兴趣的同学，可以加入他的知识星球，可以学到不少。

## 2018.7.11

`prefer-const` 

**含义**：始终用const定义你从不修改的变量，如果会被修改则使用let，永不使用var。 

**理由**：让你写代码的时候更明确哪些变量是会被修改的，哪些是不会修改的。帮你理清逻辑，减少bug。

## 2018.7.12

`no-array-constructor`

**含义**： 不要使用Array构造器去创建数组，总是使用字面量。 

**理由**： 当给Array构造器传一个参数时可能造成混淆，比如 new Array(3)，它表示什么呢？ 

-  `[undefined, undefined, undefined]`；
- `[3]`

你可能在这两种结果中犹豫，另外一个理由就是，Array构造器可能被改写，有不可预知的风险。

## 2018.7.13

`no-prototype-builtins` 

**含义**： 禁止直接调用Object原型上的方法，如foo.hasOwnProperty('name'). 应该换用Object.prototype.hasOwnProperty.call(foo, 'name') 

**理由**：Object.create方法允许给新创建的对象指定原型，意味着对象的原型可能是不明确的。如果用Object.create(null)创建了无原型的对象，再调用Object原型上的方法就报错了。

PS：我以前还用过({}).toString.call(…)这样的写法，觉得自己屌爆了，现在被ESLint教做人了哈哈。

## 2018.7.14

`func-style` 

**含义**：用于指定定义函数的方式，有两个值：expression--表达式，declaration--函数声明 

**理由**：用函数声名的方式定义的函数，会进行变量提升，有可能给人带来理解上的困惑，而表达式定义的不会。为了统一代码风格，最好确定一种函数定义风格。

## 2018.7.15

`prefer-rest-params` 

**含义**： 建议用剩余参数语法function f(a, b, ...args)来动态获取函数参数，而不是使用arguments 

**理由**： 

1. 剩余参数得到的是真正的数组，而arguments是类数组，有时候还需要再转化一步。 
2. 剩余参数的语义更明确，即声明的形参之外的实参会被归进数组。