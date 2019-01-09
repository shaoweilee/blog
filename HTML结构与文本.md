# HTML结构与文本

## 元素分类

### 根元素

 <html> 元素 表示一个HTML文档的根（顶级元素），所所以它也被称为根元素。其他所有其他元素必须是此元素的后代。

### 文档元数据

<link><meta><style><title>

### 内容分区

<address>可以让作者为<article>或者<body>祖先元素提供联系信息。

<article>表示文档、页面、应用或网站中的独立结构，其意在成为可独立分配的或可复用的结构。可能是论坛帖子，杂志或新闻文章，博客、用户提交的评论、交互式组件，或者其他独立的内容项目。

<aside>表示和页面的其余内容几乎无关的部分，独立于内容，可以被拆分而不会使整体受影响。通常表现为侧边栏或嵌入内容。也可能有其他类型的信息，例如相关的广告、笔者的传记、web 应用程序、个人资料信息，或在博客上的相关链接。

<footer>表示最近一个章节内容或者根节点元素的页脚。一个页脚通常包含该章节作者、版权数据或者与文档相关的链接等信息。

<header>表示一组引导性的帮助，包含标题元素，logo，分节头部，搜索表单等。

<h1>至<h6>标题元素能够简要描述该节主题。标题信息可以由用户代理使用，例如自动构造文档中的内容表。
<hgroup>代表一个段的标题。。。说实话，真的看不懂。

<nav>导航栏，一个含有多个超链接的区域。

<section>表示文档中的一个区域或章节，

### 文本内容

blockquote

div

dl

dt

dd

figure

figcaption

hr

main

p

pre

ul

ol

li

### 内联文本语义

a

abbr

b

bdi

bdo

br

cite

code

data

dfn

em改变语气，改变句子意思

i

kbd

mark

q

rp

rt

rtc

ruby

s

samp

small

span

strong对一个句子的部分增加重要性

sub

sup

time

u

var

wbr

### 图片和多媒体

area

audio

img

map

track

video

### 内嵌内容

embed

iframe

object

param

picture

source

### 脚本

canvas

noscript

script

### 编辑标识

del

ins

### 表格内容

caption

col

colgroup

table

tbody

td

tfoot

th

thead

tr

### 表单

button

datalist

fieldset

form

input

label

legend

meter

optgroup

option

output

progress

select

textarea

### 交互元素

details

dialog

menu

summary

### web组件

slot

template



## 从HTML4到HTML5的变化：

### HTML4文档结构定义与大纲算法

HTML4用文档中章节和子章节的概念去描述文档结构。一个章节，由一个包含着<h1>-<h6>的div元素表示。并且一个标题元素的出现就足以意味着新的章节。这些html划分元素（div）和标题元素组成了文档的结构和纲要。所以混乱的一笔。

### HTML4文档结构定义和大纲算法带来的问题

- div是不是大纲的一部分？定义的是章节还是子章节？——针对这个问题，HTML5自动生成大纲的算法去掉了div，添加了section；
- 文档嵌套很麻烦，因为层级是标题级别决定的。——针对这个问题，HTML5添加了article，section，nav，aside，标题作为作为章节title显示在大纲中，而不是决定层级；
- 所有章节都是文档大纲的一部分，但有些章节是文档大纲不乐意要的，比如广告块和解释区域。——针对这个问题，HTML5引入了aside；
- HTML4无法产生与网站相关而不是与文档相关的节段，比如logo，menus，目录或版权信息和法律声明。——针对这个问题，HTML5引入了header，footer，nav。

### HTML5定义的大纲算法

#### 显式定义章节：

HTML5通过body，section，article，aside，nav来**显式定义章节**，章节是可以嵌套的。

需要注意的是，每个section都可以有自己的标题结构，嵌套的也可以有<h1>，但是里面的<h1>，和外面的<h1>，浏览器默认字体大小是不一样的。看下图：

![image-20180419143648363](/var/folders/qq/5n8dv0z50nvcx8bp0n5z71gm0000gn/T/abnerworks.Typora/image-20180419143648363.png)



![image-20180419143729480](/var/folders/qq/5n8dv0z50nvcx8bp0n5z71gm0000gn/T/abnerworks.Typora/image-20180419143729480.png)

![image-20180419143805128](/var/folders/qq/5n8dv0z50nvcx8bp0n5z71gm0000gn/T/abnerworks.Typora/image-20180419143805128.png)

#### 隐式定义章节：

为了与HTML4保持兼容，还有隐式定义章节的方法，而这种方法不需要分节元素。

当一个标题元素（h1-h6）不是父章节的第一个标题时（因为第一个标题被用作了父章节的标题），这个标题就定义了一个新的章节。有以下三种情况：

1. 如果比之前的标题级别低，那么就建立一个新的子章节；

   ```html
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

2. 如果与前面标题的级别相同，那么就闭合前面的章节（有可能闭合一个显式定义的章节！），然后开始一个新的同一级别的隐式章节；

   ```html
   <section>
     <h1>Forest elephants</h1>  
     <p>In this section, we discuss the lesser known forest elephants.
       ...this section continues...
     <h1 class="implicit section">Mongolian gerbils</h1>
     <p>Mongolian gerbils are cute little mammals.
       ...this section continues...
   </section>

   1. Forest elephants
   2. Mongolian gerbils (implicitly defined by the h1 element, which closed the previous section at the same time)
   ```

   ​

3. 如果比之前的标题级别更高，那么关闭之前的章节，并开始一个更高级别的隐式章节。

   ```html
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
         <p>Hordes of gerbils have spread their range far beyond Mongolia.
            ...this subsection continues...
       <h2>Reptiles</h2>
         <p>Reptiles are animals with cold blood.
             ...this subsection continues...
     </section>
   </body>

   1. Mammals
      1.1 Whales (implicitly defined by the h2 element)
      1.2 Forest elephants (explicitly defined by the section element)
      1.3 Mongolian gerbils (implicitly defined by the h3 element, which closes the previous section at the same time)
   2. Reptiles (implicitly defined by the h2 element, which closes the previous section at the same time)
   ```

   ​

#### 分节根

分节根元素不进祖先大纲，但是有自己的大纲，然而内部的章节和标题不参与祖先的大纲。如：blockquote，details，fieldset，figure，td。

#### 大纲之外的章节

HTML5的aside和nav定义了大纲之外的章节。

这俩元素参与祖先大纲，但是元素内部的大纲不参与祖先大纲。

#### 页眉页脚

header。通常包含logo和站点名称以及水平菜单。或一个章节的头部，可能包含章节标题等。article，section，aside，nav都可以有自己的header。虽然名字是header，但是不一定在页面的开始位置。

footer同上。



## 在不支持HTML5中的浏览器中使用HTML5

一句CSS搞定：

```css
section, article, aside, footer, header, nav, hgroup {
  display:block;
}
```

但是ie8以及ie8之前的浏览器不支持对未知元素进行样式化，需要特殊脚本，如果禁用了脚本，那也应该给予提示：

```html
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
     <strong>Warning !</strong>
     Because your browser does not support HTML5, some elements are simulated using JScript.
     Unfortunately your browser has disabled scripting. Please enable it in order to display this page.
  </noscript>
<![endif]-->
```



https://developer.mozilla.org/en-US/docs/Web/HTML/Element

在HTML里，我们见到的更多是inline-level elements，block-level elements；而在HTML5里，更多的是semantic 标签，non-semantic 标签，以及sectioning elements。

- **header:**<header>
- **navigation bar:** <nav>
- **main content:** <main>, with various content subsections represented by<article>,<section> and<div>elements.
- **sidebar:** <aside>, often placed inside <mian>
- **footer:** <footer>.

更详细地：

- <main> is for content unique to this page. Use <main> only once per page, and put it directly inside <body>. Ideally this shouldn't be nested within other elements.
- <article> encloses a block of related content that makes sense on its own without the rest of the page (e.g. a single blog post.)
- <section> is similar to <article>, but it is more for grouping together a single part of the page that constitutes one single piece of functionality (e.g. a mini map, or a set of article headlines and summaries.) It's considered best practice to begin each section with a heading; also note that you can break <article>s up into different <section>s, or <section>s up into different <article>s, depending on the context.
- <aside> contains content that is not directly related to the main content but can provide additional information indirectly related to it (glossary entries, author biography, related links, etc.)
- <header> represents a group of introductory content. If it is a child of <body> it defines the global header of a webpage, but if it's a child of an <article> or <section> it defines a specific header for that section (try not to confuse this with titles and headings.)
- <nav> contains the main navigation functionality for the page. Secondary links, etc., would not go in the navigation.
- <footer> represents a group of end content for a page.