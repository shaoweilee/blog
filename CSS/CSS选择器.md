# CSS选择器

## 类选择器

[示例](https://codepen.io/shaoweilee/pen/yRwvBx?editors=1100)

值得注意的是**多类选择器**，类名写在一起，中间不要用空格隔开。

## 属性选择器

（其实，为了与JS对象属性区分，我更愿意把元素上的属性称为“特性”）

分三种，简单属性选择器、具体属性选择器、字串匹配属性选择器。

- 简单属性选择器，会命中有某某属性的某元素：

```
*[title] {color:red;}//命中所有具有title属性的元素
a[href] {color:red;}//命中所有具有href属性的a元素
a[href][title] {color:red;}//命中同时具有href属性和title属性的a元素
img[alt] {border: 5px solid red;}//命中具有alt属性的img元素
```

- 具体属性选择器：

```
a[href="http://www.w3school.com.cn/"][title="W3School"] {color: red;}
//会命中：
<a href="http://www.w3school.com.cn/" title="W3School">W3School</a>

p[class="important warning"] {color: red;}
//会命中
<p class="important warning">完全匹配哦</p>
```

值得注意的是匹配时进行**完全匹配**。如果要根据部分属性值匹配，需要使用波浪号~，即便如此，也需要用空格分开：

```
p[class~="warning"] {color: red;}
//会命中
<p class="important warning">部分匹配哦</p>

//但是 下面这样的不会中：
[title~='hel']{color: red;}
<a title='hello'></a>
```

此外，还有这种形式：

```
[att|="val"] {color: red;}
```

它会命中属性值精确为“val”，或者属性值中包含以“val-”开头的单词的所有元素，如`class=“val-ue value”`。

- 字串匹配属性选择器：

强大，且支持度良好，IE7都支持。

| 类型         | 描述                                       |
| ------------ | ------------------------------------------ |
| [abc^="def"] | 选择 abc 属性值以 "def" 开头的所有元素     |
| [abc$="def"] | 选择 abc 属性值以 "def" 结尾的所有元素     |
| [abc*="def"] | 选择 abc 属性值中包含子串 "def" 的所有元素 |

字串匹配属性选择器弥补了具体属性选择器的缺点，对于任何属性，都可以用偏向正则的方式匹配。

## 关系选择器

[示例](https://codepen.io/shaoweilee/pen/gBEoQw?editors=1100)

在关系选择器中，有这么几种：

后代选择器，以空格隔开：div p；选中div元素中所有的p元素；

子选择器，以>隔开：div>p；选中div下的直接p子元素，隔代选不中；

相邻兄弟选择器，以+隔开：div+p；选中紧跟在div之后的一个同级p元素；

通用兄弟选择器，以~隔开：div~p；选中紧跟在div之后的所有同级p元素；

## 伪类选择器

伪类选择器，种类非常非常多，其语法为：

```
选择器 : 伪类 {property: value}
```

下面是常用的伪类：

[示例](https://codepen.io/shaoweilee/pen/dgrjrg?editors=1100#0)

注意顺序不要弄错。其中，`link`和`visited`只能用于超链接，属于**链接伪类**。`focus`、`hover`、`active`可应用于任何元素，属于**用户行为伪类**。

此外，还有**目标伪类**:target，当一个元素被URL锚点命中时，就会应用:target伪类样式，查看[示例](https://codepen.io/shaoweilee/pen/vVMGqe#div)。甚至还可以做简单的[手风琴效果](https://codepen.io/shaoweilee/pen/YJgOYK?editors=1100)。

其次是**UI元素状态伪类**，用于设置UI元素在某个状态下的样式，查看[示例](https://codepen.io/shaoweilee/pen/ReORPQ?editors=1100)。

最后是**结构伪类**，[示例](https://codepen.io/shaoweilee/pen/qJwNvo?editors=1100#0)，种类很多：

```
//如果E没有定义，则为*
E:first-child，会命中E元素，但这个E元素必须是某个元素的第一个子元素；
E:last-child，会命中E元素，但这个E元素必须是某个元素的第一个子元素；
E:nth-child(an+b)，收集所有E元素的兄弟元素，然后从1开始排序，接着会命中第an+b个元素，n=0,1,2,3...；
E:nth-last-child(an+b)，同上，不过是倒数an+b个元素；

E:first-of-type，等同于E:nth-of-type(1)；
E:last-of-type，等同于E:nth-last-of-type(1)；
E:nth-of-type(an+b)，在E的所有兄弟元素中，收集与之类型相同的元素，然后从1开始排序，接着会命中第an+b个元素，n=0,1,2,3...；但如果E不是标签选择器，在这里，我只表述一下我的符合实际表现的猜想（示例中有体现），因为规范中并没有提及此种情况下的行为：首先，在选择器E命中的元素的所有兄弟元素中，按照标签名分组，在本例中是<p>、<div>、<span>，然后在每个分组中，查看an+b所对应的元素，如果该元素同时被选择器E命中，则该元素最终被命中。简而言之，如果一个元素被*:nth-of-type(an+b)命中，也被选择器E命中，则它被选择器E:nth-of-type(an+b)命中。其实仔细想想，无论E是不是标签选择器，都适用这个结论。甚至可以推广到所有结构伪类选择器。
E:nth-last-of-type(an+b)，同上，只不过是倒数。

E:only-child，会命中E元素，但这个E元素必须是某个元素的唯一子元素；
E:only-of-type，会命中E元素，但在其父元素中，必须只有一个E类型的元素；
E:empty，会命中E元素，但E元素不能包含任何节点。
```

## 伪元素选择器

常用的是E::after和E::before，不会命中任何实际元素，但会在E的content开始和结尾处插入一个inline元素，从E身上继承可继承的属性。如果与其他选择器连用，E::after和E::before必须写在最后面。