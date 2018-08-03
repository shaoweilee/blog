# 两种viewport的叙述——第二部分

[TOC]

[**原文链接**](https://www.quirksmode.org/mobile/viewports2.html)

在这个小系列中，我会阐释几种重要元素的宽度（像html，window，screen），还有viewports是怎么工作的。

这篇文章主要讲手机浏览器，如果你是个纯小白，我建议你先读[第一部分](https://github.com/shaoweilee/blog/blob/master/%E4%B8%A4%E7%A7%8Dviewport%E7%9A%84%E5%8F%99%E8%BF%B0%E2%80%94%E2%80%94%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86.md)，是关于桌面浏览器的，先了解下这个家庭嘛。

## 手机浏览器的问题

当我们比较手机浏览器和桌面浏览器的时候，最明显的不同就是屏幕尺寸。手机浏览器显示的网页内容显著地少于桌面浏览器；可以缩小页面直到文字都看不清，或者显示网页的一小部分来适应屏幕大小，以使我们能够看得清。

手机屏幕远远小于桌面屏幕的大小，最大也就大约400px宽吧，甚至更少。

带来的最明显的问题就是CSS，特别是viewport的尺寸。如果我们把桌面网页原封不动地复制到移动设备上，CSS就全乱了。假如一个网站的sidebar设置了'width: 10%'，如果手机浏览器和桌面浏览器行为一样，那么这个sidebar也就是最多40px宽，太窄了，你的流体布局看起来就会变得……挤扁了，丑爆了。

一种解决方式是，为手机浏览器单独做一个专门的网站。先不用说有没有这样做的必要，实际问题是（那个时候）几乎没人对移动站点开发有经验。

手机浏览器厂商想为他们的用户提供顶呱呱的体验，放在现在就意味着“用起来和电脑一样！”，因此得有一些小技巧……

## 两种视口（viewports）

手机视口（屏幕）太窄了！跟CSS布局不大对付，最明显的解决方式是让手机视口变得宽，非常宽，像电脑那么宽！这么一来，就必须分出两种视口：visual viewport和layout viewport。（可见视口和布局视口）

这篇文章的作者也引用了一段话来解释这两种视口：

> “想象一下，布局视口（layout viewport）是一幅不会改变形状和尺寸的大大的画，现在你有一个小框框，你能通过这个框来看这幅画，小框周围被不透明的材料给糊住了，遮挡了你的视线，也遮挡了大部分的画，有点坐井观天的意思。你能通过小框框看到的那一小部分画，就是可见视口（visual viewport），当你拿着这个小框框离着画远了点，就能一下子看到全部的画了（缩小）；当你离着画近了点，卧槽能看到的又少了（放大）。你也可以旋转小框框，但是整幅画的大小形状永远不变。”——译者注：框框不是可见视口，那一小部分画才是，这意味着，可见视口大小是可以缩放的。
>

可见视口就是显示在手机屏幕上的那部分网页，用户可以通过滚动，来决定能看到哪部分网页；或者直接缩放可见视口的大小。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_visualviewport.jpg) 

然而，在CSS布局的问题上，特别是百分比宽度，是根据更宽的那个视口——布局视口（layout viewport）来计算的。

因此，html元素初始用的是布局视口的宽度，CSS以为你的屏幕比手机屏幕大得多呢！这保证了你的网站布局“跟电脑真的一样了！”呵呵呵呵呵呵呵呵呵呵……

那么，layout viewport（布局视口）究竟有多宽？答案是每个浏览器眼中的顶呱呱体验都不一样：

- Safari iPhone 是 980px；
- Android WebKit 是 800px；
- Opera 是 850px；
- IE 是 974px。 

### 缩放

这两种视口显然都是以CSS像素测量的。但是，我们可以缩放可见视口呀，可见视口的大小是会变的（如果放大可见视口，屏幕上的CSS像素数变了。举栗子，去海滩看沙子，一粒沙子就是一个CSS像素，低头一看视野中好多沙子，但是把一撮沙子放在显微镜下看……你懂得，我也没看过。但是在这个过程中，沙子本身的大小是没有变化的），而布局视口面积保持不变（CSS像素总数不变）。

### 理解布局视口（layout viewport）

为了理解布局视口，我们必须看看，当页面缩放到最小时，发生了什么。许多手机浏览器初始都是以最小的缩放级别来显示网页的。

重点在于：浏览器已经确定了布局视口的大小，因此在最小缩放级别下，布局视口能够完全覆盖屏幕（这么一来，布局视口就和可见视口一样大了。译者以为，根本原因是两个视口中的CSS像素数一样了。）

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_viewportzoomedout.jpg) 

因此，布局视口的宽高就等于，在最小缩放级别下，屏幕上显示的所有内容。当页面缩放时，这个宽高不变。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_layoutviewport.jpg) 

布局视口的宽度保持不变。如果你旋转手机，变化的是可见视口，浏览器会自动地稍稍放大，以适应新的方向，这样一来，布局视口又和可见视口一样宽了（布局视口=可见视口，仅在最小缩放级别下成立。还是拿看画举栗子，当走得足够远从长方形的框里看画，框框完全把画包裹在内，但是框框旋转90度之后，画的宽度是不足以塞满整个框框的，必须再走近一点，也就是稍稍放大，画的宽度才能塞满整个框框）。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_viewportzoomedout_la.jpg) 

这对布局视口的高度有显著影响，毕竟比手机竖着拿少多了。但是网页开发者并不关心高度，只关心宽度。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_layoutviewport_la.jpg) 

## 测量布局视口

现在，我们想测量两种视口的尺寸。幸运的是，浏览器大战提供了两对儿属性。

document.documentElement.clientWidth和document.documentElement.clientHeight包含了布局视口的尺寸。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_client.jpg) 

旋转对高度有影响，对宽度无影响。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_client_la.jpg) 

## 测量可见视口

对可见视口来说，可以通过window.innerWidth和window.innerHeight测量。显然地，当页面缩放时，测量结果也会变化，因为屏幕中的CSS像素数变了。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_inner.jpg) 

不幸的是，上述属性对儿有兼容性问题；许多浏览器还是需要提供对可见视口测量的支持。目前，还没有浏览器把可见视口测量值存储为任何属性对儿，所以我想（作者想）window.innerWidth和window.innerHeight就应该是规范，尽管支持度不大好。

## 屏幕

和桌面浏览器一样，screen.width和screen.height是以**设备像素**为单位，给出了屏幕的尺寸，作为一个前端开发者，你基本上用不到这个属性，不用关心屏幕的物理大小，只关心多少CSS像素能够适配就行了。

（译者注：这句话现在看是有BUG的，不知道是不是时代的原因：假设如PPK所说，screen.wdith以设备像素为单位，且与物理像素无关，那它只能指代以“点”计的设备独立像素，事实上现在它的返回值就是设备独立像素数。而当我们设置meta-viewport标签后，这个数值就是适配屏幕的CSS像素数。）

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_screen.jpg) 

### 缩放层级

直接辨别缩放层级是不可能的，但是你可以通过一个简单的除法算出来，那就是screen.width/window.innerWidth，当然了，在两种属性被完美支持的前提下。

幸运的是，缩放层级不重要！你需要知道的是多少CSS像素能适配屏幕就行了，并且可以通过window.innerWidth来知道这个信息——如果这个属性被正确支持了……

## 滚动偏移

你还需要知道的是，可见视口相对于布局视口的位置，也就是滚动偏移，在桌面浏览器上，它就是window.pageXOffset和window.pageYOffset。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_page.jpg) 

## \<html>元素

仅在桌面浏览器上，document.documentElement.offsetWidth和document.documentElement.offsetHeight，这两个属性以CSS像素为单位，给出了\<html>元素的总宽高。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_offset.jpg) 

## 媒体查询

媒体查询，在移动端和桌面浏览器中有着相同的表现。width/height指代的是布局视口，测量值也是以CSS像素为单位。device-width/device-height是指设备屏幕，测量值是以设备像素为单位。

也就是说，width/height指的是document.documentElement.clientWidth/document.documentElement.clientHeight的值，而device-width/device-height指的是screen.width/screen.height的值。（所有浏览器都是这么干的，即使这个值是错误的。）

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_mediaqueries.jpg) 

那么，哪种测量值对前端开发来说更有用？我也不知道。

我一开始以为，device-width是最重要的，因为它给出了我们使用的设备的信息。例如，你可以改变布局时的width，来顺应设备的宽度。然而，你也可以用`<meta viewport>` 标签达到同样的目的；也不是非得用device-width媒体查询。

所以，width媒体查询就更重要啦？可能吧；它暗示着浏览器厂商所认为的，在特定设备上对一个网站来说“好的宽度”，但是相当含糊，并且width媒体查询也给不出其他信息了。

所以我犹豫了。我认为，媒体查询对我们搞清楚用的是什么设备相当重要，桌面浏览器？平板？手机？但是对于区分不同的平板和手机就没那么有用了。

## 事件坐标

事件坐标，移动设备和桌面浏览器上的表现有些相同的地方。不幸的是，在12款测试过的浏览器中，只有两款的事件坐标是全部正确的：Symbian WebKit  和 Iris，其他的所有浏览器都有严重的问题。

`e.pageX/e.pageY`仍然与页面位置相关，测量单位也是CSS像素，目前这是最有用的属性了；

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_pageXY.jpg) 

`e.clientX/e.clientY`与可见视口有关，以CSS像素为单位。这很有意义，虽然我不知道具体的好处是啥……

`e.screenX/e.screenY`与屏幕有关，以设备像素为单位。当然了，它的参考坐标和clientX/clientY相同，并且设备像素没啥用。所以我们不用关心screenX/screenY，没啥用。

![img](https://www.quirksmode.org/mobile/pix/viewport/mobile_clientXY.jpg) 

## Meta viewport

最后，来讨论一下这个标签：`<meta name="viewport" content="width=320">`; 一开始是苹果提出来的，后来被其他浏览器所借鉴。它的意思是重设布局视口（layout viewport）的大小。为了理解它为啥这么有用，我们退一步看。

假如，你做了一个简单的页面，没给元素宽度。那么这个元素就会拉伸，占用100%布局视口的宽度，大多数浏览器会缩小页面来把整个布局视口都呈现出来，类似这样的效果：

![img](https://www.quirksmode.org/mobile/pix/viewport/mq_none.jpg) 

所有人！都会马上放大页面，这起效果，但是大多数浏览器都会保持住这个元素的宽度不变，使得文字读起来很难（因为要左右滑动屏幕）：

![img](https://www.quirksmode.org/mobile/pix/viewport/mq_none_zoomed.jpg) 

（有个例外是Android WebKit，它会减少包含文字的元素的宽度，使得它们适配屏幕。这种做法很聪明，我觉得所有浏览器都该借鉴一下，我（作者）会再写文章记录下。）

现在，可以尝试设置：`html {width: 320px}`。那么html元素带着它的子孙都缩短了，占用320px的100%。当用户放大页面的时候，这很OK啊。但是初始呈现的页面就不OK了（还记得吗，刚才说浏览器会把整个布局视口呈现出来），用户会面对着一个缩放的页面，页面的大部分是空白的：

![img](https://www.quirksmode.org/mobile/pix/viewport/mq_html300.jpg) 

为了解决这个问题，苹果公司发明了meta viewport标签。当你设置了 `<meta name="viewport" content="width=320">` ，你实际上把布局视口的宽度设为了320px。现在初始呈现的页面也OK了：

![img](https://www.quirksmode.org/mobile/pix/viewport/mq_yes.jpg) 

你可以把布局视口的尺寸设为任何值，包括`device-width`，它会参照screen.width（按设备独立像素）重设布局视口的大小。

但还是有个陷阱，有时候screen.width不怎么有用，因为像素太高了。比如，Nexus One宽度是480px，但是谷歌工程师决定：当使用device-width导致太宽的时候，就在480px的宽度上作出点让步，他们把它缩短到2/3，device-width就给你个320px的宽度，跟iPhone一样。

传闻，新的iPhone会秀出高像素（不可避免地，等同于大屏幕了），如果苹果采用和谷歌一样的做法，我也不会吃鲸。也许，最终device-width会仅仅代表着320px。











