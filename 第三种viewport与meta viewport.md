# 第三种viewport和`meta viewport`

**本文包含了作者对`<meta viewport>`不同取值的测试。**



## `meta viewport`标签

`meta viewport`标签包含了对浏览器视口和缩放行为的指导，特别要指出的是，它允许开发人员指定布局视口的大小，而布局视口与CSS的计算有关，比如 `width: 20%`就是根据布局视口宽度计算的。

`meta viewport`语法如下：

```html
<meta name="viewport" content="name=value,name=value">
```

### “命令”

每一个name/value对儿都是一个“命令”（这个词是作者发明的），总共有六个：

| 属性名          | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| `width`         | 设置布局视口的宽度                                           |
| `height`        | 应当设置布局视口的高度，但支持度不太好                       |
| `initial-scale` | 设置页面的初始缩放**并且**设置布局视口的宽度                 |
| `minimum-scale` | 设置最小缩放层级（也就是用户可以缩小页面的极限）             |
| `maximum-scale` | 设置最大缩放层级（也就是用户可以放大页面的极限）             |
| `user-scalable` | 设置为`no`可以阻止用户缩放。这是令人憎恶的行为，坚决不能用（译者：？？？） |

### device-width

`width`有个特殊取值：`device-width`，它可以把布局视口的宽度设置为理想视口的宽度。

## 三种视口

很久以前我记录过——手机有两种视口：可见视口和布局视口。

[两种viewport的叙述——第一部分](https://github.com/shaoweilee/blog/blob/master/%E4%B8%A4%E7%A7%8Dviewport%E7%9A%84%E5%8F%99%E8%BF%B0%E2%80%94%E2%80%94%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86.md)

[两种viewport的叙述——第二部分](https://github.com/shaoweilee/blog/blob/master/%E4%B8%A4%E7%A7%8Dviewport%E7%9A%84%E5%8F%99%E8%BF%B0%E2%80%94%E2%80%94%E7%AC%AC%E4%BA%8C%E9%83%A8%E5%88%86.md)

有必要的话请再读一遍，下面的内容会用到关于这两种视口的知识。

### 理想视口

事实证明还有第三种视口，我称之为“**理想视口**”，它给出了页面在设备上的理想大小。因此，对不同的设备来说，理想视口大小都是不同的。

在老旧的、非retina屏的手机上，理想视口的大小等于物理像素数，但不是必备条件。新手机有更高的屏幕像素密度，也能继续保留旧的理想视口，因为理想情况下，它真的适合设备。

直到iPhone4s，不管是不是retina屏，理想视口都是320x480，因为对那些爱疯来说，320x480都是网页理想的尺寸。

关于理想视口，这两点非常重要：

1. 用`width=device-width`和`initial-scale=1`，布局视口可被设为理想视口的值。
2. 所有的scale，都以理想视口为基准，无论布局视口宽度是多少。因此，`maximum-scale=3`就意味着最大缩放是理想视口的三倍。

### 找出理想视口的大小

有时候搞明白理想视口的大小很有用。真倒霉。（译者：？？？哪里倒霉啦）

给页面这样的meta标签：

```html
<meta name="viewport" content="width=device-width,initial-scale=1">
```

然后大声读出这俩值！`document.documentElement.clientWidth/clientHeight`。

如果它不行，那就没办法获取理想视口大小了。我期望 `screen.width/height`有用，但只有黑莓手机给了正确的信息。（译注：现在`screen.width/height`貌似都可以返回理想视口的大小了）

开放问题：`screen.width/height`应当给出理想视口的大小吗？

正方观点：这俩属性至少给个有用的信息吧。

反方观点：理想视口的大小不用非得等于设备的物理像素数。

### 能力测试——理想视口

[请点击链接查看原文表格](https://www.quirksmode.org/mobile/metaviewport/#link7)。

### 布局视口宽度

在渲染页面之前，浏览器需要知道布局视口有多宽，这是CSS计算的依据，比如：`width: 20%`。

如果没有给定的指示，那么浏览器就会自己定值。在八款测试的浏览器中，有六种是980px，黑莓和IE10是1024px。并没有对错之分，只是浏览器厂商作出的选择。

当在meta viewport标签中使用 `width=400`或者其他的数字值，布局视口宽度就被设为那个值，这个我们都知道。

然而，安卓Webkit和IE的最小视口是320px。如果设为小于320px的值，视口宽度就会自动变成理想视口的宽度。

这就是布局视口变成理想视口的情况。当设置 `width=device-width`或者 `initial-scale=1`，也会这样。

### 最小和最大尺寸

布局视口最大宽度为10000px。我并不完全相信这个值，因为浏览器并不允许缩放到那么大。到目前为止，我认同这个官方值。

布局视口最小宽度为理想视口的1/10，这也是最小的缩放级别。（也就是说，布局视口永远不会比最小的可见视口还要小。）特例：安卓Webkit和IE，不会低于320px。

### 能力测试——布局视口

[请点击链接查看原文表格。](https://www.quirksmode.org/mobile/metaviewport/#link10)

## 缩放

缩放很棘手。听起来很简单：确定用户可以放大或缩小的缩放系数。问题有两个：

1. 不能直接辨别缩放系数。我们必须知道可见视口的宽度，它是和缩放系数成反比的。缩放系数越大，可见视口越小。
2. 事实表明，所有的缩放系数都与理想视口有关，无论当前的布局视口有多大。

还有关于名称的问题。按苹果的说法，zoom就是scale，这就是为啥meta viewport有这样的名字：`initial-scale`，`minimum-scale`， `maximum-scale`。其他浏览器为了保持兼容性，被迫遵照这个约定。

`initial-scale`，`minimum-scale`， `maximum-scale`都接收一个缩放系数，例如设为2则意味着“缩放为理想视口宽度的200%”。（译注：看怎么理解了，我认为是把每个CSS像素都缩放为原来的200%，意味着CSS像素更大，导致可见视口变小了。）

### 公式

我们首先定义公式：

```
可视视口宽度=理想视口宽度 / 缩放系数
缩放系数=理想视口宽度 / 可视视口宽度
```

因此，如果理想视口是320px宽，缩放系数是2，那么可见视口宽度就是160px。

### 最小和最大缩放系数

这部分没翻译明白……[原文在此](https://www.quirksmode.org/mobile/metaviewport/#link13)。



## initial-scale

设置`initial-scale`实际上做了两件事：

1. 设置页面的初始缩放系数，页面可以根据这个值，相对于理想视口，产生可见视口的宽度；
2. 设置布局视口宽度为刚刚计算出的可见视口的宽度。

所以，假如我们有个竖屏的iPhone，为它设置了`initial-scale=2`，并且没有其他值了。有了前面的铺垫，当你看到它的可见视口宽为160px（=320/2）时，也应该惊不到你了。这就是缩放命令的工作方式。

然而，它也会设置布局视口的宽度为160px，所以，现在我们的页面在最小缩放级别下就是160px宽了。（可见视口不能比布局视口更大，所以不能再缩小页面了）

### 浏览器BUG

只有Android Webkit，很明显。在不使用`width`的前提下，Android Webkit只有在 `initial-scale=1`时才会设置布局视口的宽度。所以如果没有其他指令（就是说的`width`），只有`initial-scale=1`起效。

（译注：根据下面的能力测试补充，Android Webkit，把`initial-scale`设为`1`以外的其他值时，页面正确地应用缩放，但是不管布局视口大小，也就是布局视口会使用默认值。）

IE也有BUG，它应用一个错误的理想视口（320x320而不是320x480），并且也假装值都是1。所以在IE中，设置`initial-scale`并不重要。

### 冲突的宽度指令

既然`initial-scale`设置了布局视口的宽度，那么你可以创建一个有冲突的指令了：

```html
<meta name="viewport" content="initial-scale=1,width=400">
```

会发生什么？浏览器得到了有冲突的指令。让我们再回到iphone4s：

1. `initial-scale=1`告诉浏览器，竖屏时把布局视口的宽度设为320px，横屏时设为480px。
2. `width=400`告诉浏览器，横竖屏时把布局视口宽度都设为400px。

浏览器解决这个问题的方式是：横屏或竖屏，遵循那个大的值。在我们的例子中，竖屏时，布局视口宽为400px（320和400中取大值），横屏时，布局视口宽度变为480px（480和400中取大值）。

有意义吗？实际上没有，但是浏览器无论如何还是这么做了。

总之，我们得到了布局视口的min-width。上面的meta标签设置了min-width为400px，但是允许浏览器在设备尺寸或者设备方向需要的时候，增加布局视口的大小，使其超过min-width的值。

我不确定为布局视口设置min-width是否有实际用处，但是如果你需要，嘿，这里有！

### 浏览器BUG

Android Webkit不遵从上面的规定。如果`width`等于`device-width`或者小于320，Android Webkit总是把布局视口宽设为理想视口宽（译注：译者没经过实际测试，因为查看了下安卓手机上的浏览器，都是AppleWebkit……）。超过320的话，总是遵从`width`的值。

### 能力测试——`initial-scale`和`width`

表格太大了……[请看原文](https://www.quirksmode.org/mobile/metaviewport/#link19)……

（译注：后来安卓手机上也是用AppleWebkit了，但是貌似保留了AndroidWebkit的那个BUG：只使用`initial-scale`时，值为`1`才会设布局视口为理想视口大小，对于其他值，只缩放，不设宽。）

（译注：就到这里了，原文还有对minimum-scale和maximum-scale的测试，结果是除了Android Webkit和IE上有BUG外，其余表现很正常。我觉得这部分用处不大，就没有翻译……）





























