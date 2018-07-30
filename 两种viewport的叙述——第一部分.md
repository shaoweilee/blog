# 两种viewport的叙述——第一部分

[TOC]

**[原文链接](https://www.quirksmode.org/mobile/viewports.html)**

在这个小系列中，我会阐述视口和几种重要元素的宽度，像\<html>元素，window，screen。

这篇文章是关于桌面浏览器的，写这篇文章的目的是为讨论移动浏览器做铺垫。许多前端开发者已经对大多数桌面浏览器概念有了直观的理解。在移动端，会有一些概念相当复杂。

## 概念：设备像素（device pixels）和CSS像素（CSS pixels）。

你需要理解的第一个概念是CSS像素，还有它和设备像素的不同。

设备像素，是那种直观上认为是“正确的，恰当的”像素。设备像素提供了分辨率，一般可以通过screen.width/screen.height获得。

如果你给一个元素设置了`width: 128px`，然后你的显示器是1024px宽，然后最大化浏览器窗口，那么8个这样的元素就塞满了（先忽略复杂的情况）。

用户一旦缩放，结果就变了。如果用户缩放至200%，只需要四个`width: 128px`的元素就能塞满显示器了。

在现代浏览器中实现的缩放，只不过是“拉伸”像素而已。 也就是说，元素的宽度并没有从128像素变为256像素，而是**实际的像素**被乘了2。正式来说，元素仍然是128个CSS像素宽，尽管它占用了256个设备像素的空间。

换句话说，缩放到200%使得一个CSS像素变成了一个设备像素的四倍大小（两倍宽，两倍高，总共四倍）。

上个图就明白了。这是100%缩放层级下的四个像素（也就是不缩放）。CSS像素和设备像素完全重叠：

![img](https://www.quirksmode.org/mobile/pix/viewport/csspixels_100.gif) 

现在，缩小！CSS像素开始缩了，意味着一个设备像素现在可以覆盖好几个CSS像素了：

![img](https://www.quirksmode.org/mobile/pix/viewport/csspixels_out.gif) 

如果放大，就会发生相反的情况。CSS像素开始变大，意味着一个CSS像素可以覆盖好几个设备像素了：

![img](https://www.quirksmode.org/mobile/pix/viewport/csspixels_in.gif) 

这部分重点在于，你只需要对CSS像素感兴趣就行了。它才是支配你CSS如何渲染的像素。

设备像素对你来说几乎没啥用。对用户来说就不是了，用户会缩放页面，直到能够舒适地阅读。然而，你不用管缩放层级，浏览器会自动拉伸或者挤压你的CSS布局。

### 100%缩放

我用这个例子来假设100%缩放层级。是时候给个更严格的定义了：

**100%缩放层级就是，一个CSS像素精确地等于一个设备像素。**

100%缩放层级的概念对接下来的阐述相当重要。但在日常工作中不必太担心。在桌面浏览器上，你一般只在100%缩放下测试站点，但是即便用户缩放页面，神奇的CSS像素也会保持住你的布局比例。

## 屏幕尺寸

看一下一些实际的测量值。从`screen.width`和`screen.height`开始。这俩值是用户屏幕的总大小，而且是用设备像素来测量的，因为它们不会变：它们是显示器的特性，而不是浏览器的。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_screen.jpg) 

因吹斯听！这个信息对我们来说有啥用？

基本没啥用。用户显示器的大小对咱们不重要——除非你想做个统计数据。

## 窗口尺寸

你真正想知道的是，浏览器窗口的尺寸。这个信息说明了，用户有多少空间供你的CSS布局使用。你可以通过`window.innerWidth`和`window.innerHeight`得到这个信息。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_inner.jpg) 

显然，窗口尺寸用的是CSS像素测量的。你需要知道多大的布局可以被塞进浏览器窗口，还要知道如果用户放大的话，会被裁剪多少。所以，如果用户放大，窗口中的可用空间就会减少，`window.innerwidth`和`window.innerHeight`也会体现这一点。

（Opera浏览器是个例外，window.innerWidth和window.innerHeight是用设备像素测量的，用户放大的话也不会减少。这在桌面浏览器上很恼人，手机上也是个致命的灾难。一会儿再看这个问题。）

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_inner_zoomed.jpg) 

**注意，测量出来的宽高是包含了滚动条的。**滚动条也被认为是窗口内的一部分。（大概是历史原因吧。）

## 滚动偏移

`window.pageXOffset`和`window.pageYOffset`，包含了document的水平和垂直滚动偏移量。因此可以知道用户滚动了多少。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_page.jpg) 

这俩属性也是用CSS像素测量的。毕竟你想知道的是文档被向上滚动了多少，无论缩放状态是怎么样的。

理论上来说，如果用户向上滚动了页面然后又放大了，window.pageXOffset/window.pageYOffset会变。然而，当用户缩放时，浏览器会使页面可见区顶部的那个元素不变，来尽量保持页面不变。虽然不总是完美呈现，但这意味着实际上window.pageXOffset/window.pageYOffset值不变：被滚上去的CSS像素数保持不变。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_page_zoomed.jpg) 

## 概念：视口（viewport）

在进行更多JavaScript属性的讨论前，不得不先引入另一个概念：视口。

视口的功能是约束`<html>`元素，也就是页面最顶级的包含块。

这么说可能有点含糊，所以还是上实例吧。假设你有一个流体布局，其中一个sidebar设置了`width: 10%`。现在，当你重设浏览器窗口大小的时候，这个sidebar也会伸缩。但是，它到底是怎么工作的？

技术层面上讲，sidebar是它父级宽度的10%，这个父级，我们就指定为`<body>`吧（不给它设width）。所以，问题就变成了，`<body>`用的是谁的宽度？

正常来讲，所有块级元素占用父级100%的宽（有例外，但先忽略吧）。所以`<body>`和父级一样宽，也就是`<html>`元素。

那么， `<html>`元素多么宽？它和浏览器窗口一样宽。这就是为啥你设了`width: 10%`的那个sidebar占用了整个浏览器窗口的10%的宽度，这个人人都会用。

但是你可能不知道的是它理论上是怎么工作的。理论上来说，html元素被约束在视口（viewport）的宽度中。html元素占用100%视口的宽度。

视口，反过来讲，精确等于浏览器窗口：就是这样被定义的！视口不是HTML结构，所以你并不能通过CSS影响它。它只有浏览器窗口的宽高——在桌面浏览器上。在移动设备上，情况有点小复杂。

### 后果

这种事实造就了有意思的后果。就在[这个页面](https://www.quirksmode.org/mobile/viewports.html)上，就可以看到其中一个。滚到顶，然后放大两三倍，可以看到这部分内容就溢出了浏览器窗口。

现在往右滚动，就会看到这个蓝条就对不齐了。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_htmlbehaviour.jpg) 

这个表现，就是视口被那样定义的后果（视口精确等于浏览器窗口）。我给了那个蓝条`width: 100%`，什么的100%？答案是`<html>`元素的100%，也就是和视口一样宽，也就是和浏览器窗口一样宽。

重点在于：明明在100%缩放下表现很好，现在我们放大了，视口变得比网站总宽度小了，蓝条本身没啥问题，只是内容溢出了`<html>`元素，但是那个元素（蓝条）有`overflow: visible`，意味着溢出的内容还是会显示。

但是蓝条却没有溢出，毕竟我给了它`width: 100%`，浏览器严格执行，就把视口的宽度赋予了蓝条。浏览器才不管现在宽度是不是太窄呢！

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_100percent.jpg) 

### 文档宽度？（document width）

我真正需要知道的是，页面到底有多宽？包括伸展出的那部分（上面溢出的那部分）。据我所知，找是不可能找出这个值的（除非你计算每个元素的宽度，边距，但是这样容易出错）。

所以，我坚信，要通过JavaScript属性得到这个宽度！我称之为“文档宽度（document width）”，当然啦，以CSS像素计。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_documentwidth.jpg) 

更进一步，为啥不把这个值暴露给CSS？那样我就可以让`width: 100%`的蓝条依赖于整个文档的宽度，而不是`<html>`的宽度了。（注定难办，实现不出来我也不惊讶。）

浏览器厂商，你们咋想？

## 测量视口

你也许想知道视口的宽度。可以通过`document.documentElement.clientWidth` 和`document.documentElement.clientHeight`得到。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_client.jpg) 

如果你了解DOM，就会知道`document.documentElement`实际上是`<html>`元素：所有HTML元素的根元素。然而，视口比它高一级，可以这么说：视口就是包含`<html>`的那个元素。如果你给`<html>`元素一个宽度，影响会很大。（不建议这么做，虽然这是可行的。）

这种情况下，`document.documentElement.clientWidth` 和`document.documentElement.clientHeight` 仍然给出了视口的尺寸，而不是 `<html>`的尺寸。 

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_client_smallpage.jpg) 

所以`document.documentElement.clientWidth` 和`document.documentElement.clientHeight`总是给出视口的尺寸，不管`<html>`的尺寸。

### 两个属性对儿

等等，视口的尺寸，刚才`window.innerWidth`和`window.innerHeight`不是已经给过了吗？呃，是，也不是。

正式的区别就是， **`document.documentElement.clientWidth/clientHeight` 不包含滚动条，`window.innerWidth/innerHeight`包含滚动条。**

这两对儿属性，其实是浏览器大战的残留，那个时候哇，Netscape只支持`window.innerWidth/Height`，IE只支持 `document.documentElement.clientWidth/clientHeight` 。从那时候起，所有其他浏览器开始支持`clientWidth/clientHeight` ，但是IE并没有捡起`window.innerWidth/innerHeight` 。

在桌面浏览器上，两个属性对儿都能用是个小麻烦，但是在移动浏览器上，这可是个好东西。我们拭目以待。

（译者注：在桌面浏览器上，这俩属性一减，就是滚动条的宽度，有用的很！）

## 测量`<html>`元素

So…`document.documentElement.clientWidth/clientHeight`提供了视口尺寸。但是去哪儿找`<html>`元素自身的尺寸呢？答案是，`<html>`的尺寸被存储在了`document.documentElement.offsetWidth/offsetHeight` 中。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_offset.jpg) 

如果你设置`width`，`offsetWidth`会实时改变。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_offset_smallpage.jpg) 

## 事件坐标

然后嘞，还有事件坐标。当鼠标事件触发时，多达五个属性对儿被暴露出来，为你提供这个事件的精确信息。对本文的讨论来说，有三对儿重要的：

1. `pageX/Y` 给出了相对于 `<html>` 元素的坐标，以CSS像素为单位计。
2. `clientX/Y` 给出了相对于视口的坐标，以CSS像素为单位计。
3. `screenX/Y` 给出了相对于屏幕的坐标，以设备像素为单位计。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_pageXY.jpg) 

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_clientXY.jpg) 

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_screenXY.jpg) 

在90%的情况下，你都会使用`pageX/Y` ；通常你会想知道事件相对于document的位置。在剩余的10%的情况下，你会使用`clientX/Y` 。你永远也不需要知道事件相对于屏幕的坐标。

## 媒体查询

最终，来说说媒体查询。很简单：你可以为特定宽度范围内的页面指定要执行的CSS规则。例如：

```css
div.sidebar {
	width: 300px;
}

@media all and (max-width: 400px) {
	// styles assigned when width is smaller than 400px;
	div.sidebar {
		width: 100px;
	}

}
```

现在，sidebar是300px宽了，当**宽度**小于400px时，sidebar就会变成100px宽。

问题来了，测量的依据是哪个宽度？

有两个相关的媒体查询：`width/height` 和`device-width/device-height`。

1. `width/height` 使用和`documentElement.clientWidth/clientHeight`相同的值 （也就是视口），以CSS像素为单位计。
2. `device-width/device-height` 使用和 `screen.width/height` 相同的值（也就是屏幕）。以设备像素为单位计。

![img](https://www.quirksmode.org/mobile/pix/viewport/desktop_mediaqueries.jpg) 

那么，该用哪个？不用想！用`width`！网页开发者对设备宽度不感兴趣，浏览器窗口宽度才重要。

所以，在桌面浏览器上，用`width`，忘记`device-width`。我们将会看到，移动设备上的情况要复杂的多。

## 结论

就这样，结束了对桌面浏览器行为的探索。本系列的[第二部分](https://github.com/shaoweilee/blog/blob/master/%E4%B8%A4%E7%A7%8Dviewport%E7%9A%84%E5%8F%99%E8%BF%B0%E2%80%94%E2%80%94%E7%AC%AC%E4%BA%8C%E9%83%A8%E5%88%86.md)将这些概念移植到移动设备上，并突出与桌面浏览器的重要区别。