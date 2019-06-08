# 使用异步组件优化 vue 应用

[Optimize the Performance of a Vue App with Async Components](https://www.sitepoint.com/vue-async-components/)

单页面应用优势会因为他们缓慢的加载速度而被浏览器给与一点警告。那是因为传统的方式中，服务器将会发送一个巨大的 JavaScript 文件到客户端，在渲染到屏幕之前需要先下载和解析。正如你所想的一样，当你的应用的体积不断增长时，这个解析时间将会更长也会出现问题。

幸运的是，当使用 Vue CLI 常见 Vue 应用时，你可以有很多种方法来解决它。在这篇文章里，我将演示如何使用异步组件和 webpack 代码切割函数来加载部分页面，在初始化渲染之后。这样可以将你的初始化加载时间控制在短的时间之内并且给你的应用一个快速的感觉。

为了能跟上这篇教程，你需要对 vue 和 node 有一个基础的理解。

## 异步组件

在我们开始创建异步组件之前，让我先看一下我们正常情况下是如何加载一个组件的。为此，我们将使用一个非常简单的消息组件。

```js
<!-- Message.vue -->
<template>
  <h1>New message!</h1>
</template>
```

现在我们已经创建了组件，让我们将其加载到 App.vue 文件中并显示它。

```js
<!-- App.vue -->
<template>
  <div>
    <message></message>
  </div>
</template>

<script>
import Message from "./Message";
export default {
  components: {
    Message
  }
};
</script>
```

现在将会发生什么？每当我们的应用被加载时这个`Message`组件都将会被加载，所以这个组件将会包含在初始加载进程之中。

对于简单的应用来说这并不是什么大问题，但是考虑到有时候情况将会更复杂比如网页内存。想象一下一个用户添加了一个商品到购物车，然后像推掉，所以就点击了清出购物车按钮，这个是会弹出一个所有购物车内部商品的按钮。用上面那个方法，这些选择框代码都将包含在初始代码里，虽然我们只想要用户点击的清空按钮，这也就意味着没必要浪费资源去加载这个可能会毫无用处的组件。

为了提高应用的性能，我们可以结合使用懒加载和代码分割技术。

懒加载就是延迟初始中的一些组件。你可以在`medium.com`查看懒加载的使用，其中图片只有在被需要时才会被加载。这个非常有用，这样我们就不需要为一个特定的文章浪费资源加载所有的图片了，因为用户可能会在中途跳过这篇文章。

`webpack`提供的代码切割特性允许你将代码切分到不同的文件里，这样他们就可以根据需要加载，也可以稍后加载。

## 动态加载

幸运的是，Vue 可以满足这个场景，通过使用一个叫做动态加载的技术。该特性引入了一种新的函数的导入方式，它将返回包含所请求组件(Vue)的一个 Promise。由于 import 函数接受一个字符串，我们可以使用一些更高级方式比如通过表达式加载组件。动态加载在谷歌浏览器 61 版本就可以使用了。

代码分割将由像 webpack。Rollup、Parcel 这类的打包工具进行处理，他们可以解析动态加载语法，并且为每一个动态引入的模块创建一个单独的文件。这个我们之后可以在网络控制台中进行查看。不过首先让我们看一下静态和动态加载的不同：

```js
// static import
import Message from './Message'

// dynamic import
import('./Message').then(Message => {
  // Message module is available here...
})
```

现在我们将其应用到`Message`组件中，现在我们的`App.vue`如下：

```js
<!-- App.vue -->
<template>
  <div>
    <message></message>
  </div>
</template>

<script>
import Message from "./Message";
export default {
  components: {
    Message: () => import("./Message")
  }
};
</script>
```

如果我们打开你的网络调试工具标签，你将会注意到一个叫做`0.js`的文件，那就是你异步加载的组件。

![semantic-html5-tags-outline](/img/OtimizeVueAppWithAsync/15431553231-network-tab-output.png)

## 条件加载异步组件

现在我们已经有了一个异步加载的组件，现在我们获得其真正的效果，通过在需要的时候加载他们。

## 项目配置

如果你还没有安装 Vue CLI，你应该执行它：

```shell
npm i -g @vue/cli
```

接下来，使用 CLI 创建一个新项目，在提示时选择默认的设置:

```shell
cd my-store
npm i ant-design-vue
```

下一步，在`src/main.js`中导入`Ant Design`库：

```js
import 'ant-design-vue/dist/antd.css'
```

最终，我们在`src/comonents`文件夹下创建两个组件，`Checkout.vue`和`Items.vue`:

```shell
touch src/components/{Checkout.vue,Items.vue}
```

打开`src/App.vue`使用如下代码将其替换：

```js
<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <items></items>
  </div>
</template>

<script>
import items from "./components/Items"

export default {
  components: {
    items
  },
  name: 'app',
  data () {
    return {
      msg: 'My Fancy T-Shirt Store'
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
```

这里没什么特别的，我们想要的就是展示一条消息和渲染`<item>`组件。

下一步，打开`src/components/Items.vue`，添加如下代码：

```js
<template>
  <div>
    <div style="padding: 20px;">
      <Row :gutter="16">
        <Col :span="24" style="padding:5px">
          <Icon type="shopping-cart" style="margin-right:5px"/>{{shoppingList.length}} item(s)
          <Button @click="show = true" id="checkout">Checkout</Button>
        </Col>
      </Row>
    </div>
    <div v-if="show">
      <Row :gutter="16" style="margin:0 400px 50px 400px">
        <checkout v-bind:shoppingList="shoppingList"></checkout>
      </Row>
    </div>
    <div style="background-color: #ececec; padding: 20px;">
      <Row :gutter="16">
        <Col :span="6" v-for="(item, key) in items" v-bind:key="key" style="padding:5px">
          <Card v-bind:title="item.msg" v-bind:key="key">
            <Button type="primary" @click="addItem(key)">Buy ${{item.price}}</Button>
          </Card>
        </Col>
      </Row>
    </div>
  </div>
</template>

<script>
import { Card, Col, Row, Button, Icon } from 'ant-design-vue';

export default {
  methods: {
    addItem (key) {
      if(!this.shoppingList.includes(key)) {
        this.shoppingList.push(key);
      }
    }
  },
  components: {
    Card, Col, Row, Button, Icon,
    checkout: () => import('./Checkout')
  },
  data: () => ({
    items: [
      { msg: 'First Product', price: 9.99 },
      { msg: 'Second Product', price: 19.99 },
      { msg: 'Third Product', price: 15.00 },
      { msg: 'Fancy Shirt', price: 137.00 },
      { msg: 'More Fancy', price: 109.99 },
      { msg: 'Extreme', price: 3.00 },
      { msg: 'Super Shirt', price: 109.99 },
      { msg: 'Epic Shirt', price: 3.00 },
    ],
    shoppingList: [],
    show: false
  })
}
</script>
<style>
#checkout {
  background-color:#e55242;
  color:white;
  margin-left: 10px;
}
</style>
```

在这个文件中，我们展示了一个购物车图标和当前购买商品的数量。

我们也添加了一个清空按钮在页面上，这时事情开始变得有趣了：

```html
<button @click="show = true" id="checkout">Checkout</button>
```

当用户点击按钮时，我们将`show`参数设置为`true`,这个`true`的值对于条件加载我们的异步组件非常重要。

在下面几行，你能找到一个`v-if`状态，`<div>`里的内容只有当`show`被设置为`true`时才会被展示。这个`<div>`标签包含了清空组件，只有当用户点击清空按钮时他才会被加载进来。

清空组件在`<script>`中的`components`选项中被异步加载进来。我们甚至还可以通过`v-bind`给其传递参数。正如你所见，创建一个条件加载异步组件真的很容易：

```js
<div v-if="show">
  <checkout v-bind:shoppingList="shoppingList" />
</div>
```

`src/components/Checkout.vue`添加如下代码：

```js
<template>
  <Card title="Checkout Items" key="checkout">
    <p v-for="(k, i) in this.shoppingList" :key="i">
      Item: {{items[Number(k)].msg}} for ${{items[Number(k)].price}}
    </p>
  </Card>
</template>

<script>
import { Card } from 'ant-design-vue';

export default {
  props: ['shoppingList'],
  components: {
    Card
  },
  data: () => ({
    items: [
      { msg: 'First Product', price: 9.99 },
      { msg: 'Second Product', price: 19.99 },
      { msg: 'Third Product', price: 15.00 },
      { msg: 'Fancy Shirt', price: 137.00 },
      { msg: 'More Fancy', price: 109.99 },
      { msg: 'Extreme', price: 3.00 },
      { msg: 'Super Shirt', price: 109.99 },
      { msg: 'Epic Shirt', price: 3.00 },
    ]
  })
}
</script>
```

最终你将得到如下页面：

![15431556012-Webshop-example](/img/OtimizeVueAppWithAsync/15431556012-Webshop-example.png)

你可以在[github 上找到这段代码](https://github.com/sitepoint-editors/shopping-cart-async)

## 加载中和错误组件异步处理

我们甚至可以定义加载中或者错误组件，当我们的组件加载中时或者加载失败时。展示一个加载动画将会非常有用，不过要记住这将会拖慢你的应用。一个异步组件应该小而且加载起来足够快。这里是一个例子：

```js
const Message = () => ({
  component: import('./Message'),
  loading: LoadingAnimation,
  error: ErrorComponent
})
```

## 总结

创建一个可用的异步组件非常简单，而且这应当成为你的标准开发准则。希望这篇教程能对你有所帮助。
