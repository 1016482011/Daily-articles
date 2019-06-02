# 什么是语义化 HTML 标签以及如歌使用它们

原文章：[WHAT ARE SEMANTIC HTML TAGS AND HOW TO USE THEM](https://www.developerdrive.com/2019/03/what-are-semantic-html-tags/)

语义化的 HTML 标签允许你赋予标签含义，这样搜索引擎、屏幕阅读器以及浏览器理解其含义。默认情况下，当用户代理读取到你的内容时，它并不理解其上下文以及含义。语义化标签可以让你为用户提供结构化的服务，尤其对于页面的 SEO 优化和体验更好。

虽然语义化早在 HTML 版本就存在了。HTML5 规范增加了几个新的语义元素-块级和内联元素都有。在这篇文章里，我们将探究 HTML 元素是如何工作的，并且就如何充分的使用这些特性给出一些建议。

## 语义化 VS 非语义化 HTML 元素

HTML 标记有两种元素组成：语义化和非语义化的，他们两个都有着自己的含义。当你想在文档结构中使用有具体含义的内容时，应当使用语义化标签。例如，`<ul>`和`<li>`都是语义化的元素，他们代表着列表元素以无序的方式排列。

- `<html>`存放整个网页。
- `<head>`包含了渲染页面的必要信息。
- `<body>`存放整个页面的内容。

这三个语义元素组成了每个 HTML 文档的主干。除了他们，这里还有几个最常用的语义化元素，所有这些都是由早期的 HTML 规范定义的:

- `<ul>`、`<ol>`、和`<li>`用来定义有序和无序列表。
- `<p>`定义段落。
- `<table>`定义表格。
- `<form>`定义表单。
- `<img>`定义图片。
- `<h1>`、`<h2>`、`<h3>`、`<h4>`、`<h5>`、`<h6>`定义不同层级的头。

上面的一些元素还有补充标签，那些也是语义化。例如：`<table>`有`<thead>`、`<tbody>`和`<tfoot>`，这些分别用来定义表单的头、内容和尾部内容。

## HTML5 中的块级语义化元素

HTML5 添加了几个新的语义化标签。开发人员最喜欢那些允许他们创建语义化内容块的元素，块级语义标签主要有两种类型:

1. 分段元素在 HTML 文档中创建了一个独立的区域，它们的内容在文档大纲中被视为单独的块，所以他们可以有自己的标题和页脚标签。其中有四个是:

- `<article>`用于自包含的块，例如博客和文章。
- `<aside>`用于侧边栏。
- `<nav>`用于导航。
- `<section>`用于内容的主干。

2. 语义流元素具有语义含义，但不会在文档中创建独立的块，因此他们也没有他们自己的头和尾元素。这些元素有很多，但我们只看最常用的几个：

- `<header>`用于内容的主要部分。(页面之中只能使用一次)
- `<header>`用于页面或分段元素的标题部分
- `<footer>`用于页面或分段元素的页脚部分
- `<audio>`用于音频嵌入
- `<video>`用于视频嵌入
- `<figure>`用于块级图像

理解分段元素如何工作是十分重要的，让我们来看一个代码例子。

```html
<html>
   <head></head>
   <body>
      <header>
          <h1>Page Title</h1>
          <h2>Page Subtitle</h2>
      <header>
      <main>
          <article>
             <header>
                 <h1>Article Title (1)</h1>
                 <h2>Article Subtitle (1)</h2>
             </header>
             ...
             <footer></footer>
          </article>
          <article>
             <header>
                 <h1>Article Title (2)</h1>
                 <h2>Article Subtitle (2)</h2>
             </header>
             ...
             <footer></footer>
          </article>
          <section>
             <header>
                 <h1>Section Title</h1>
                 <h2>Section Subtitle</h2>
             </header>
             ...
             <footer></footer>
          </section>
      </main>
      <footer>...</footer>
   </body>
</html>
```

正如你所见，文档的`<header>`包含一个`<h1>`和`<h2>`标签用来展示整个页面的标题和副标题。他也有一个`<footer>`，就在 body 的闭合标签之前，用于页面页脚相关的内容，如版权。

但是，每个`<article>`和`<section>`标签也都有他自己的`<header>`和`<footer>`元素。每一个`<header>`标签都包含一个他自己的`<h1>`和`<h2>`头用来展示他们自己的标题和副标题。

每一个分段元素将被视为一个小型文档，具有自己的大纲。如果你把上面的代码例子在浏览器打开，大概样式如下:

![semantic-html5-tags-outline](/img/SemsntivHTMLTags/semantic-html5-tags-outline.jpg)

## 文章大纲

使用语义化 HTMl 标签的终极意义就是能够让用户代理像浏览器、屏幕阅读器和搜索爬虫能够容易的读取并理解。要达到这个目的，你需要明智的使用语义化和非语义化标签。

## 下一步

简而言之，这就是 HTML 的语义化!创建语义文档需要多一些考虑，但它对用户意义重大，还可以改进 SEO 结果。
