# 如何使用 React Hooks 和 Context 代替 Redux

原文章：[How to Replace Redux with React Hooks and the Context API](https://www.sitepoint.com/replace-redux-react-hooks-context-api/)

在 react 中处理共享状态最流行的方式就是使用像 Redux 这样的框架。最近，react 团队引入了几个新的特性，其中就包括 React Hooks 和 Context API。这两个特性可以有效的解决很多开发者在大型 React 项目中面临的问题和挑战。其中一个最大问题就是在嵌套组件中跨组件传递属性。现在的解决方案是使用像 Redux 的状态管理库。不幸的是，使用 redux 在写代码时会受到诸多模板写法的限制-但是现在，使用 React Hooks 和 Context API 来替代 Redux 变的极有可能。

在这篇文章里，你将学习到一种在新的在项目中处理状态的方法，不再需要像在 redux 中写一些冗余的代码或者安装一堆类库。React hooks 允许你在函数组件中使用本地状态，而 Context API 允许你将状态分享给其他组件。

## 关于新的状态管理技术

在 React 项目中你需要处理两类状态：

- 本地状态
- 全局状态

本地状态只能在组件内部使用。全局状态能够在几个组件中进行共享。无论哪种状态，在 React hooks 中都有对应的处理方法：

- `userState`
- `userReducer`

`useState`建议用来处理像数字或者字符串这种简单的值。不过，当需要处理复杂的数据结构是，你就需要使用`userReducer`了。不像`useState`只能使用`setValue`方法，`useReducer`允许你定义你想要的任何函数。例如，一个对象数组必要的添加、更新和删除方法。

一旦你决定了使用`useState`或者`useReducer`其中一种，你可以通过使用`React Context`将其提升到全局。这个技术允许你在组件中分享值，无需通过 props 向下传递。当你声明了一个`Context`对象，他将作为发布者给其他组件提供`context`内容变化时的通知。你可以添加尽可能多的组件到发布者。这些所有订阅过的组件的共享状态都会自动同步。

现在我们来创建一个项目，这样你可以在实战中理解他是如何工作的。

## 项目初始化

创建项目最简单的方法就是使用 create-react-app 工具。这些工具将会安装一堆开发依赖，这将会占用很多的磁盘内存。结果就是，在启动开发服务之前你将花很长时间去安装依赖。如果你不介意这个小问题，你可以直接使用这个工具创建一个新的项目。你可以将项目命名为`react-hooks-context-demo`.

另外一种创建项目的方式就是通过拷贝一个初始项目的配置，这个配置是使用[Parcel JS](https://parceljs.org/)创建的。这个方法者少减少了一半的文件大小，并且启动服务比`create-react-app`更加的快.我已经专门为这个教程创建了一个。命名如下：

```shell
$ git clone git@github.com:brandiqa/react-parcel-starter.git react-hooks-context-demo
$ cd react-hooks-context-demo
$ git remote rm origin
# Replace `username` and `repositoryName` with yours
$ git remote add origin git@github.com:username/repositoryName.git
$ git config master.remote origin
$ git config master.merge refs/heads/master
$ git push -u origin master
# Install dependencies
$ npm install
```

在你完全执行了上面的说明之后，你可以使用`npm start`指令来启动我们的服务器。你需要自己启动浏览器并且跳转到`localhost:1234`页面。

![155688124101-react-parcel-starter](/img/ReplaceReduxWithContext/155688124101-react-parcel-starter.png)

如果你使用`create-react-app`脚手架，这个页面看起来将会不一样，不过那也没事，因为我们下一步将会改变默认页面。如果你的项目可以正常启动，你可以移步下一小节。

## 安装一个 UI 库

这一步对于本篇教程来说并不是必要的，我一直喜欢用最少的精力创建一个干净漂亮的界面。对于本篇教程，我们将使用[Semantic UI React](https://react.semantic-ui.com/)。因为这篇教程是关于状态管理的，我就不解释这个库是如何工作了。我这里只展示一下如何使用它：

```shell
npm install semantic-ui-react semantic-ui-css
```

打开`index.js`，通过如下方式来导入：

```shell
import 'semantic-ui-css/semantic.min.css';
import './index.css';
```

这就是我们准备工作的全部内容了。下面让我们开始使用新的状态管理技术的第一个例子。
