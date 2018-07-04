# HTML可访问性

其实就是注意两点：accessibility，SEO。

- 为搜索引擎考虑；
- 为移动设备考虑；
- 为残疾人考虑；
- 为低网速考虑。

## 一、使用语义化的元素：

### 文字内容清晰：

该用h1就用h1，该用p就用p，该用列表就用列表。

一个bad例子就是，dl dt dd三个元素，其中dd是自带缩进的，但是不要为了缩进而用dd，这样很不语义化——dd是有自己的用处的。普通文本请用text-indent，调整dd的缩进请用margin。

### 页面布局清晰：

nav包装主导航，footer包页脚，可以用div也可以用section就用section，可以用div也可以用article就用article。section>article。

## 二、使用对键盘和手指友好的UI控件：

通常是按钮button，链接a和表单控件input。不要忘记tab键。

### 重构键盘可访问性：

重构全div布局的页面，请为div设置tabindex=0。这样它就可以用tab键访问到了。

虽然这样的div可以用tab切换到了，但是仍然不能用enter或return键激活，如果想为它添加这个功能，请这样做：

```javascript
//只有onclick设置的事件处理程序才起作用。
div.onclick = function(){alert('onclick')}
//addEventListener添加的事件处理程序，不会触发。
div.addEventListener('click', function(){
    alert('click!')
});
document.onkeydown = function(e) {
    if(e.keyCode === 13) { // The Enter/Return key
        document.activeElement.onclick(e);
    }
};
```

tabindex本来是给可用tab键切换焦点的元素设置顺序的，但是有时候就混乱了，比如页面header区域有一个button的tabindex为10，最底部有一个button的tabindex为1，那么用tab切换的时候，焦点会首先定位到tabindex为1的button上，这在逻辑上是错误的。

另外一个tabindex可选值为-1，设置之后，原本可用tab切换到的按钮，就切换不到了。

### 表单

该用label就用label！

## 三、对阅读器友好

使用文本替代品：

例如，有四个img的写法：

```html
<img src="dinosaur.png">

<img src="dinosaur.png"
     alt="A red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth.">

<img src="dinosaur.png"
     alt="A red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth."
     title="The Mozilla red dinosaur">

<img src="dinosaur.png" aria-labelledby="dino-label">
<p id="dino-label">使用相同的文本，作多张图片的文字说明，alt可办不到。The Mozilla red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth.</p>
```

