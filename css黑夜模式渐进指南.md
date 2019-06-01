# css 黑夜模式渐进指南

原文章：[A STEP-BY-STEP GUIDE TO DARK MODE IN CSS](https://www.developerdrive.com/2019/05/css-dark-mode/)

在 CSS 中启用黑夜模式允许用户随时切换到对视力友好和省点模式。我将给你展示如何用 jQuery 在页面顶部添加一个易于使用的黑夜模式的转换按钮。

我们例子兼顾了响应式，所以他在所有的尺寸的屏幕上都看起来很好，从手机到电脑或者更大的屏幕。这里是正常模式的最终成果图：

![dark-mode-in-css-light](/img/CssDarkGuide/dark-mode-in-css-light.jpg)

下面是同一页面的黑暗模式版本:

![dark-mode-in-css-dark](/img/CssDarkGuide/dark-mode-in-css-dark.jpg)

现在，我们开始来确定 HTML、CSS 和 JS 文件的目录结构

## 创建目录结构

创建一个文件夹，并在其中创建三个空文本文件:一个带有.html、一个带有.css 和一个带有.js 扩展名的文件。还要为要在页面上显示的图像创建一个图像文件夹。

我们的“CSS 中的黑夜模式”例子中也使用了 jQuery。在本例中，我们直接从 Cloudflare CDN 引用到到 HTML 文件中，保证它始终是最新的。不过，如果您乐意，还也可以下载 jQuery 库并将其添加为本地.js 文件。

在开始编码之前，下面展示了文件目录结构：

```
- dark-mode-css/
   - images/
   - cake.jpg
   - index.html
   - script.js
   - style.css
```

## 编写 HTML

首先把黑夜模式切换按钮添加到页面的顶部。然后，创建一个`<h1>`标签放置标题和一个语义化的`<srticle>`标签放置内容。最终，添加两个`<script>`标签在`</body>`闭合之前。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Demo | Dark Mode in CSS</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div class="switch">
      Dark mode:
      <span class="inner-switch">OFF</span>
    </div>
    <h1 class="title">The Best Recipes of All Time</h1>
    <article>
      <h1>Raspberry Cake</h1>
      <p><small>Created with Cupcake Ipsum</small></p>
      <p>...</p>
      <img src="images/cake.jpg" />
      <p>...</p>
    </article>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.0/jquery.min.js"></script>
    <script src="script.js"></script>
  </body>
</html>
```

## 创建正常模式下的 css

首先，我们先来编写正常模式下的 css 编码，作为页面的默认样式。下面的 CSS 使用基于列的 flexbox 布局，使您能够轻松地在页面上定位元素，特别注意`.switch`这个 class，这个是用来处理模式切换按钮和`<img>`元素的。

```css
body {
  font-family: sans-serif;
  font-size: 1.125rem;
  display: flex;
  flex-direction: column;
  max-width: 50rem;
  margin: 0 auto;
  padding: 0 0.9375rem;
}
small {
  font-style: italic;
}
article {
  display: flex;
  flex-direction: column;
}
img {
  max-width: 100%;
  display: block;
  align-self: center;
}
.switch {
  align-self: flex-end;
  margin: 0.9375rem;
}
.inner-switch {
  display: inline-block;
  cursor: pointer;
  border: 1px solid #555;
  border-radius: 1.25rem;
  width: 3.125rem;
  text-align: center;
  font-size: 1rem;
  padding: 0.1875rem;
  margin-left: 0.3125rem;
}
```

## 使用 jQuery 添加切换功能

在`script.js`中添加切换功能函数，这样，当用户点击切换开关时，就能应用到黑夜模式，并且按钮显示为`开`，并且，如果用户在黑夜模式下点击页面，将会切换到正常模式下，按钮显示为`关`。

```js
$('.inner-switch').on('click', function() {
  if ($('body').hasClass('dark')) {
    $('body').removeClass('dark')
    $('.inner-switch').text('OFF')
  } else {
    $('body').addClass('dark')
    $('.inner-switch').text('ON')
  }
})
```

## 添加黑夜模式的代码

我们要做的最后一件事就是为`.drak`类定义一些样式，并且将这个 class 添加到 html 中以供 jQuery 使用。

```css
.dark,
.dark * {
  background-color: #222;
  color: #e6e6e6;
  border-color: #e6e6e6;
}
```

如果你编写的页面过于复杂，你可以将黑夜主题单独放到`dark.css`文件下。

## 查看在线的例子

以上就是所有的内容，黑夜模式功能已经能够正确的运作了。如果你想查看他在网站上是如何运作的，可以点击这里[Dark Mode in CSS](https://www.annalytic.com/demos/dark-mode-css/)。

## 总结

CSS 中的黑夜模式不仅为您的站点增加了一个很好的额外功能，而且还提高了用户体验和好感。在内容篇幅较长或要求用户长时间盯着屏幕的网站上，黑夜模式的切换尤其有用。

如果你不知道如何选择黑夜模式的颜色，并且想得到一些帮助时，你可以查看我们的[这篇](https://www.developerdrive.com/2014/06/the-coders-guide-to-finding-the-perfect-color-palette/)文章
