# 设备像素，设备独立像素，CSS像素

谢天谢地，让我找到[这么个好文](http://yunkus.com/physical-pixel-device-independent-pixels/)。

补充这么一幅图，来自https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions

![1533098619799](设备像素，设备独立像素，CSS像素.assets/1533098619799.png)

## 基本定义

### 设备像素

设备像素又称为物理像素，是硬件，是设备能控制的最小的那个显示单位。一个设备像素，就是显示器上的一个点。比如我的手机是1920*1080的分辨率，其实就是说的手机屏幕上，有1920\*1080个像素点。

### CSS像素

CSS像素是Web编程的概念，独立于设备的、用于逻辑上衡量像素的单位，也就是说我们在做网页时用到的CSS像素单位，是抽象的，而不是实际存在的。 

### 设备独立像素

独立于设备的像素，与设备无关的像素。

一个设备独立像素，可以认为是计算机坐标系统中的一个点，这个点代表一个可以由程序使用并控制的虚拟像素(比如：CSS 像素,只是在android机中CSS 像素就不叫”CSS 像素”了而是叫”设备独立像素”)，然后由相关系统转换为物理像素。 



通过上面的定义我们就可以从中挑出两个来展开就可以了，也就是设备像素和设备独立像素（在本文我们不妨假装Android的“设备独立像素”是通用叫法），正如你所看到的，因为设备独立像素已经包括了CSS 像素。 

在PC端可以通过`screen.width/height`属性来获取设备独立像素值，在PC端，这个值经常被我们称为**屏幕分辨率**（实际上它不是，但是由于在PC端设备像素和设备独立像素数值相等，才有这么一个不准确的说法）。但你把浏览器切换到设备模式时，也就是在移动端环境下通过上面的`screen.width/height`获取的不是移动设备的分辨率，而是远远小于分辨率的值，比如，我的手机是小米Mix2s，分辨率是2160\*1080像素，用screen.width和screen.height测出的值是786\*393。所以不管是移动端还是PC端，通过`screen.width/height`获取的这个值是设备独立像素（CSS 像素），而不是设备的屏幕分辨率，因为设备的屏幕分辨率对于WEB开发者来说是无法通过代码来获得的，是完全透明的。 



## 设备像素与设备独立像素的关系

它俩的关系就是，一个设备独立像素，占用了几个设备像素。

在一定的条件下两者它们两者是可以相等的，比如：在PC端浏览器默认情况下（100%，即页面没被缩放），一个设备独立像素，占用了一个设备像素。而在移动端可就不一样的，这两个值很多时候是不相等的。为什么会不相等，因为为了让你看起来更爽，爽在哪里？就是画质的精细度。即使它们有关系，但又不相等，那么总得有个说法或者公式吧？别急，在这之前，我们还得认识个东西叫PPI。 

PPI又是个什么东西？总之它是个好东西，不然为什么各品牌的手机商开发布会时常常挂在嘴边。PPI 全称是（pixel per inch）翻译下就是每英寸有多少个像素点，这个像素点指的是设备像素点（物理像素），说得接地气点PPI就是像素密度（pixel  density）。PPI的值越高，画质越好，也就是越细腻，看起来更有逼格。那么PPI是怎么算出来的呢？我来看看它的公式。 

![physical-pixel-device-independent-pixels](http://yunkus.com/wp-content/uploads/2016/07/physical-pixel-device-independent-pixels-1.jpg) 

解释一下：看着像勾股定理吧，其实它就是勾股定理。

设备尺寸，常说的你手机多少英寸呀，你披萨多少英寸呀（滑稽），其实是屏幕对角线的长度，按英寸计。那么像素数呢，也得看对角线上有多少个像素，两条边的像素数平方再相加，然后开方，就是对角线上的像素数了，再除以对角线的长度，就是每英寸内的像素数了。

![physical-pixel-device-independent-pixels](http://yunkus.com/wp-content/uploads/2016/07/physical-pixel-device-independent-pixels-2-1.png) 

就是这么算出来的。

回到上面的问题：两者（设备像素和设备独立像素）是如何进行换算的？它们是通过设备像素比(dpr,device pixel ratio)来进行换算的。那么什么是设备像素比呢？ 

## 设备像素比

### 定义

```
设备像素比 = 设备像素/设备独立像素 // 在某一方向上，x方向或者y方向
```

那么现在问题来了，什么是设备独立像素呢？上面不是说过了吗？但我觉得再重复一篇也不为过。
设备独立像素（DIP，device-independent pixel，density-independent pixel），简单地来说设备独立像素就是：独立于设备的用于逻辑上衡量像素的单位。

逻辑上衡量像素的单位？这不就是说CSS 像素吗？没错就是它。好下面我们就来接着讲。

### 获取设备像素比

你可以通过JavaScript 中的`window.devicePixelRatio`来获取设备像素比。那么这个设备像素比有什么用呢？

它可以告诉你一个设备独立像素占用了多少设备像素。

还有一个东西我们明白了对于理解这个设备像素比会有帮助。设备像素的大小是在手机生产线上就固定了的，是不可变的，而设备独立像素是可以被拉长或者被压缩的。这个要怎么理解呢，我们不妨来看看三个图，看完后相信你会明明白白的。

深色为设备像素，浅色为设备独立像素。

![device-independent-pixels（dip）](http://yunkus.com/wp-content/uploads/2016/07/physical-pixel-device-independent-pixels-1.png) 标准PPI（160）下的设备像素比 

![device-independent-pixels（dip）](http://yunkus.com/wp-content/uploads/2016/07/physical-pixel-device-independent-pixels-2.png) 经放大后的设备像素比图示 

![device-independent-pixels（dip）](http://yunkus.com/wp-content/uploads/2016/07/physical-pixel-device-independent-pixels-3.png) 经缩小后的设备像素比示图 

下面我们来看个现实中的例子，Iphone 5s 使用的是 Retina 视网膜屏幕，什么是Retina视网膜屏幕？PPI 值超过 300 的叫做超高密度屏幕，只是 Apple 给它换了个高大尚的名称：Retina 视网膜屏幕而已。 

在做移动页面开发的时候，相信你经常会遇到这种情况：在不同的手机上看时，里面的图片、文字或者线的大小会不一样，有时候大小区别还非常地大。原因就是刚才说到的设备像素比在作怪。 

你想啊，如果在普通屏即标准 PPI下，一个CSS像素占用一个设备像素，页面不大不小，完美地渲染出来了。但在现在这个高逼格的年代，标准 PPI 已经很少见了。更多的是 Retina视网膜设备。而设备像素比不同的品牌的手机这个值也是不一样的。即使是同一个品牌的手机也不一样。你比如  Iphone 5s 设备像素比为2，Iphone 6s 设备像素比为3。至于安卓机中的设备像素比就更多了，有1.3、1.5、2、3等等。（我的手机竟然是2.75） 



最后……英雄请留步，且听我一言……

有一天，操作系统觉得累了，你们整天说这个像素，那个像素，烦不烦？这样吧，我给你个单位，叫**设备独立像素**，我这里呢，还有个比例，叫**设备像素比**。你只管告诉我，你这个小浏览器，想要多少个**设备独立像素**（不许缩放！），我嘞，到时候哇，直接拿你这个**设备独立像素**乘以**设备像素比**，我把得出的数，告诉屏幕，然后屏幕去调用多少个**设备像素**（物理像素，像素点）去呈现你就行了，公平公正，童叟无欺！

我觉得，这几个家伙之间就是这个关系……

