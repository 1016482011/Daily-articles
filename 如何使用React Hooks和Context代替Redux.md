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

## 计数的例子

在这个例子里，我们设计一个由两个按钮和一个展示按钮组成的简单的例子。为了去展示全局状态，这个例子将由两个展示的组件组成。首先，我们徐亚定义我们的`context`对象，用来存放我们的状态。这个和 redux 里的 store 很类似。使用我们的 context 达到和 store 相同的目的需要给每个项目添加一些引用代码。幸运的是，有人已经给我们写好了一个自定义的钩子，这个钩子允许你只用一行代码创建你的 context 对象。只要安装`constate`库即可：

```shell
npm install constate
```

这个安装完成之后，你就可以开发了。我将在代码里写上注释来说明其功能。创建 context 对象文件`context/CounterContext.js`并且插入一下代码：

```js
import { useState } from 'react'
import createUseContext from 'constate' // Context状态生成器

// 第一步：创建一个包含状态和action的自定义钩子
function useCounter() {
  const [count, setCount] = useState(0)
  const increment = () => setCount(prevCount => prevCount + 1)
  const decrement = () => setCount(prevCount => prevCount - 1)
  return { count, increment, decrement }
}

// 第二步：声明context状态对象，以便与其他组件共享状态
export const useCounterContext = createUseContext(useCounter)
```

创建一个父组件`views/Counter.jsx`，插入如下代码：

```js
import React from 'react'
import { Segment } from 'semantic-ui-react'

import CounterDisplay from '../components/CounterDisplay'
import CounterButtons from '../components/CounterButtons'
import { useCounterContext } from '../context/CounterContext'

export default function Counter() {
  return (
    // Step 3: Wrap the components you want to share state with using the context provider
    // 第三步：使用context provider包装的想要共享组件
    <useCounterContext.Provider>
      <h3>Counter</h3>
      <Segment textAlign="center">
        <CounterDisplay />
        <CounterButtons />
      </Segment>
    </useCounterContext.Provider>
  )
}
```

创建视图组件`components/CounterDisplay.jsx`，插入如下代码：

```js
import React from 'react'
import { Statistic } from 'semantic-ui-react'
import { useCounterContext } from '../context/CounterContext'

export default function CounterDisplay() {
  // 第四步：通过context；来使用共享状态
  const { count } = useCounterContext()
  return (
    <Statistic>
      <Statistic.Value>{count}</Statistic.Value>
      <Statistic.Label>Counter</Statistic.Label>
    </Statistic>
  )
}
```

创建视图组件 components/CounterButtons.jsx，插入如下代码：

```js
import React from 'react'
import { Button } from 'semantic-ui-react'
import { useCounterContext } from '../context/CounterContext'

export default function CounterButtons() {
  // 第四步：通过context；来使用共享状态
  const { increment, decrement } = useCounterContext()
  return (
    <div>
      <Button.Group>
        <Button color="green" onClick={increment}>
          Add
        </Button>
        <Button color="red" onClick={decrement}>
          Minus
        </Button>
      </Button.Group>
    </div>
  )
}
```

将`App.jsx`中的代码替换为：

```js
import React from 'react'
import { Container } from 'semantic-ui-react'

import Counter from './views/Counter'

export default function App() {
  return (
    <Container>
      <h1>React Hooks Context Demo</h1>
      <Counter />
    </Container>
  )
}
```

你在浏览器里将会得到如下页面。点击按钮来确认其工作是否正常：

![155688124202-counter-demo](/img/ReplaceReduxWithContext/155688124202-counter-demo.png)

希望这个例子对你能有所帮助-读完我代码里包含的代码后，让我们开始开始下一个章节，我将使用一个更高级一点的例子。

## 联系人的例子

在这个例子里，我将创建一个用来管理联系人的增删改查页面。其将会有两个视图组件和一个容器组件。还有一个 context 对象用来管理联系人状态。由于我们的状态数会比之前一个列子更加复杂一点，所以这里我们将使用`userReducer`钩子。

创建 context 对象`context/ContactContext.js`，插入如下代码：

```js
import { useReducer } from 'react'
import _ from 'lodash'
import createUseContext from 'constate'

// 定义我们应用的初始状态
const initialState = {
  contacts: [
    {
      id: '098',
      name: 'Diana Prince',
      email: 'diana@us.army.mil'
    },
    {
      id: '099',
      name: 'Bruce Wayne',
      email: 'bruce@batmail.com'
    },
    {
      id: '100',
      name: 'Clark Kent',
      email: 'clark@metropolitan.com'
    }
  ],
  loading: false,
  error: null
}

// 定义一个纯函数的reducer
const reducer = (state, action) => {
  switch (action.type) {
    case 'ADD_CONTACT':
      return {
        contacts: [...state.contacts, action.payload]
      }
    case 'DEL_CONTACT':
      return {
        contacts: state.contacts.filter(contact => contact.id != action.payload)
      }
    case 'START':
      return {
        loading: true
      }
    case 'COMPLETE':
      return {
        loading: false
      }
    default:
      throw new Error()
  }
}

// 声明你的自定义钩子，包含状态、分发器和action
const useContacts = () => {
  const [state, dispatch] = useReducer(reducer, initialState)
  const { contacts, loading } = state
  const addContact = (name, email) => {
    dispatch({
      type: 'ADD_CONTACT',
      payload: { id: _.uniqueId(10), name, email }
    })
  }
  const delContact = id => {
    dispatch({
      type: 'DEL_CONTACT',
      payload: id
    })
  }
  return { contacts, loading, addContact, delContact }
}

// 导出你的自定义钩子
export const useContactsContext = createUseContext(useContacts)
```

创建其父组件`views/Contacts.jsx`，插入如下代码：

```js
import React from 'react'
import { Segment, Header } from 'semantic-ui-react'
import ContactForm from '../components/ContactForm'
import ContactTable from '../components/ContactTable'
import { useContactsContext } from '../context/ContactContext'

export default function Contacts() {
  return (
    // 将需要共享状态的组件包装起来
    <useContactsContext.Provider>
      <Segment basic>
        <Header as="h3">Contacts</Header>
        <ContactForm />
        <ContactTable />
      </Segment>
    </useContactsContext.Provider>
  )
}
```

创建视图组件`components/ContactTable.jsx`,并且插入如下代码：

js

```js
import React, { useState } from 'react'
import { Segment, Table, Button, Icon } from 'semantic-ui-react'
import { useContactsContext } from '../context/ContactContext'

export default function ContactTable() {
  // Subscribe to `contacts` state and access `delContact` action
  const { contacts, delContact } = useContactsContext()
  // Declare a local state to be used internally by this component
  const [selectedId, setSelectedId] = useState()

  const onRemoveUser = () => {
    delContact(selectedId)
    setSelectedId(null) // Clear selection
  }

  const rows = contacts.map(contact => (
    <Table.Row
      key={contact.id}
      onClick={() => setSelectedId(contact.id)}
      active={contact.id === selectedId}
    >
      <Table.Cell>{contact.id}</Table.Cell>
      <Table.Cell>{contact.name}</Table.Cell>
      <Table.Cell>{contact.email}</Table.Cell>
    </Table.Row>
  ))

  return (
    <Segment>
      <Table celled striped selectable>
        <Table.Header>
          <Table.Row>
            <Table.HeaderCell>Id</Table.HeaderCell>
            <Table.HeaderCell>Name</Table.HeaderCell>
            <Table.HeaderCell>Email</Table.HeaderCell>
          </Table.Row>
        </Table.Header>
        <Table.Body>{rows}</Table.Body>
        <Table.Footer fullWidth>
          <Table.Row>
            <Table.HeaderCell />
            <Table.HeaderCell colSpan="4">
              <Button
                floated="right"
                icon
                labelPosition="left"
                color="red"
                size="small"
                disabled={!selectedId}
                onClick={onRemoveUser}
              >
                <Icon name="trash" /> Remove User
              </Button>
            </Table.HeaderCell>
          </Table.Row>
        </Table.Footer>
      </Table>
    </Segment>
  )
}
```

创建视图组件`components/ContactForm.jsx`，并且插入这段代码：

```js
import React, { useState } from 'react'
import { Segment, Form, Input, Button } from 'semantic-ui-react'
import { useContactsContext } from '../context/ContactContext'

export default function ContactForm() {
  const name = useFormInput('')
  const email = useFormInput('')
  // Consume the context store to access the `addContact` action
  const { addContact } = useContactsContext()

  const onSubmit = () => {
    addContact(name.value, email.value)
    // Reset Form
    name.onReset()
    email.onReset()
  }

  return (
    <Segment basic>
      <Form onSubmit={onSubmit}>
        <Form.Group widths="3">
          <Form.Field width={6}>
            <Input placeholder="Enter Name" {...name} required />
          </Form.Field>
          <Form.Field width={6}>
            <Input placeholder="Enter Email" {...email} type="email" required />
          </Form.Field>
          <Form.Field width={4}>
            <Button fluid primary>
              New Contact
            </Button>
          </Form.Field>
        </Form.Group>
      </Form>
    </Segment>
  )
}

function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue)

  function handleChange(e) {
    setValue(e.target.value)
  }

  function handleReset() {
    setValue('')
  }

  return {
    value,
    onChange: handleChange,
    onReset: handleReset
  }
}
```

相应的在`App.jsx`中插入如下代码:

```js
import Contacts from './views/Contacts'
//...
;<Container>
  <h1>React Hooks Context Demo</h1>
  {/* <Counter /> */}
  <Contacts />
</Container>
```

实现完这段代码之后，刷新你的浏览器。删除一个联系人，你需要选择一行然后点击删除按钮。创建一个联系人，只需要填写表单，然后点击新增按钮即可。

![155688124403-contacts-example](/img/ReplaceReduxWithContext/155688124403-contacts-example.png)

复习一下代码，确保你理解了这里面的所有东西。仔细阅读代码里的注释。

## 总结

现在你唯一需要问自己的问题是:redux 对于你未来的项目是否是必要的？如上这种技术的一个缺点是不能使用 Redux DevTool 插件调试应用程序状态。不过，这个在未来可能将会有所改善，我们可以开发一个新的工具来调试。显然作为一个开发者，你仍然需要学习 Redux，以便管理以前的项目。如果你开始了一个新的项目，你需要询问你和你同事，在你们的项目中是否有必要使用状态库。
