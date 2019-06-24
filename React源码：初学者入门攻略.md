# React 源码：初学者入门攻略

原文章：[The React Source Code: a Beginner’s Walkthrough I](https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030)

这是一篇 react 源码的旅程，可以帮助初学者更深刻的理解他们使用的这个工具。很多开发者知道如何以及怎么使用 react，但往往忽略了更深层的东西，为什么这么用。

而且，在拜读了好的代码之后我们可以写出更好的代码。我亲身体会到 react 源码范围之广的可怕之处。我希望通过逐行构建 React 核心源代码，将它的设计模式和小的差别暴露给初级开发者。这些知识应该可以让开发者更好的理解他们所写的代码，鼓励他对社区多做贡献，最终揭开开源项目神秘的面纱。

读者在阅读之前应该已经 JavaScript，并且有过 react 相关经验。如果有任何概念需要进一步的解析，请及时告知我，我会尽力去细说。同时，这对于我来说一趟难忘的源码之旅，希望对你们大多数人也是如此，所以我可能会混淆其中的一些概念，请及时纠正其中错误，我很乐意来在文章中修改它们。

## 什么是 React？

这部分原本并不包含在内，不过这篇文章确实是一次我和父亲交谈的结果。当他听到我正在写一篇关于 react 核心的时候，对我说：“什么？你正在写一个反应堆？政府指导这件事吗？”

我们大笑起来。对于世界上所有想知道他们儿女在做什么的父亲，我想对他们说：React 是一个非常流行的 web 框架，由 Facebook 开源。可惜的是，他与核聚变和潜艇无关。

## 深入代码

### package.json

当我第一次全身投入到一个库的研究时，我通常从[package.json](https://docs.npmjs.com/files/package.json)，这个文件通常位于项目的根目录下。`package.json`里的[main 字段](https://docs.npmjs.com/files/package.json#main)指向项目的入口，并且返回你的项目的主模块的导出对象。换句话说，当一个模块需要被`required in`时，这个就是那个`API`导入的入口。然而，当我们查看 react 根目下的`package.josn`时，我们发现`main`这个字段很奇怪的不见了。我们可以从 React 的基础介绍中了解到为什么会这样，[详情点击这里](https://reactjs.org/docs/codebase-overview.html#multiple-packages)。从这段描述中可以得到两样重要的事情，第一就是 React 在 github 上的分支是一个`monorepo`(react 和他所有相关的项目都放在同一个分支下)，第二个是，像`package.josn`
文件这种 npm 的元数据放在顶级文件夹[packages](https://github.com/facebook/react/tree/master/packages)下。

很好，这个`package`文件夹可能值得接下来去探索。在继续前进之前你可能好奇为什么`package.json`存在于根目录下，如果他没有提供入口点的话。这个 package 的依赖都是开发依赖(换句话说，没有直接相关性)。进一步讲，这个 npm 里`scripts`字段里的脚本都是开发脚本，用来使用一些有用的命令行工具像`npm run build`、`npm t`、`npm run flow`等等。因此，假设这个`package.json`是用来设置开发环境是完全合理的。

现在我们进入到项目根目录下的`package`文件夹下。我们现在对 react 核心代码感兴趣，因此我们导航到[react/packages/react/package.json](https://github.com/facebook/react/blob/master/packages/react/package.json)。你应该注意到这个`package.json`包含一个指向`idnex.js`的`main`字段，还有一些很少的依赖。如果我们导航到[react.js](https://github.com/facebook/react/blob/master/packages/react/index.js)，我们可能会惊讶的发现如下内容：

```js
'use strict'

module.exports = require('./lib/React')
```

就是这样-只有 3 行代码，其中一行是`use strict`，其他的似乎是在导出一个不存在的文件里的一个不存在的文件，同样的，这个神秘文件可以在代码概述中找到答案：

> 当我们为 npm 编译 React 时，脚本将所有模块复制到一个叫做`lib`的平级目录中，并在所有`require()`路径前加上`./`。

## 一条关于 Haste 小插曲

打开页面到[https://unpkg.com/react@15.4.1/lib/](https://unpkg.com/react@15.4.1/lib/)，我们可以看到那里确实有个叫做`React.js`的文件，但是这些文件的源文件在哪呢？这个答案在 Facebook 的文件定制系统里，Haste。Haste 致力服务于大代码量的项目，可以在不用担心指定新的相对路径的情况下将项目里的文件移动到不同的位置。Haste 和`common.js`最关键的不同点在于，Haste 要求项目所有的文件名必须全局唯一。当一个新文件在 Facebook 中被创建时，Facebook 会要求文件包含一个许可证头。在开头会有一行类似`* @providesModule React`文字，而`@providesModule`之后的文字应当和新建的文件名一致。所以，这种情况下`@providesModule React`导入的是 react 模块。以上就是如何在 Haste 中创建模块。

## React.js

在全局有一个唯一的文件名使得在 GitHub 和大多数文本编辑器上找文件变的难以置信的简单。在 React GitHub 分支上的任何一个地方按`t`，你可以输入`react.js`来搜索 react 核心模块。事实上，我在`src/isomorphic`下找到了名为`React.js`的文件。

![1_hk4L2L_fB0ML4CZW1cOvtw](/img/ReactCore/1_hk4L2L_fB0ML4CZW1cOvtw.png)

打开`src/isomorphic/React.js`我们终于看到不止 3 行代码的 JavaScript 文件了。

## 核心模块

### React.js(内容)

```js
/**
 * Copyright 2013-present, Facebook, Inc.
 * All rights reserved.
 *
 * This source code is licensed under the BSD-style license found in the
 * LICENSE file in the root directory of this source tree. An additional grant
 * of patent rights can be found in the PATENTS file in the same directory.
 *
 * @providesModule React
 */
```

1-10 行：正如我之前所提到的，每一个源码文件都引入了一个许可头。这些代码所包含的许可信息中的第九行被 Haste 用作模块的唯一标识。

```js
var ReactChildren = require('ReactChildren')
var ReactComponent = require('ReactComponent')
var ReactPureComponent = require('ReactPureComponent')
var ReactClass = require('ReactClass')
var ReactDOMFactories = require('ReactDOMFactories')
var ReactElement = require('ReactElement')
var ReactPropTypes = require('ReactPropTypes')
var ReactVersion = require('ReactVersion')
```

14-21 行：这些代码通过 Haste 导入了`React.js`的模块依赖。在这里导入的依赖最终帮助组成 React 的顶级公共 API。我们稍后再来研究他们的代码。

```js
var onlyChild = require('onlyChild')
var warning = require('warning')
```

23-24 行：这段代码还需要引入两个模块，虽然这两个模块不是顶级 API 的组成部分，但是在`React.js`本身中会使用到。其中一个警告模块比较有趣：如果你在分支中搜索它，你可能会惊讶的发现这个模块并在 react 分支中并不存在。相反，他在 Facebook 的`fbjs`分支中，[也就是在这](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/__forks__/warning.js)，这个模块在 React 的`package.json`被作为一个依赖引入了进来。如果你对其好奇，可以从[这](https://www.npmjs.com/package/fbjs)了解到为什么它在另一个分支上。

```js
var createElement = ReactElement.createElement
var createFactory = ReactElement.createFactory
var cloneElement = ReactElement.cloneElement
```

26-28 行：这段代码我认为有两个目的。1)他声明了一些实用的变量，这些变量更直观并且比直接从`ReactElement`对象中引用更加简洁。2)新创建的变量可以安全地在开发环境中重新赋值，无需担心复写了其模块的函数。

```js
if (__DEV__) {
  var ReactElementValidator = require('ReactElementValidator')
  createElement = ReactElementValidator.createElement
  createFactory = ReactElementValidator.createFactory
  cloneElement = ReactElementValidator.cloneElement
}
```

30-35 行：有一个约定在 react 源码中频繁使用，那就是`__DEV__`全局变量。你可以从[这里](https://facebook.github.io/react/contributing/codebase-overview.html#development-and-production)得到跟多了解。不过他的基本理念就是，确保这段代码只在开发环境中，换句话说，在这里创建的这块代码只会在开发环境中执行，在生产环境中不会被执行。在本例中，如果 React 文件在开发环境中执行，则需要使用 ReactElementValidator 中的 ReactElementValidator 覆盖第 26-28 行的那三个变量。

```js
var __spread = Object.assign
```

37 行：这一行第一眼看上去似乎没什么存在的必要，他的目的只有通过仔细观察才能发现。在 JavaScript 中函数是第一公民并且可以直接赋值给变量。这里的`__spread`只是一个简单的变量，保存着和`Object.assign`一样的函数引用，你可以通过[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)进行了解。这个非常重要，待会我们就会看到，`React.__spread`已经被弃用，而这个变量可以确保他现在指向和`Object.assign`一样的函数。如果只是这一行看起来仍然充满神秘的感觉，可以尝试在一个代码解释器中运行下段代码，并且想象一下最终的输出结果：

```js
// __spread
var __spread = Object.assign

var animal = { type: 'dog' }
var whichAnimal = { name: 'Rufus' }

var newAnimal = __spread({}, animal, whichAnimal)

console.log(animal) // { type: 'dog' }
console.log(whichAnimal) // { name: 'Rufus' }
console.log(newAnimal) // { type: 'dog', name: 'Rufus' }
```

```js
// React.js: 39–52 行
if (__DEV__) {
  var warned = false
  __spread = function() {
    warning(
      warned,
      'React.__spread is deprecated and should not be used. Use ' +
        'Object.assign directly or another helper function with similar ' +
        'semantics. You may be seeing this warning due to your compiler. ' +
        'See https://fb.me/react-spread-deprecation for more details.'
    )
    warned = true
    return Object.assign.apply(null, arguments)
  }
}
```

39-52 行：我们再一次遇到这个`if`只在`__DEV__`环境下执行的语句块了。这是 React 在生产环境中屏蔽开发模式下代码的方法。很明显的是，这个方法很依赖早些时候导入的`warning`函数。因此，为了理解这段代码的意图我们首先要理解[warning.js](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/__forks__/warning.js)

## warning.js

为了全面起见我决定包含这一章，因为他的功能在 React 中得到了充分的使用。不过，这个模块的标题代表了其含义，并且代码也相对简单，如果你愿意，你可以跳过这一章。

`warning`模块开始于从[emptyFunction.js](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/emptyFunction.js)中引入了一个空的函数。警告模块中最终被使用的代码位于[24 行](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/emptyFunction.js#L24)，代码如下：

```js
const emptyFunction = (...args: Array<any>) => void = function() {}
```

如果我们去掉类型定义，这个函数还剩下：

```js
const emptyFunction = (...args) => function() {}
```

这是一种将函数与他的的参数分离的惯用手法，然后简单的返回一个匿名函数。这一步非常重要，因为我们不想在非开发环境下执行是报出警告。然而，如果我们知道程序是在开发环境下执行时。`warning`将会被 40 行的变量覆盖：

```js
warning = function(condition, format, ...args) {
  if (format === undefined) {
    throw new Error(
      '`warning(condition, format, ...args)` requires a warning ' +
        'message argument'
    )
  }
  if (!condition) {
    printWarning(format, ...args)
  }
}
```

这个`warning`函数的最终目的通过结构化的数据来传达错误信息。它接受`condition`、`format`和`...args`作为它的参数。接下来第 40 行，这个警告函数将检查是否存在`format`参数，也就是错误信息被定义的地方。如果这个参数不存在，将会报出一个异常，“`warning(condition, format, …args)` requires a warning message argument”。接着`condition`参数将会被执行，如果不存在，将会在 26 行调用`printWarning`。`printWarning`将会检查`console`是否存在，如果存在。它将会调用`console.error`打印出消息。否则，他将执行`throw new Error(message)`，最终，`warning`被导出。

## 返回 React.js

```js
if (__DEV__) {
  var warned = false
  __spread = function() {
    warning(
      warned,
      'React.__spread is deprecated and should not be used. Use ' +
        'Object.assign directly or another helper function with similar ' +
        'semantics. You may be seeing this warning due to your compiler. ' +
        'See https://fb.me/react-spread-deprecation for more details.'
    )
    warned = true
    return Object.assign.apply(null, arguments)
  }
}
```

在理解了`warning.js`是什么、为什么以及如何用之后，我们可以继续逐步了解代码。代表着`React.js`中最常见的模式从第 40 行开始，一个名为`warned`的变量被设置为`false`。接着下一行，`__spread`被重新赋值给了一个新的函数，这个函数引用了`warning`
，并通过传递`warnedcam`作为消息参数。这个可以确保生产环境下的`React._spread`重命名为`Object.assign`，当在`__DEV__`环境下引用`React.__spread`将会警告用户`__spread`现在已经被弃用了，应该避免使用。接下来，`warned`被设置为`true`，这样当引用`React.__spread`时，`warning`只有在第一次打印抛出错误信息。最后，`Object.assign`调用参数`arguments`并作为最终结果返回。这段代码含义：`React.__spread`应该被避免使用，相反，在这里我们应该使用`Object.assign`。

```js
var React = {

  // Modern

  Children: {
    map: ReactChildren.map,
    forEach: ReactChildren.forEach,
    count: ReactChildren.count,
    toArray: ReactChildren.toArray,
    only: onlyChild,
  },

  Component: ReactComponent,
  PureComponent: ReactPureComponent,

  createElement: createElement,
  cloneElement: cloneElement,
  isValidElement: ReactElement.isValidElement,
```

54-71 行：这里就是 React 顶级公共 api 定义的地方。例如，`React.Component`和`React.PureComponent`在这里被分别导出为`Component: ReactComponent`和`PureComponent: ReactPureComponent`：

```js
Children: {
  map: ReactChildren.map,
  forEach: ReactChildren.forEach,
  count: ReactChildren.count,
  toArray: ReactChildren.toArray,
  only: onlyChild,
},
```

58-64 行：这里是`React.Children`API 定义的地方，其包含以下几种方法：`map`、`forEach`、`count`、`toArray`和`only`

```js
Component: ReactComponent,
PureComponent: ReactPureComponent,
```

66-67 行：定义了`React.Component`和`React.PureComponent`API

```js
createElement: createElement,
cloneElement: cloneElement,
isValidElement: ReactElement.isValidElement,
```

69-71 行：这里定义了`React.createElement`、`React.cloneElement`和`React.isValidElement`

```js
  PropTypes: ReactPropTypes,
  createClass: ReactClass.createClass,
  createFactory: createFactory,
  createMixin: function(mixin) {
    // Currently a noop. Will be used to validate and trace mixins.
    return mixin;
  },

  // This looks DOM specific but these are actually isomorphic helpers
  // since they are just generating DOM strings.
  DOM: ReactDOMFactories,

  version: ReactVersion,

  // Deprecated hook for JSX spread, don't use this for anything.
  __spread: __spread,
};
```

75-90 行：这些是 Reat 顶级类接口定义的地方，我不打算花过多的是在这些上面，但是他们当中有一些还是值得你们自己去研究的。

```js
PropTypes: ReactPropTypes,
createClass: ReactClass.createClass,
createFactory: createFactory,
createMixin: function(mixin) {
  // Currently a noop. Will be used to validate and trace mixins.
  return mixin;
},
```

75-81 行：`React.PropTypes`, `React.createClass`,`React.createFactory`, 和`React.createMixin`作为公共 API 被定义导出的地方。

```js
DOM: ReactDOMFactories,
```

85 行：这一行定义了`React.DOM`，有趣的是这个并不是特定于`DOM`的，而是返回一系列`DOM`字符串的助手函数。

```js
version: ReactVersion,
```

87 行：定义了`React.version`，包含了当前 React 的版本。

```js
__spread: __spread,
```

90 行：定义了`React.__spread`，再一次强调一下这个方法以及被弃用了，应该避免使用它。当需要是使用`Object.assign`。

```js
module.exports = React
```

93 行：最终，在`React.js`的最后一行 React 模块被导出。

## 更深层次的研究

现在我们定义了 React 的公共 API。此处，可研究的方向有很多。我们可以探索`React.PureComponent`, `React.Component`, 或者 `React.Children`，但是我决定遵循由浅入深的路线，优先考虑探索基础简单的模块，因此，我们从最直接的模块开始，`React.Children.onlyChild`,然后是`React.Component`和`React.PureComponent`模块。最终回到复杂的`React.Children`部分。

### `React.Children.onlyChild`模块

`onlyChild`是一个由(react/src/isomorphic/children/onlyChild.js)[https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030]导出的单独函数。这个模块只需要两个其他依赖。`invariant`和`ReactElement`。`invariant`模块的功能和`warning`模块非常相似，即我们之前提到的，只要 if 条件执行结果不为真，就会报出一个异常。而`invariant`异常的不同点在于，在`__DEV__`和生产环境下都会被执行，其他模块，`ReactElement`，位于[react/src/isomorphic/classic/element/ReactElement.js](https://github.com/facebook/react/blob/master/src/isomorphic/classic/element/ReactElement.js)，由 386 行代码组成。不过，本着`React.Children.onlyChild`为主，我们只看一下 372-384 行。理解`ReactElement.isValidElement`最重要的东西在于，它接受一个 React 组件并且验证他是否真的是一个 React 组件，通过如下方式：

```js
typeof object === 'object' &&
  object !== null &&
  object.$$typeof === REACT_ELEMENT_TYPE
```

他首先会检查组件的类型，来确定它是否一个对象并且不为 null，此组件还有一个被设置为`REACT_ELEMENT_TYPE`的名为`$$typeof`的属性。因此，如果`onlyChild`中传递的不是一个有效的 React 组件，它将会抛出一个固定的错误异常，否则，它将会返回这个组件。

如果可以的话我想说一下测试，因为我坚信我们可以通过模块的测试可以收获很多。这里我将花一点时间讲一下`React.Children.onlyChild`的测试部分。

### React 中测试的说明

React 中的测试是由 Facebook 的 Jest 测试框架完成的。一个文件的测试代码通常可以凭知觉猜到其大致位置。他们都在一个叫做`__tests__`的文件夹下，他保存着同一文件夹下的所有测试套件。例如，因为`onlyChild`模块位于`src/isomorphic/children`文件夹下，他的测试文件可以在`src/isomorphic/children/__tests__`文件夹下找到。这使得查找文件的测试变得更有条理并且移动文件更简单。

### 回到 onlyChild

既然我们知道了`onlyChild`的测试文件的所在位置，我们可以通过阅读测试部分来更好地理解`onlycChild`的代码。我们继续打开到`src/isomorphic/children/__tests__/onlyChild-test.js`，我不会在这里花太多时间来执行这些测试。不过每一项通过的测试都揭示了`onlyChild`是如何以及为什么而被使用的。通过测试我们可以很快的了解到`onlyChild`：

- 传入两个`children`时将会失败
- 传入空值将会失败
- 传入键值对对象将会失败
- 传入一个`children`时不会失败
- 应该只返回一个子元素

只需瞥一眼 onlyChild 的测试组件，就可以在几秒钟内获取到所有这些信息。

## React.Component

接下来，我想找一个点来读完`React.Component`。不用担心，我待会将返回去查看`React.Children`剩余部分的代码。我们从`React.Component`而不是从`React.PureComponent`入手，是因为`React.PureComponent`是建立在`React.Component`基础之上的。所以了解前者有利于对后者的理解。

`React.Component`可以在项目文件夹下的`react/src/isomorphic/modern/class/ReactComponent.js`下找到。我们可以跳过许可头部，因为他们的格式在整个分支下都是不变的：

```js
var ReactNoopUpdateQueue = require('ReactNoopUpdateQueue')
```

14 行：引入了`ReactNoopUpdateQueue`模块，这个模块本质上是一个当 react 组件的更新参数不存在时的一个替代品。它的功能非常简单，其主要功能主要是用来警告用户不要更新卸载的组件。

```js
var canDefineProperty = require('canDefineProperty')
var emptyObject = require('emptyObject')
var invariant = require('invariant')
var warning = require('warning')
```

16-19 行：这些是来自`fbjs`分支下实用函数。`invariant`和`warning`再一次被引入进来，同时还有一个叫做`emptyObject`的模块，这个模块创建了一个在开发环境中被`Object.freeze`冻结空对象，并最终被导出。

### canDefineProperty 模块

最后一个在被引用的是来自`react/src/shared/utils/canDefineProperty.js`的`canDefineProperty`模块。这是一个非常有趣的模块，其利用了`Object.defineProperty`方法。如果你对`Object.definedProperty`不熟悉，[MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)有很好的解释。

由`Object.defineProperty`创建的属性都是默认不可变和枚举的。如果我们查看 react 分支中的`canDefineProperty.js`模块，我们可以了解到其如何使用。

```js
var canDefineProperty = false
if (__DEV__) {
  try {
    // $FlowFixMe https://github.com/facebook/flow/issues/285
    Object.defineProperty({}, 'x', { get: function() {} })
    canDefineProperty = true
  } catch (x) {
    // IE will fail on defineProperty
  }
}
```

15-24 行：一个叫做`canDefineProperty`标识被声明，并且默认为布尔值`false`。接着，如果 react 在`__DEV__`中执行，它将会尝试对一个空对象调用`Object.defineProperty`。你可能想知道，如果我们没有对这个空对象做任何事，那为什么需要这一步。如果我们查看`try`对应的`catch`条件，我们将看到一行注释表明，在 IE 下`Object.canDefineProperty`将会失效。这也就意味着 19 将会引起一个异常，在`canDefineProperty`被设置为`true`之前，这个异常将会先被`catch`抓到。最终`canDefineProperty`被导出

### 回到 ReactComponent.js

既然我们知道了`canDefineProperty`导出了什么，让我们回到`ReactComponent.js`。

```js
function ReactComponent(props, context, updater) {
  this.props = props
  this.context = context
  this.refs = emptyObject
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue
}
```

24-31 行：这些行定义了基类，而这些类被拓展成了 react 组件！24 行的函数是一个构造函数伪类，其创造了 React 组件的实体。正如我们所看到的。这里通过在运行是传递的参数设置了组件的`props`、`context`和`updater`，如果`updater`没有指定，它将被设置为之前引入的`ReactNoopUpdateQueue`模块。而`refs`被设置为一个空对象，由[这里](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/emptyObject.js)导出。

我们可以像这样调用：

```js
class button extends React.Component {
  // some code
  // ..
  // ..
}
```

这就是实际使用中被拓展的函数！

```js
ReactComponent.prototype.isReactComponent = {}
```

33 行：设置了在`ReactCompoent`中设置了`isReactComponent`属性，并且分配了一个空对象给它。

```js
ReactComponent.prototype.setState = function(partialState, callback) {
  invariant(
    typeof partialState === 'object' ||
      typeof partialState === 'function' ||
      partialState == null,
    'setState(...): takes an object of state variables to update or a ' +
      'function which returns an object of state variables.'
  )
  this.updater.enqueueSetState(this, partialState, callback, 'setState')
}
```

60-69 行：上述代码在`ReactComponent.prototype`中定义了`setState`方法。`setState`做的第一件事就是防止`partialState`既不是对象，也不是函数和 null。回想一下，`partialState`不仅仅代表着需要被合并到状态中的信息，还有如何合并它。如果`partialState`不是上面三种中的任何一个，它将引起一个异常。否则他将通过`enqueueSetState`方法将新的状态传递给 react reconciler 。

```js
ReactComponent.prototype.forceUpdate = function(callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate')
}
```

85-87 行：这些代码在`ReactComponent.prototype`上定义了`forceUpdate`方法，这个函数仅仅就是告诉 reconciler ，有一些东西发生了变化，应该通过调用 enqueueForceUpdate 来更新状态。回想一下，updater 是在运行时注入的，默认的 updater 属性可能没有 enqueueForceUpdate!

```js
if (__DEV__) {
  var deprecatedAPIs = {
    isMounted: [
      'isMounted',
      'Instead, make sure to clean up subscriptions and pending requests in ' +
        'componentWillUnmount to prevent memory leaks.'
    ],
    replaceState: [
      'replaceState',
      'Refactor your code to use setState instead (see ' +
        'https://github.com/facebook/react/issues/3236).'
    ]
  }
  var defineDeprecationWarning = function(methodName, info) {
    if (canDefineProperty) {
      Object.defineProperty(ReactComponent.prototype, methodName, {
        get: function() {
          warning(
            false,
            '%s(...) is deprecated in plain JavaScript React classes. %s',
            info[0],
            info[1]
          )
          return undefined
        }
      })
    }
  }
  for (var fnName in deprecatedAPIs) {
    if (deprecatedAPIs.hasOwnProperty(fnName)) {
      defineDeprecationWarning(fnName, deprecatedAPIs[fnName])
    }
  }
}
```

94-127 行：上述代码被在开发环境中用来警告开发者不要使用计划弃用的 API。具体的说，如果一个开发者使用了`deprecatedAPIs`对象中的`isMounted`或者`replaceState`，将会引发一个警告。107 行的`defineDeprecationWarining`体现了前面引入`canDefineProperty`模块的必要性。他将被废弃的模块作为不可枚举属性添加到`ReactComponent.prototype`中，他们在被访问之前是不可见的。这里被弃用的接口被“隐藏”。如果它们被调用，他们的 `getters`方法将会被调用，最终在 122 行的`for ...in`循环将会添加这些被弃用的接口到`ReactComponent.prototype`当中。有趣的是，在未来被弃用的接口可以直接添加到 95 行的`deprecatedAPIs`对象当中，当在`__DEV__`环境中被调用时将会引发一个适当的警告。

```js
module.exports = ReactComponent
```

129 行：`ReactComponent`被导出

这就是`ReactComponent`模块中的 API 了。既然我们已经讨论了`ReactComponent`模块，我们可以研究下继承与`ReactComponent`的`ReactPureComponent`。

### ReactPureComponent

实际上，`ReactComponent`模块在第 14 行被引入到`ReactPureComponent`模块当中。同时还有`ReactNoopUpdateQueue`和`emptyObject`模块-这两个都是`ReactComponent`所依赖的模块。我们可以跳过这些代码，因为他们的作用前面已经讲过了。

```js
function ReactPureComponent(props, context, updater) {
  // Duplicated from ReactComponent.
  this.props = props
  this.context = context
  this.refs = emptyObject
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue
}
```

22-30 行：这个函数看上去应该相当熟悉，他与出现在 24 行的`ReactComponent.js`完全相同，除了名称叫`ReactPureComponent`。实际上，23 行的注意说明了这段代码是从`ReactComponent`模块复制的。

```js
function ComponentDummy() {}
ComponentDummy.prototype = ReactComponent.prototype
ReactPureComponent.prototype = new ComponentDummy()
ReactPureComponent.prototype.constructor = ReactPureComponent
// Avoid an extra prototype jump for these methods.
Object.assign(ReactPureComponent.prototype, ReactComponent.prototype)
ReactPureComponent.prototype.isPureReactComponent = true
```

32-37 行：这些代码第一眼看上去可能有点陌生，尤其如果你不熟悉 JavaScript 的原型代理的话。如果你想了解更多关于原型链继承相关知识的话，MDN 有一篇极佳的文章，可以在[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)进行阅读。这里有一些像`ComponentDummy`这样的间接的伪类组件，我将尽我所能来分解他们。`ComponentDummy`的构造函数在 32 行定义，并且给`ComponentDummy.prototype`实例化了一个空对象。接着，在第 33 行`ComponentDummy`的原型被`ReactComponent`的原型覆盖。然后，`ReactPureComponent`的原型被`ComponentDummy`实例替代，也就是那个值为空，其`__proto__`指向`ReactComponent`的原型的函数。这步操作允许定义在`ReactComponent.prototype`之上的行为直接委托给`ReactPureComponent.prototype`。如果这个无法理解的话，看一下如下代码片段并且尝试理解其中发生了什么。

```js
function ReactComponent() {
  console.log('I am a ReactComponent!')
}

ReactComponent.prototype.ReactComponentMethod = function() {
  console.log('This is a method on the ReactComponent.prototype')
}

function ReactPureComponent() {
  console.log('I am a ReactPureComponent!')
}

function ComponentDummy() {}

ComponentDummy.prototype = ReactComponent.prototype
ReactPureComponent.prototype = new ComponentDummy()
ReactPureComponent.prototype.constructor = ReactPureComponent

ComponentDummy.prototype.constructor() // I am a ReactComponent!
ReactComponent.prototype.constructor() // I am a ReactComponent!
ReactPureComponent.prototype.constructor() // I am a ReactPureComponent!

const newPureComp = new ReactPureComponent() // I am a ReactPureComponent!
newPureComp.ReactComponentMethod() // This is a method on the ReactComponent.prototype
```

接下来，`ReactPureComponent`的原型构造函数属性在 35 行被重新赋值。这是很有必要的，因为在之前其构造函数被新的`ComponentDummy`实例覆盖了。
