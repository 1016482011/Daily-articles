# vue render 函数的的使用案例：构建系统视图

原文章：[A Practical Use Case for Vue Render Functions: Building a Design System Typography Grid](https://css-tricks.com/a-practical-use-case-for-vue-render-functions-building-a-design-system-typography-grid/)

这篇文章讲述了我是如何使用 [vue render 函数](https://vuejs.org/v2/guide/render-function.html)来进行设计系统排版布局的构建，这里是[例子](https://9oj1v5qlqp.codesandbox.io/)和[代码](https://codesandbox.io/embed/9oj1v5qlqp)。我才用 render 函数的原因在于他创建的 html 比 vue 的模板拥有更高的控制级别，然而令我感到惊讶的是，我在网上并没有搜索到很多关于这种方法在现实开发和程序中使用的资料。我希望这篇文章能填补这方面的空白，并且可以提供一个使用 vue render 函数的实际案例。

我一直觉得 render 函数和 vue 有那么点不合拍。在框架的其他部分强调简洁和分离的情况下，render 函数就表现的比较奇怪并且通常 html 和 JavaScript 混合的写法也难以阅读。

举个例子，如下所示：

```html
<div class="container">
  <p class="my-awesome-class">Some cool text</p>
</div>
```

你需要写成。。。：

```js
render(createElement) {
  return createElement("div", { class: "container" }, [
    createElement("p", { class: "my-awesome-class" }, "Some cool text")
  ])
}
```

我想这种语法可能会让一些人感到厌烦，因为选用 vue 的首要因素就是他的易用性。可惜的是，render 函数是和组件是用来实现一些非常酷和功能强大的页面，因而缺乏易用性。为了证明他们的价值，这里有一个 render 函数解决我的实际问题的例子。

## 为设计系统制定标准

我的团队想在我们 vuepress 驱动的设计系统中添加一个页面，以不同的不同的排版展示。这是我从设计师那得到的模型的一部分。

![vue-ds-01_uq9urm](/img/VueRenderFunctions/vue-ds-01_uq9urm.webp)

这里是一些相应的简单的 CSS：

```css
h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: 'balboa', sans-serif;
  font-weight: 300;
  margin: 0;
}

h4 {
  font-size: calc(1rem - 2px);
}

.body-text {
  font-family: 'proxima-nova', sans-serif;
}

.body-text--lg {
  font-size: calc(1rem + 4px);
}

.body-text--md {
  font-size: 1rem;
}

.body-text--bold {
  font-weight: 700;
}

.body-text--semibold {
  font-weight: 600;
}
```

标题使用的的是标签名。其他部分使用的是类名，并且字体粗细和大小有分离出来的类名。

在写任何代码之前，我制定了一些基本准则：

- 数据存放应当使用单独分离的文件。
- 标题应当使用语义化的标题标签（例如，`<h1>,<h2`>等等）
- 内容部分应该使用段落标签（`<p>`）并且使用类名（例如：`<p class="body-text--lg">`）。
- 具有不同类型的内容应该一起放在段落标签的更目录下，或者相应的根节点下，并且不可带有样式类。子元素应当使用带有类名的`<span>`包装。

```html
<p>
  <span class="body-text--lg">Thing 1</span>
  <span class="body-text--lg">Thing 2</span>
</p>
```

- 任何没有具体展示含义和特殊含义的内容应当使用带有合适类名的段落标签，并且子节点放在`<span>`内。

```js
<p class="body-text--semibold">
  <span>Thing 1</span>
  <span>Thing 2</span>
</p>
```

- 对于每个样式的类名应当只写一次。

## 为什么 render 函数适合

在开讲之前我考虑了如下几个方法：

### 硬编码

我想硬编码总是可以实现的，但是手写 HTML 意味这我需要输入不同的标签组合，这似乎一个重复和不愉快的工作。这也意味着我们的数据无法存放到分离的文件里去，所以我放弃了这个方法。

我的代码如下：

```html
<div class="row">
  <h1>Heading 1</h1>
  <p class="body-text body-text--md body-text--semibold">h1</p>
  <p class="body-text body-text--md body-text--semibold">Balboa Light, 30px</p>
  <p class="group body-text body-text--md body-text--semibold">
    <span>Product title (once on a page)</span>
    <span>Illustration headline</span>
  </p>
</div>
```

### 使用传统的 vue 模板

这个通常是一个首选项。但是，考虑如下情况：

![20190531180307](/img/VueRenderFunctions/20190531180307.png)

在第一列，我们有：

- 一个 `<h1>`标签渲染成原生样式。
- 一个 `<p>`组合一些带有文字的`<span>`子元素标签，每一个都带有一个样式（不过`<p>标签上没有样式`）。
- 一个带有类名的`<p>`标签并且没有子元素。

要达到这个结果就意味着需要很多的`v-if`和`v-if-else`，我很清楚这样很快就会搞混。我也不喜欢去记这些判断逻辑。

综上这些原因，我选择函数渲染。渲染函数使用 js 去基于一个已有的定义规则分条件的创建子元素，完美适配当前情况。

## 数据模型

正如我之前所提到的，我更喜欢采用分离的 JSON 文件来保存布局的数据，这样以后我就可以很方便的做一些修改，而不需要去修改标签。这里是[原始数据](https://codepen.io/soluhmin/pen/ROMXOb.js)。

文件中的每个对象表示不同的行：

```json
{
  "text": "Heading 1",
  "element": "h1", // Root wrapping element.
  "properties": "Balboa Light, 30px", // Third column text.
  "usage": ["Product title (once on a page)", "Illustration headline"] // Fourth column text. Each item is a child node.
}
```

这个上述的对象将会渲染成如下 HTML：

```html
<div class="row">
  <h1>Heading 1</h1>
  <p class="body-text body-text--md body-text--semibold">h1</p>
  <p class="body-text body-text--md body-text--semibold">Balboa Light, 30px</p>
  <p class="group body-text body-text--md body-text--semibold">
    <span>Product title (once on a page)</span>
    <span>Illustration headline</span>
  </p>
</div>
```

让我们来看一个更复杂的例子。数组代表着一组子元素。一个`classes`对象能够存放 class 类。`base`属性存放单元组件通用的 class 类。每个 class 中的`variants`将会被应用到组的不同元素当中。

```json
{
  "text": "Body Text - Large",
  "element": "p",
  "classes": {
    "base": "body-text body-text--lg", // Applied to every child node
    "variants": ["body-text--bold", "body-text--regular"] // Looped through, one class applied to each example. Each item in the array is its own node.
  },
  "properties": "Proxima Nova Bold and Regular, 20px",
  "usage": ["Large button title", "Form label", "Large modal text"]
}
```

渲染结果如下：

```html
<div class="row">
  <!-- Column 1 -->
  <p class="group">
    <span class="body-text body-text--lg body-text--bold"
      >Body Text - Large</span
    >
    <span class="body-text body-text--lg body-text--regular"
      >Body Text - Large</span
    >
  </p>
  <!-- Column 2 -->
  <p class="group body-text body-text--md body-text--semibold">
    <span>body-text body-text--lg body-text--bold</span>
    <span>body-text body-text--lg body-text--regular</span>
  </p>
  <!-- Column 3 -->
  <p class="body-text body-text--md body-text--semibold">
    Proxima Nova Bold and Regular, 20px
  </p>
  <!-- Column 4 -->
  <p class="group body-text body-text--md body-text--semibold">
    <span>Large button title</span>
    <span>Form label</span>
    <span>Large modal text</span>
  </p>
</div>
```

## 基本设置

我们有一个父组件-`TypographyTable.vue`，其中包含了存放表格元素的一些标签，和一个子组件-`TypographyRow.vue`，用来创建单元行和存放我们的 render 函数。

我通过循环这个子组件，将行数据作为属性传递给子组件：

```html
<template>
  <section>
    <!-- Headers hardcoded for simplicity -->
    <div class="row">
      <p class="body-text body-text--lg-bold heading">Hierarchy</p>
      <p class="body-text body-text--lg-bold heading">Element/Class</p>
      <p class="body-text body-text--lg-bold heading">Properties</p>
      <p class="body-text body-text--lg-bold heading">Usage</p>
    </div>
    <!-- Loop and pass our data as props to each row -->
    <typography-row
      v-for="(rowData, index) in $options.typographyData"
      :key="index"
      :row-data="rowData"
    />
  </section>
</template>
<script>
  import TypographyData from '@/data/typography.json'
  import TypographyRow from './TypographyRow'
  export default {
    // Our data is static so we don't need to make it reactive
    typographyData: TypographyData,
    name: 'TypographyTable',
    components: {
      TypographyRow
    }
  }
</script>
```

有一点需要指出：这个排版的数据可以作为 vue 实例中的一个属性并通过`$options.typographyData`来使用，因为他不会发生改变也不需要响应。

## 制作功能组件

`TypographyRow`组件是一个接收数据的纯函数组件，纯函数组件是无状态和无依赖的，这研究意味着他没有`this`并且不需有 vue 的生命周期的钩子函数。

这个空的起始组件如下：

```js
// No <template>
<script>
export default {
  name: "TypographyRow",
  functional: true, // This property makes the component functional
  props: {
    rowData: { // A prop with row data
      type: Object
    }
  },
  render(createElement, { props }) {
    // Markup gets rendered here
  }
}
</script>
```

render 方法接受一个上下文参数，该参数具有一个非结构化的 props 属性作为第二个参数。

第一个参数是`createElement`，这个函数告诉 Vue 创建什么节点。为了简洁和惯例，我将`createElement`缩写为`h`。

```js
render(h, { props }) {
  return h("div", { class: "example-class }, "Here's my example text")
}
```

### 数据转换

我希望我的数据的格式能够匹配 h 的参数，但在此之前，我先给出了我想要的数据结构:

```json
// One cell
{
  "tag": "", // HTML tag of current level
  "cellClass": "", // Class of current level, null if no class exists for that level
  "text": "", // Text to be displayed
  "children": [] // Children each follow this data model, empty array if no child nodes
}
```

每个对象代表一个最小单元，四个单元构成一行。

```js
// One row
;[{ cell1 }, { cell2 }, { cell3 }, { cell4 }]
```

起始的函数大致如下：

```js
function createRow(data) {
  // Pass in the full row data and construct each cell
  let { text, element, classes = null, properties, usage } = data
  let row = []
  row[0] = createCellData(data) // Transform our data using some shared function
  row[1] = createCellData(data)
  row[2] = createCellData(data)
  row[3] = createCellData(data)

  return row
}
```

让我们再来回顾一下我们的原型：

![vue-ds-01_uq9urm](/img/VueRenderFunctions/vue-ds-01_uq9urm1.webp)

第一个列的样式有变化，但是其他列似乎遵循相同的模式，所以让我们从这些列开始。

同样，每个单元格的期望的数据模型是:

{
tag: "",
cellClass: "",
text: "",
children: []
}

给我的是一个类似树形的结构，因为每一个最小单元都有一组子元素。我们用两个方法来创建：

- `createNode`接收我们需要的属性作为参数
- `createCell`用来包装`createNode`，这样我们就能检查传入的参数是否是数组。如果是，则创建一个子节点数组。

```js
// Model for each cell
function createCellData(tag, text) {
  let children
  // Base classes that get applied to every root cell tag
  const nodeClass = 'body-text body-text--md body-text--semibold'
  // If the text that we're passing in as an array, create child elements that are wrapped in spans.
  if (Array.isArray(text)) {
    children = text.map(child => createNode('span', null, child, children))
  }
  return createNode(tag, nodeClass, text, children)
}
// Model for each node
function createNode(tag, nodeClass, text, children = []) {
  return {
    tag: tag,
    cellClass: nodeClass,
    text: children.length ? null : text,
    children: children
  }
}
```

现在，我们可以这么干：

```js
function createRow(data) {
  let { text, element, classes = null, properties, usage } = data;
  let row = [];
  row[0] = ""
  row[1] = createCellData("p", ?????) // Need to pass in class names as text
  row[2] = createCellData("p", properties) // Third column
  row[3] = createCellData("p", usage) // Fourth column

  return row;
}
```

我们将`properties`和`usage`作为参数传递给第三和第四行。然而，第二列有些不同;在那里，我们显示 class 名，这些类名存放在这样的数据文件中：

```json
"classes": {
  "base": "body-text body-text--lg",
  "variants": ["body-text--bold", "body-text--regular"]
},
```

另外，请记住标题没有类名，所以我们使用标签名即可。

接着让我们创建一些辅助函数将我们的参数解析成可供我们使用的数据格式。

```js
// Pass in the base tag and class names as arguments
function displayClasses(element, classes) {
  // If there are no classes, return the base tag (appropriate for headings)
  return getClasses(classes) ? getClasses(classes) : element
}

// Return the node class as a string (if there's one class), an array (if there are multiple classes), or null (if there are none.)
// Ex. "body-text body-text--sm" or ["body-text body-text--sm body-text--bold", "body-text body-text--sm body-text--italic"]
function getClasses(classes) {
  if (classes) {
    const { base, variants = null } = classes
    if (variants) {
      // Concatenate each variant with the base classes
      return variants.map(variant => base.concat(`${variant}`))
    }
    return base
  }
  return classes
}
```

现在我们可以这么写：

```js
function createRow(data) {
  let { text, element, classes = null, properties, usage } = data
  let row = []
  row[0] = ''
  row[1] = createCellData('p', displayClasses(element, classes)) // Second column
  row[2] = createCellData('p', properties) // Third column
  row[3] = createCellData('p', usage) // Fourth column

  return row
}
```

现在我们还剩下 demo 样式的第一列。这一列是独特的，因为我们我们要给每个单元使用新的标签和类名，而不是像其他列一样使用类的集合：

```html
<p class="body-text body-text--md body-text--semibold"></p>
```

与其尝试在`createCellData`或者`createNodeData`中干，不如另写一些逻辑来处理：

```js
function createDemoCellData(data) {
  let children
  const classes = getClasses(data.classes)
  // In cases where we're showing off multiple classes, we need to create children and apply each class to each child.
  if (Array.isArray(classes)) {
    children = classes.map(child =>
      // We can use "data.text" since each node in a cell grouping has the same text
      createNode('span', child, data.text, children)
    )
  }
  // Handle cases where we only have one class
  if (typeof classes === 'string') {
    return createNode('p', classes, data.text, children)
  }
  // Handle cases where we have no classes (ie. headings)
  return createNode(data.element, null, data.text, children)
}
```

现在我们传递给 render 函数的标准行数据了：

```js
function createRow(data) {
  let { text, element, classes = null, properties, usage } = data
  let row = []
  row[0] = createDemoCellData(data)
  row[1] = createCellData('p', displayClasses(element, classes))
  row[2] = createCellData('p', properties)
  row[3] = createCellData('p', usage)

  return row
}
```

## 渲染数据

这里展示了我们如何真正的去渲染数据：

```js
// Access our data in the "props" object
const rowData = props.rowData

// Pass it into our entry transformation function
const row = createRow(rowData)

// Create a root "div" node and handle each cell
return h('div', { class: 'row' }, row.map(cell => renderCells(cell)))

// Traverse cell values
function renderCells(data) {
  // Handle cells with multiple child nodes
  if (data.children.length) {
    return renderCell(
      data.tag, // Use the base cell tag
      {
        // Attributes in here
        class: {
          group: true, // Add a class of "group" since there are multiple nodes
          [data.cellClass]: data.cellClass // If the cell class isn't null, apply it to the node
        }
      },
      // The node content
      data.children.map(child => {
        return renderCell(child.tag, { class: child.cellClass }, child.text)
      })
    )
  }

  // If there are no children, render the base cell
  return renderCell(data.tag, { class: data.cellClass }, data.text)
}

// A wrapper function around "h" to improve readability
function renderCell(tag, classArgs, text) {
  return h(tag, classArgs, text)
}
```

我们得到了最终的[成果](https://9oj1v5qlqp.codesandbox.io/)，这里是[源代码](https://codesandbox.io/embed/9oj1v5qlqp)

## 总结

值得说明的是，这种方法只是代表了一种解决这种琐碎问题的实验性方法。我相信很多人认为这种解决方案是设计多度和不必要的。我只能说可能吧。

尽管前期的成本很高，但是这么干数据和视图是完全分离的，现在，如果我的设计团队需要增加或者删除一行，我不必去专研[混乱的 HTML](https://codepen.io/soluhmin/pen/GLzaYM.html)，我只要更新 JSON 文件中的两三个属性。

这值得吗？就像编程中的其他东西一样，我想这是要要视具体情况而定的，下面这个漫画可以代表我的观点：

![the_general_problem_obgsbr](/img/VueRenderFunctions/the_general_problem_obgsbr.webp)
