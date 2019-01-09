本篇记录CSS中样式优先级的顺序，以及常见的继承属性与非继承属性。

样式优先级顺序，向下依次降低：

貌似命中的越少，优先级越高……

```
!important
inline style
id选择器
类选择器=属性选择器=伪类选择器
元素选择器=伪元素选择器
*通用选择器
浏览器默认样式
继承样式
```

比较的时候，优先比较优先级最高的，这就导致，10个元素选择器都比不上1个类选择器……

 

### 常见继承属性

- 文本相关属性都可以继承

     color、font、font-family、font-size、font-style、font-variant、font-weight、text-decoration、text-transform、letter-spacing、word-spacing、white-space、word-break、overflow-wrap、line-height、direction、text-indent、text-align、text-shadow

- 列表相关属性

    list-style-image、list-style-position、list-style-type、list-style

- 表格相关属性

    border-collapse、border-spacing

- visibility 和 cursor

### 常见非继承属性

- 盒模型相关属性

    margin、border、padding、height、min-height、max-height、width、min-width、max-width、box-sizing

- 布局类属性

    display、overflow、position、left、right、top、bottom、z-index、float、clear、table-layout、vertical-align

- 系列类

    background 系列、transform 系列、transtion 系列、animation 系列、flexbox 系列、grid 系列