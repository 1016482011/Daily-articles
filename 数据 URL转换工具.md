# 数据 URL 转换工具

原文章：[Data URI conversion tools](https://catalin.red/data-uri-tools/)

如今，图片编码已经不是什么神秘的事情了，并且我们也正在使用 数据 URL 来获得更小的图片体积，例如 SVG 甚至字体。总之，这只是用来压缩 HTTP 请求的最佳的方法之一。这里，我列出了一些你可以立马使用在线转换工具的清单。

## 何时图片使用编码？

任何你觉得值得使用的时候。虽然转码有很多优势，但是你不要忘记他也有弊端。我认为这取决于你的内容是否经常被使用。另外，内容较大是应当避免使用，除非你想你的代码中存在着一大块无法解释的代码。

## 数据 URL 语法

```
data:[<MIME-type>][;charset=<encoding>][;base64],<data>
```

下面是一个小的、编码过的 SVG 图像:

```html
<img src="data:image/svg+xml;base64,PD94bWwgd..." />
```

如果我们分开解析其内容:

- data，编码的名称
- image/svg+xml，内容类型
- base64，编码类型
- PD94bWwgd...，编码数据

## 一些 base64 的编码工具

我不打算在这里解释 数据 URL 了，因为其他人在此之前已经做过相关讲解并且做得更好。此外，我还列出了一些在线转换工具，它们都可以正常将你的内容编码为 base64 字符串，以便您可以在项目中立即使用它。

- [b64.io](http://b64.io/)
- [AskApache’s Base64 encoder/decoder](https://www.askapache.com/online-tools/base64-image-converter/)
- [Image2Base64](http://image2base64.wemakesites.net/)
- [duri.me](https://duri.me/)
- [dataurl.net](https://dataurl.sveinbjorn.org/#dataurlmaker)

## 使用 Grunt

如果你正在使用像 Grunt 这样如今常见的自动构建工具，你能够更平滑的进行转换。例如，将你样式中的图片地址转换为数据 URL，你可以很方便的使用插件来完成这件事。

## 你该如何选择

随着字体图标的传播，在你的项目中使用字体图标几乎已经是强制性的了，菜单图标、社交图标甚至一些特殊的列表，他们都可以使用字体图标来实现。最酷的是，能“解决所有问题”的字体图标也是可以进行编码的，以一个数据 URL 字符串嵌入到你的项目之中。只要你不需要兼容老版本浏览器的话。
