# 先说说gradient

<gradient>是一种<image>CSS数据类型的子类型，用于表现两种或多种颜色的过渡转变。所以，它只能被用于图片可以用到的地方。

<gradient>没有尺寸；即不具备固有尺寸或首选尺寸，也不具备首选的比率，其实际大小取决于其填充元素的大小。

<gradient>由下列函数定义：

`linear-gradient()`——线性渐变。

`radial-gradient()`——径向渐变。

`repeating-linear-gradient()`——重复线性渐变。

`repeating-radial-gradient()`——重复径向渐变。

# linear-gradient

[示例](https://codepen.io/shaoweilee/pen/rqgyNj?editors=1100)

语法：

```
linear-gradient([ <angle> | to <side-or-corner> ,]? <color-stop> [, <color-stop>, ...])
```

其中， 第一个参数`[ <angle> | to <side-or-corner> ]`在直观上，定义了渐变的方向，角度 或 to+关键字 的形式二选一。

在使用关键字时，可以使用一个关键字，如`to right`，也可以用两个关键字，如`to right bottom`。

第二个参数`<color-stop>`，实际是`<color> [<percentage>|<length>]? `的缩写。第一个颜色默认从0%开始，最后一个颜色默认到100%结束。

```
linear-gradient(to right, red 0%, red 80%, blue);
```

上面这段可以解释为：一个向右的渐变，完全的红色从0%开始，持续到80%，到100%的位置渐变为完全的蓝色。

# radial-gradient



径向渐变，要比线性渐变复杂一些。查看[示例](https://codepen.io/shaoweilee/pen/ePaWLx?editors=1100)。

其语法为……

```
radial-gradient(形状, 半径, 位置, 颜色们);
```

或……

 `radial-gradient(半径, 形状, 位置, 颜色们);`  

为了避免被打死，我就不在这里贴[MDN语法](https://developer.mozilla.org/zh-CN/docs/Web/CSS/radial-gradient)了o(╥﹏╥)o。然后，张鑫旭大大也提供了[十个示例](https://www.zhangxinxu.com/wordpress/2017/11/css3-radial-gradient-syntax-example/)，方便学习。

以上参数中：

- 形状：可选，默认是椭圆ellipse。如果容器是个正方形，椭圆就变成了正圆。可选值：ellipse，circle。

- 半径：可选，默认是关键字`farthest-corner`，下面有关键字详细含义。如果形状设置为circle，大小可选值为：关键字、长度（如果设为具体长度，只能设置一个，因为设置的是半径）；如果形状为ellipse，大小可选值为：关键字、长度（如果设置为具体长度，必须设置两个，因为设置的是x方向的半径和y方向的半径）、百分比（相对于容器自身宽高）。另外如果使用具体长度，xy设为相同的值，就又是圆形了。

- 位置：可选，定义渐变中心的位置。默认为容器中心点，也可参考transform-origin。

- 颜色们：与线性渐变相同。

## 关键字详细含义

 

| 关键字            | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `closest-side`    | 渐变的Ending shape与容器距离渐变中心点最近的一边相切（圆形）或者至少与距离渐变中心点最近的垂直和水平边相切（椭圆）。 |
| `closest-corner`  | 渐变的边缘形状与容器距离渐变中心点最近的一个角相交。         |
| `farthest-side`   | 与closest-side相反，边缘形状与容器距离渐变中心点最远的一边相切（或最远的垂直和水平边）。 |
| `farthest-corner` | 渐变的边缘形状与容器距离渐变中心点最远的一个角相交。         |



# 重复渐变 

最后说下重复渐变。它们与不重复的渐变的不同点是，如果最后一个颜色设置了位置，那么只要不是100%的位置，那么这个渐变就会被重复，直到充满整个容器。这也使得正圆径向渐变中，半径的设置不起作用。查看[示例](https://codepen.io/shaoweilee/pen/MPdrej?editors=1100)。











