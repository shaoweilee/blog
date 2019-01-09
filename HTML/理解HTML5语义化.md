我对HTML5语义化的理解可以归纳为以下几点：

1. 整体理解，什么是语义，什么是HTML5语义，为什么要做语义化；
2. 了解各个HTML5标签的语义；
3. 了解该用什么标签，宏观布局与标签嵌套；
4. 增强页面易访问性；

# 语义

我们所面临的第一个问题就是，什么是语义？

在程序中，语义，指的是一段代码的含义，例如“运行这行代码会产生怎样的影响？”在HTML5的世界中，语义指的是“这个HTML元素有什么作用，扮演了什么样的角色？”

# HTML5各标签的语义

在这里，我推荐张鑫旭[这篇文章](http://www.zhangxinxu.com/wordpress/?p=1544)，详细记录了HTML5各个标签的语义。不过浏览器的支持状况那一节，还是去[Can I Use](https://caniuse.com/)上查找比较好。



# 为什么要做语义化？

一是因为HTML4的大纲算法太烂了。二是开发者太喜欢`<div>`了。o(╥﹏╥)o。

  如果要确定文档大纲，就必须确定文档中的章节是哪一章，哪一节。  在HTML4中，文档结构由章节和子章节的概念来描述。假如一个 `<div>`包含着 `<h1>`~`<h6>`中任意一个标题标签，那么这个 `<div>`就构成了一个章节。文档结构全部由`<div>`和标题标签来表示，带来了如下问题：

1.   `<div>`  是不是大纲的一部分？  定义的是章节还是子章节？  ——针对这个问题，HTML5自动生成大纲的算法去掉了`<div>`，添加了`<section>`； 
2.   章节嵌套很麻烦，因为层级是标题级别决定的。——针对这个问题，HTML5添加了`<article>`，`<section>`，`<nav>`，`<aside>`，标题作为作为章节title显示在大纲中，而不是决定层级； 
3.   所有章节都是文档大纲的一部分，但有些章节是文档大纲不乐意要的，比如广告块和解释区域。——针对这个问题，HTML5引入了`<aside>`； 
4.   HTML4无法产生与网站相关而不是与文档相关的节段，比如logo，menus，目录或版权信息和法律声明。——针对这个问题，HTML5引入了`<header>`，`<footer>`，`<nav>`。

HTML5通过`<body>`，`<section>`，`<article>`，`<aside>`，`<nav>`来**显式定义章节**。另外，为了与HTML4保持兼容，仍然可以通过标题标签隐式定义章节，并且不需要分节元素。  当一个标题元素（h1-h6）不是父章节的第一个标题时（因为第一个标题被用作了父章节的标题），这个标题就定义了一个新的章节。有以下三种情况：

-   如果比之前的标题级别低，那么就建立一个新的子章节：

```
<section>
  <h1>Forest elephants</h1>  
  <p>In this section, we discuss the lesser known forest elephants.
    ...this section continues...
  <h3 class="implicit subsection">Habitat</h3>
  <p>Forest elephants do not live in trees but among them.
    ...this subsection continues...
</section>

1. Forest elephants
   1.1 Habitat (implicitly defined by the h3 element)
```



-   如果与前面标题的级别相同，那么就闭合前面的章节（有可能闭合一个显式定义的章节！），然后开始一个新的同一级别的隐式章节：

```
<section>
  <h1>Forest elephants</h1>  
  <p>In this section, we discuss the lesser known forest elephants.
    ...this section continues...
  <h1 class="implicit section">Mongolian gerbils</h1>
  <p>Mongolian gerbils are cute little mammals.
    ...this section continues...
</section>

1. Forest elephants
2. Mongolian gerbils (implicitly defined by the h1 element, which 
   closed the previous section at the same time)
```



-   如果比之前的标题级别更高，那么关闭之前的章节，并开始一个更高级别的隐式章节：

```
<body>
  <h1>Mammals</h1>
  <h2>Whales</h2>
  <p>In this section, we discuss the swimming whales.
    ...this section continues...
  <section>
    <h3>Forest elephants</h3>  
    <p>In this section, we discuss the lesser known forest elephants.
      ...this section continues...
    <h3>Mongolian gerbils</h3>
      <p>Hordes of gerbils spread their range far beyond Mongolia.
         ...this subsection continues...
    <h2>Reptiles</h2>
      <p>Reptiles are animals with cold blood.
          ...this subsection continues...
  </section>
</body>

1. Mammals
   1.1 Whales (implicitly defined by the h2 element)
   1.2 Forest elephants (explicitly defined by the section element)
   1.3 Mongolian gerbils (implicitly defined by the h3 element, which
       closes the previous section at the same time)
2. Reptiles (implicitly defined by the h2 element, which closes the 
   previous section at the same time)
```





# 我该用什么标签？

这里，我认为又包含了两个问题，一个是在做布局的时候，宏观上该用哪些标签构建布局？另一个是，标签嵌套规则。

先来回答第一个问题。现在，网页宏观上的布局已经推荐使用`<nav> <article> <sections> <aside> <footer>`等来构建骨架了。这里有一些宏观上的建议，指明某些标签的用法：

1.  `<nav>`：标记导航（网页中重要的链接群就需要用这个标签）。包含在`<nav>`中间的通常是`<ul>`无序列表。 但不推荐对有辅助性的页脚链接使用 `<nav>`； 
2.  `<main>`：标记对页面来说独一无二的内容，在每个页面中只能用一次，而且作为`<body>`的直接子元素，不应该嵌套在任何其它标签中；
3.  `<header>`：表示一组引导性的帮助，包含标题元素、logo、搜索表单、 `<nav>`  等。
4.  `<article>`：文章标记标签（表示的是一个文档、页面、应用或是网站中的一个独立的容器，原则上来讲就是聚合）；
5.  `<section>`：区块定义标签（表示的是文档或应用的一个一般的区域或章节），它一般是有一组相似的主题的内容，一般会包含一个标题。可以用这个标签来写：文章的章节、标签式对话框中的各种标签页等等类似的功能；
6. 区分 `<article>`  和 `<section>`： 如果页面中需要一个单独的模块来实现一个单独的功能，也就是高聚合的情况，就用`<article>`，其他的时候都用`<section>`；
7.  `<aside>`：定义侧栏标签（表示一部分内容与页面的主体并不是有很大的关系，但是可以独立存在），用他可以实现：侧栏、相关文章的链接框、广告、友情链接等等。 
8.  `<footer>`：页脚标签（与`<header>`标签对应的标签），用他可以实现的功能有：附录、索引、版权页、许可协议等。 
9.  `<figure>`：用来引入图表、图形、照片等，就像是杂志中的图片。可以包含 多个内容块，但只能有一个 `<figcaption>`（可以理解成给图表加标题）标签；  
10.  `<address>` 标签中不可嵌套  `<h1>`~`<h6>`、`<article>`、`<address>`、`<aside>`、`<footer>`、`<header>`、`<hgroup>`、`<nav>`、`<section>` ；
11.  `<div>`：如果有比 `<div>`更合适的标签，就不要用 `<div>`。如果效果需要，可以在HTML5标签外加一个 `<div>`标签，这样对HTML语义不会产生太大影响。

还有一些细节上的建议：  

 

1. 标题使用`<h1>` (`<h2>`, `<h3>`…)，列表使用`<ul>`或者`<ol>`；

2.  `<article>`内容中的标题应该从`<h1>`开始；

3. 正文中的文本内容要用`<p>`标签，内容的结构化可以使用HTML5的新元素（或者`<div>`），不要颠倒；

4.  不要滥用`<div>`，W3C对`<div>`的描述是这样的：当没有其他元素可用时才能使用`<div>`。如果想让`<link>`和`<img>`这类元素能够在结尾换行，可以在样式中添加`display: block`，这样要比把它们放进`<div>`或者使用`<br>`来换行要好得多；  

5. `<em>`和`<strong>`要比`<i>`和`<b>`更好些，因为前者语义更加明显。 `<em>`强调语气， `<strong>`强调文本重要；

6.  `<form>`中要包含`<label>`元素；

7. 不要把文字和元素混合在一起，这样容易导致布局出错，比如这样：

   ```
   <div>
       Name: 
       <input type="text">
   </div>
   ```

   这样写会更好些：

   ```
   <div>
       <label>Name:</label>
       <input type="text">
   </div>
   ```

## 标签嵌套

详细规则请查看[这里](http://www.herosanctuary.com/detail/1540991027616)。记住是不大可能，有疑问的时候，可以随时查阅，用着用着就熟了。

# 加强易访问性

上面做的还远远不够，我们还要加强诸如键盘访问、低网速访问、残障人士访问的易访问性。所幸的是，都有针对的、或通用的解决方案。

## 键盘易访问性

如果要提升键盘易访问性，那么要考虑到：

- 页面元素需要可以用table键访问到——需要用到[tabindex](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/tabindex)属性，使用起来非常非常简单；
- 某些元素行为，可以用回车键激活，查看[示例](https://codepen.io/shaoweilee/pen/XxLqeM?editors=1010)。

## 低网速易访问性

在低网速情况下，例如网页加载不出图片、CSS样式，那就需要为元素设置替代性文本。比如：

- 重要图片`<img>`标签的`alt`属性不能为空值；
- 使用语义化标签，因为某些标签是自带样式的。

## 残障人士易访问性

实际上，这块内容非常大，因为牵扯到了无障碍访问。张鑫旭大大曾写过文章专门[记录](http://www.zhangxinxu.com/wordpress/?p=2277)和[实战](http://www.zhangxinxu.com/wordpress/?p=5893)。

来个实例，本网站主页和文章页的搜索框旁的放大镜按钮，其实是用`<a>`链接模拟实现的，其实，按钮使用`<button>`是语义化和可访问性最好的，但是 `<button>`丑啊。

那么问题来了，用了 `<a>`标签之后，屏幕阅读器会把它读作“链接 ”，而不是“按钮 ”，又因为它不包含阅读器认识的文本，所以阅读器读了 “链接 ”之后就不干事儿了，有非常大的误导性。但是，可以通过简单的ARIA无障碍属性弥补这一点：

首先，让阅读器把它识别为按钮：

```
<a role='button'>...</a>
```

其次，让阅读器读出它的意义：

```
<a role='button' aria-label='按下回车发起搜索'>...</a>
```

此时，阅读器就会读出“按下回车发起搜索，按钮”。

这样就完成了最最基本的屏幕阅读器易访问性。

# 最后的最后，兼容

如何在不支持HTML5的浏览器中使用HTML5？一行CSS就能搞定：

```
section, article, aside, footer, header, nav, hgroup {
  display:block;
}
```

另外，IE8及IE8以下的浏览器，

不支持对未知元素进行样式化，需要特殊脚本，如果禁用了脚本，那也应该给予提示：

```
<!--[if lt IE 9]>
  <script>
    document.createElement("header" );
    document.createElement("footer" );
    document.createElement("section"); 
    document.createElement("aside"  );
    document.createElement("nav"    );
    document.createElement("article"); 
    document.createElement("hgroup" ); 
    document.createElement("time"   );
  </script>
  <noscript>
    <strong>警告!</strong>
    你的浏览器不支持HTML5，一些元素用JS模拟了。
    但你的浏览器禁用了脚本，为了正常显示页面，请启用脚本。
  </noscript>
<![endif]-->
```

最后，也可以借助`html5shiv.js`来做统一处理，更为方便。

