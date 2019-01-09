本文摘自[小火柴的博客](https://www.xiaohuochai.cc/posts/5a7ef0331453972fd0a53eed)。

 html5出现之前，经常把元素按照block、inline、inline-block来区分。在html5中，元素不再按照display属性来区分，而是按照[内容模型](http://www.cnblogs.com/xiaohuochai/p/5046584.html)来区分，分为元数据型(metadata  content)、区块型(sectioning content)、标题型(heading content)、文档流型(flow  content)、语句型(phrasing content)、内嵌型(embedded content)、交互型(interactive  content)。元素不属于任何一个类别，被称为穿透的；元素可能属于不止一个类别，称为混合的。 

# 标签分类 

 

**元数据元素**(metadata content)是可以被用于说明其他内容的表现或行为，或者在当前文档和其他文档之间建立联系的元素

```
base link meta noscript script style template title
```

**流元素**(flow content)是在应用程序和文档的主体部分中使用的大部分元素

```
a abbr address area(如果它是map元素的子元素) article aside audio b bdi 
bdo blockquote br button canvas cite code data datalist del dfn div 
dl em embed fieldset figure footer form h1 h2 h3 h4 h5 h6 header hr 
i iframe img input ins kbd keygen label main map mark math meter 
nav noscript object ol output p pre progress q ruby s samp script 
section select small span strong sub sup svg table template 
textarea time u ul var video wbr text
```

**区块型元素**(sectioning content)是用于定义标题及页脚范围的元素

```
article aside nav section
```

**标题型元素**(heading content)定义一个区块/章节的标题

```
h1 h2 h3 h4 h5 h6
```

**语句型元素**(phrasing content)是用于标记段落级文本的元素

```
a abbr area (如果它是map元素的子级) audio b bdi bdo br button 
canvas cite code data datalist del dfn em embed i iframe img 
input ins kbd keygen label map mark math meter noscript object 
output progress q ruby s samp script select small span strong 
sub sup svg template textarea time u var video wbr text
```

**嵌入型元素**(embedded content)是引用或插入到文档中其他资源的元素

```
audio canvas embed iframe img math object svg video
```

**交互型元素**(interactive content)是专门用于与用户交互的元素

```
a audio(如果设置了controls属性) button embed iframe 
img(如果设置了usemap属性) input(如果type属性不为hidden) keygen 
label object(如果设置了usemap属性) select textarea 
video (如果设置了controls属性)
```

 

# 嵌套规则

## 下列元素，子元素是流元素：

 

```
<article>、<section>、<blockquote>、<li>、<dd>、<figcaption>、<div>
<main>、<td>
```

 

下列元素，子元素是流元素，但不包括`<main>`元素

```
<aside>、<nav>
```

 下列元素，  子元素是流元素，但不包括`<table>`元素

```
<caption>
```

 下列元素，  子元素是流元素，但不包括`<form>`元素

```
<form>
```

 下列元素，  子元素是流元素，但不包括`<header>`、`<footer>`、`<main>`元素

```
<header>、<footer>、<main>
```

 下列元素，  子元素是流元素，但不包括`<header>`、`<footer>`、区块型元素(sectioning content)、标题型元素(heading content)

```
<dt>、<th>
```

 下列元素，  子元素是流元素，但不包括`<header>`、`<footer>`、`<address>`、区块型元素(sectioning content)、标题型元素(heading content)

```
<address>
```

 下列元素，  子元素是一个`<figcaption>`元素，紧跟着流元素

```
<figure>
```

 下列元素，  子元素是一个`<legend>`元素，紧跟着流元素

```
<filedset>
```



## 下列元素，子元素是语句型元素

```
<h1>、<h2>、<h3>、<h4>、<h5>、<h6>、<p>、<pre>、<em>、<strong>、
<small>、<s>、<cite>、<q>、<abbr>、<data>、<time>、<code>、<var>、
<samp></span>、<kbd>、<sub>、<sup>、<i>、<b>、<u>、<mark>、<bdi>、
<bdo>、<span>、<input>、<output>、<legend>、<label>
```

 下列元素，  子元素是语句型元素，但不包括和自身相同的元素

```
<dfn>、<progress>、<meter>
```

 下列元素，  子元素是语句型元素，但不包括交互型元素(interactive content)

```
<button>
```



 

##  下列元素，  子元素是transparent(以它的父元素允许的子元素为准)

```
<ins>、<del>、<map>
```

 下列元素，  子元素是transparent(以它的父元素允许的子元素为准)，但不包括交互型元素(interactive content)

```
<a>
```

 下列元素，  子元素可以没有、可以是`<param>`元素，也可以是transparent(以它的父元素允许的子元素为准)

```
<object></span>
```



 

##  下列元素，    无子元素

```
<hr>、<br>、<wbr>、<img>、<embed>、<param>、<source>、<track>、<area>、
<col>、<keygen>
```

 下列元素，    子元素可以没有、可以是`<li>`元素，也可以是`<script>`、`<template>`元素

```
<ol>、<ul>
```

 下列元素，    子元素可以没有、可以是`<dt>`和`<dd>`元素，也可以是`<script>`、`<template>`元素

```
<dl>
```

 下列元素，    子元素可以没有，可以是`<option>`、`<optgroup>`，也可以是`<script>`、`<template>`元素

```
<select></span>
```

 下列元素，    子元素可以没有，可以是`<option>`，也可以是`<script>`、`<template>`元素

```
<optgroup>
```

 下列元素，    子元素可以没有、可以是`<option>`元素

```
<datalist>
```

 下列元素，    子元素可以没有、也可以是`<track>`元素，也可以是`<source>`元素

```
<audio>、<video>
```

 下列元素，    子元素可以没有，也可以是`<col>`、`<template>`元素

```
<colgroup>
```

 下列元素，    子元素可以没有，可以是`<tr>`，也可以是`<script>`、`<template>`元素

```
<tbody>、<thead>、<tfoot>
```

 下列元素，    子元素可以没有，可以是`<tr>`、`<th>`，也可以是`<script>`、`<template>`元素

```
<tr>
```



## 表格元素 

下列元素，子元素是`<caption>`、`<colgroup>`、`<thead>`、`<tfoot>`、`<tbody>`，也可以是`<script>`、`<template>`元素

```
<table>
```



 

##  下列元素，  子元素是文本内容

```
<textarea>
```

 下列元素，  子元素可以没有，也可以是文本内容

```
<option>
```

