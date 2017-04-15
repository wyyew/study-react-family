## 五大核心概念：

- 组件

- JSX

- Props & State & context

- 组件API

- 组件类型

### 概念一：React组件的作用

你需要了解有关React的第一要点就是——组件。你编写的所有React代码基本上就是一个包含许多小组件在内的大组件。

那么到底什么是组件呢？我们可以拿HTML标签 <select> 来举一个很恰当的例子。原生的下拉框标签不止包括边框、文本、下拉箭头，它还掌控着自身打开关闭的逻辑。


 图片

现在来设想一下你需要构建一个你自定义样式和行为逻辑的<select>：

 图片

这其实就是React能够帮你做到的。React组件能够像原生的HTML标签一样输出特定的界面元素，并且也能包括一些元素相关逻辑功能的代码。

现在我们一般会用ES6的Class语法来声明一个React组件，它包含一个能够返回HTML的render方法。（当然也可以用函数声明）

```
class MyComponent extends React.Component {
  render() {
    return (
      <p>Hello React!</p>
    )
  }
}
```
### 概念二：JSX是什么?

是的你没看错，按照上面React组件的示例代码，React的意思就是让我们把HTML和JS代码全都写在一起。React是通过一种叫做JSX的语法扩展（X代表XML）来实现的。

按照我们以往的传统，应该尽量把HTML和JavaScript的代码分开才是。不过看样子现在忘记这教条才是提高你前端开发效率的正道。

1. JSX允许你在js中写可嵌套的闭合标签，可以自定义属性

```
const Demo1 = () => {
  return (
    <li>
      <h3>类似HTML</h3>
      <p data-attribute="demo1">可以嵌套， 可以自定义属性</p>
    </li>
  )
}
```
> 注意: 因为JSX终究还是JS,而class和for 又是js中的保留字，所以尽管JSX中的标签属性绝大多数都与HTML规范一致，但是class和for这两个属性在JSX中需要写为className和htmFor.

2. JSX允许在闭合标签中使用js表达式，但要用{JS变量}大括号包裹起来：

```
class MyComponent extends React.Component {
  render() {
    return (
      <h3>JS表达式</h3>
      <p>今天是:{new Date()}</p>
    )
  }
}

export default MyComponent
```

3. 你不再需要什么前端模板标签之类的东西了，你可以直接在JSX中使用三元运算符一类的逻辑：

```
export default class MyComponent1 extends React.Component {
  render() {
    return (
      <p>Hello {this.props.someVar ? 'world' : 'Kitty'}</p>
    )
  }
}
```
4. 样式分为内联样式、内嵌样式、链接式等，这里证讲一下内联样式的写法， 内联样式不是字符串，而是对象。

```
const Demo4 = () => {
  return (
    <li>
    <h3>样式</h3>
    <p style={{color:'red', fontSize:'14px'}}内联样式不是字符串，而是对象</p>
    </li>
  );
}

```

> 注意： 对象中的属性名需要使用驼峰命名法，例如：backgroundColor:#ececec

5. 注释--在JSX中添加注释只需要注意将标签子节点内的注释写在{}中就可以了。

```
const Demo5 = () => {
  return (
  <h3>注释</h3>
  {/* 注释 */}
  <p>标签子节点内注释写在大括号中</p>
  )
}
```

6. 数组--JSX中数组会自动展开所有成员。但是需要注意， 如果数组或迭代器中的每一项都是HTML标签或组件， 那么它们必须要拥有唯一的key属性。这样做是为React的DIFF算法服务的， React会通过唯一的key属性实现最高效的DOM更新。

```
const Demo6 = () => {
  const arr = [
  <h3 key={0}>数组</h3>,
  <p key={1}>数组会自动展开。注意，数组中每一项元素需要添加key属性</p>
  ];
  return (<li>{arr}</li>)
}
```

7. HTML标签vs. React组件

React可以渲染HTML标签或React组件。HTML标签使用小写字母的标签名，顼React组件的标签名首字母要大写。

```
export default class App extends Component{
render() {
  return (
    <div>
    <h2>JSX语法</h2>
    <ul>
      <Demo1 />
      <MyComponent />
      <MyComponent1 />
      <Demo5 />
      <Demo4 />
      <Demo6 />
    </ul>
    ...
    </div>
  );
}
}
```

## 概念三：Props & State & context React的数据载体

> state 其实应该被称为内部状态或局布状态。“内部”表示它很少“跑”出组件，“状态”则意味着它经常变化。prop与context则用于在组件间传递数据， props只支持逐层传递， 而context 则能够跨级传递。

### state

React 组件可以在构造函数中初始化内部状态，可以通过this.setState()方法更新内部状态，还可以使用this.state获取内部状态。这些内部状态的操作与React的事件系统 配合就可以实现一些用户交互的功能。

```
export default class Counter extends Component {
 constructor() {
  super();
  this.state = { value: 0};
 }
 
  render()
  {
   return (
    <div>
     <button onClick={() => this.setSate({value: this.state.value + 4 })}>
      增加
     </button>
    </div>
    Counter组件的内部状态:
    <pre>{JSON.stringify(this.state, null, 2)}</pre>
   )
  }
 }
```
> 注意： constructor是ES2015类中的构造函数， 它公在实例化一个类的时候被调用。另外在子类的构造函数中，必须先调用super()，才能使用this获取实例化对象。这是ES2015类的一个规则。

随着无状态函数（无状态函数没有内部状态）的应用和redux的使用，内部状态的使用正在逐渐减少。但是内部状态在非全局的数据管理更新中仍扮演着重要的角色。

### props

我们可以使用props向组件传递数据， React 组件从props中拿到数据。然后返回视图。

1. 使用props

向一个组件传递props的方法是将数据写在组件标签的属性中。

```
<Content value={this.state.value} />
```

获取props:

>> 在无状态函数中编写的组件中获取props，只需要将prps作为参数传入组件即可。

```
function Content(props) {
return <p>Content组件的props.value: {props.value} </p>;
}

const Title = ({ title }) => (<h1>{title}</h1>)
```

在使用类编写的组件中，需要通过this.props获取props。 this是组件实例。

2. 验证props

在一个健壮的程序中，任何输入都是需要验证的，组件也不例外。Props作为组件的输入，必须进行验证。验证props需要使用 * prop-typesr的PropTypes(React升级之后就不是React.propTypes了再用它会报错) * 。 它提供很多验证来验证传入的数据是否合法。当向props传入非法数据时，控制台会抛出警告。

> 请看propValidation页面

3. 组合使用state与props

> 为了演示如何通过props向里而的组件传递数据，我们编写了三个组件，分别是是Button、 Message 和 MessageList 。在最外层消息列表中定义一个color变量，然后通过props将color会给最里面的按钮组件。

```
import React, { Component } from 'react';
import PropTypes from 'prop-types'

function Content(props) {
  return <p>Content组件的props.value：{props.value} </p>;
}

Content.propTypes = {
  value: PropTypes.number.isRequired
};

export default class Counter extends Component {
  constructor(props){
    super(props);
    this.state = { value: 0 };
  }
  render() {
    return (
      <div>
        <button onClick={() => this.setState({ value: this.state.value + 1})}>增加</button>
        <pre>{this.state.value}</pre>
        <Content value={this.state.value}></Content>
      </div>
    )
  }
}

```

一般React应用当中的绝大多数数据都是prop，只有当用户输入内容时才会使用state来处理。


## context

> context 在React中是个比较不常用的概念。 但是因为后面react-redux会用到context，所以我们把它拿出来和props对比说一说。

```
import React from 'react'
import PropTypes from 'prop-types'

function Button(props){
  return (
    <button style={{backgroundColor: props.color}}>
      {props.children}
    </button>
  );
}

Button.propTypes = {
  color:PropTypes.string.isRequired,
  children:PropTypes.string.isRequired
}

function Message(props) {
  return (
    <li>
      {props.text} <Button color={props.color}>Delete</Button>
    </li>
  );
}

Message.propType= {
  text: PropTypes.string.isRequired,
  color:PropTypes.string.isRequired
};

function MessageList() {
  const color = 'gray';
  const messages = [
    {text: 'Hello React'},
    {text: 'Hello Redux'}
  ];
  const children = messages.map((message, id) =>
    <Message key={id} text={message.text} color={color}/>
  );
  return (
    <div>
      <p>通过props将color逐层传递给里面的Button组件</p>
      <ul>
        {children}
      </ul>
    </div>
  );
}

export default MessageList
```

消息组件并没有使用color，但是为了给按钮组件传递color，不得不从消息列表中接受color。这个例子只有三层组件，假如你的程序中有更深层次的组件结构，你一定不愿意逐层传递props.此时， context将帮助你解决这个问题。

### 使用 context 传递数据

与使用props相比， context 只需要两步即可实现跨级传递。每一步，将要传递的数据放在消息列表组件的context中。第二步，在按钮组件中声明contextTypes,就可以通过组件实例的context属性访问接收到的数据了。事实上，只要在一个组件中定义了context，这个组件里面的子组件，不管卡跨多少级，都可以访问到context中的数据。

```
import React from 'react'
import PropTypes from 'prop-types'

function Button(props, context){
  return (
    <button style={{backgroundColor: context.color}}>
      {props.children}
    </button>
  );
}

Button.propTypes = {
  children:PropTypes.string.isRequired
}

Button.contextTypes = {
  color: PropTypes.string.isRequired
}

function Message(props) {
  return (
    <li>
      {props.text} <Button>Delete</Button>
    </li>
  );
}

Message.propType= {
  text: PropTypes.string.isRequired
};

class ContextMessageList extends React.Component {
  getChildContext() {
    return {color: '#108ee9'};
  }
  render() {
  const messages = [
    {text: 'Hello React'},
    {text: 'Hello Redux'}
  ];
  const children = messages.map((message, id) =>
    <Message key={id} text={message.text} />
  );
  return (
    <div>
      <p>通过context将color逐层传递给里面的Button组件</p>
      <ul>
        {children}
      </ul>
    </div>
  );
  }
}
ContextMessageList.childContextTypes = {
  color:PropTypes.string.isRequired
}
export default ContextMessageList
```

> 注意：

1. 如果不声明childContextTypes，将无法在组件中使用getChildContext()方法

2. 如果 没有定义contextTypes, context将会是个空对象

3. 无状态组件可以直接在函数参数中获取context,需有状态组件可以通过this.context和生命周期函数获取context.

## props 与 context的适用场景

react 的context 和全局变量非常相似，在大多数场景下， 我们都应该尽量避免使用。 适合使用context的场景包括传递登录信息、当前语言以及主题信息。另外 react-redux的 Provider组件 就是使用了conext来传递store的。

如果只是传递一些功能模块的数据，则尽量不要使用context, 使用props传递会更加和容易理解，而使用context则会使你的组件的复用性降低，因为这些组件依赖“上下文”，当你在别的地方渲染它们的时候，可能会出现一些差异。


## 概念四：组件API

在之前的内容当中我们已经提及了render和setState两个方法，他们都包含在组件API方法之中。还有一个比较有用的方法constructor，我们一般会在其中初始化state并做一些方法的绑定。

我们并不会在这里展开篇幅讲解React的API 用到的时候可以去查。

[top-level-api](http://reactjs.cn/react/docs/top-level-api.html)

[component-api](http://reactjs.cn/react/docs/component-api.html)

[Component Specs and Lifecycle](http://reactjs.cn/react/docs/component-specs.html)

### 组件的生命周期

组件的生命周期可分成三个状态：

Mounting：已插入真实 DOM

Updating：正在被重新渲染

Unmounting：已移出真实 DOM

生命周期的方法有：

Mounting: componentWillMount  在渲染前调用。

Mounting: componentDidMount 在第一次渲染后调用。

Updating: componentWillReceiveProps 在组件接收到一个新的prop时被调用。这个方法在第一次渲染时不会被调用。

Updating: shouldComponentUpdate  返回一个布尔值。 在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。可以在你确认不需要更新组件时使用。

Updating: componentWillUpdate 在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。

Updating: componentDidUpdate  在组件完成更新后立即调用。在初始化是不被调用

Unmounting: componentWillUnmount  在组件从DOM中移除的时候立刻被调用。

### React 组件中的this

点击下面组件中的h1。

```
import React from 'react';

const suffix = '被调用，this指向：';
export default class TestThis extends React.Component {
  handler() {
    console.log(`handler${suffix}`, this);
  }
  render() {
    console.log(`render${suffix}`, this);
    return (
      <div>
        <h1 onClick={this.handler}>测试this指向</h1>
      </div>
    );
  }
}

```

结果是render()函数中的this指向了组件实例，需handler()函数中的this则是一个null。那React组件中的this到底是什么呢？

js函数中的this不是在函数定义的时候定义的而是在函数运行时定义的，React组件也遵循了这种特性，所以组件方法的“调用者”不同会导致this不同。注这里的“调用者”指函数执行时候的当前对象。

让我们分别在组件自带的生命周期函数以及自定义的handler()方法中打印this,并在render()方法里分别使用this.handler()、window.handler()、onClick={this.handler}这三种方法调用handler(),看看this会有什么不同。

```
import React from 'react';

const suffix = '被调用，this指向：';

export default class TestThis extends React.Component {
  constructor(props) {
    super(props);
    this.handler = this.handler.bind(this)
  }
  componentDidMount() {
    console.log(`componentDidMount${suffix}`, this)
  }
  componentWillReceiveProps() {
    console.log(`componentWillReceiveProps${suffix}`, this)
  }
  shoudComponentUpdate() {
    console.log(`shoudComponentUpdate${suffix}`, this);
    return true;
  }
  componentDidUpdate() {
    console.log(`componentDidUpdate${suffix}`, this);
  }
  componentWillUnmount() {
    console.log(`componentWillUnmount${suffix}`, this)
  }
  handler() {
    console.log(`handle${suffix}`, this)
  }

  render() {
    console.log( `render${suffix}`, this);
    this.handler();
    window.handler = this.handler;
    window.handler();
    return (
      <div>
        <h1 onClick={this.handler}>测试this指向</h1>
        <p></p>
      </div>
    )
  }
}

```

React组件生命周期函数中的this指向组件实例。自定义组件方法的this会因“调用者”不同而不同。为了在组件的自定义方法中获取组件实例，需要手动绑定this到组件实例

```
constructor(props){
super(props);
this.handler = this.handler.bind(this)
```
## 概念五：组件类型

我们在React当中一般按照如下的方法定义一个组件

```
class MyComponnet extends Component {
render() {
return <p>Hello world</p>
}
}
```

在Class中我们还可以申明一个组件的许多其他方法，而在更多的情况下我们可以写一种函数式组件。
类似于自定义一个模板标签一样，函数式组件接收一个props参数并返回特定的HTML内容，不过你当然仍可以在其中调用一些JS代码：

```
const MyComponent = props => {
return <p>hello {props.name}! Today is {new Date()}</p>
}
```
因为通常你的组件可能并不需要多么复杂的交互，也不需要多余的其他方法，用函数式写法可以让你的代码更加简洁。

当然在这样的组件当中你也没有办法使用setState方法，也即是说函数式组件没有state，所以也可以被称作是无状态组件。

不同的组件类型也就延伸出了组件角色的概念，人们在实践过程中开始将组件分为两种角色，一种关注UI逻辑，用来展示或隐藏内容；另一种关注数据交互，例如加载服务器端的数据。

这两种组件被称作容器组件和展示组件。分别用来处理不同的业务逻辑：

```
//展示组件
class CommentList extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <ul>
        this.props.comments.map((body, author) => {
        return <li>{body}------{author}</li>
        })
      </ul>
    )
  }
}

//容器组件

class CommentListContainer extends React.Component {
  constructor(props) {
    super(props);
    this.state= {comments: []};
  }
  componentDidMount() {
    $ajax({
      url:'/mycomments.json',
      dataType:'json',
      success:function(comments) {
        this.setState({comments:comments});
      }.bind(this)
    });
  }
  render() {
    return (
       <CommentList comments={this.state.comments} />
    )
  }
}
```
## 组件的嵌套

使用React来构建web应用，每个页面都将是多个组件组成，并且相互嵌套来构成的, 组件的嵌套的本质就是父子关系，我们来看一下父子之间是怎么传递数据的。

1. 父组件向子组件通信--前面我们学过了是用props来传递的，父组件把一些数据通过props传递给子组件，子组件对这些props进行一些操作，例如保存为状态或者进行一些渲染之类的。

2. 子组件向父组件通信--父组件可以把一些事件处理函数通过props的方式传给子组件，子组件如果触发了事件就可以调用父组件的事件处理函数。这样间接的实现了子组件和父组件的通信。

```
import React, { Component } from 'react';

class GenderSelect extends Component {
  render() {
    return (
      <select onChange={this.props.handleChange}>
          <option value="">请选择</option>
          <option value="0">女</option>
          <option value="1">男</option>
      </select>
    )
  }
}

export default  class SignupForm extends Component {
    constructor(props) {
      super(props);
      this.state = {name:'', password:'', gender:''};
    }
    handleChange = (name, event) => {
      let newState = {};
      newState[name] = event.target.value;
      this.setState(newState);
    }
    handleSelect = (event) => {
      this.setState({gender: event.target.value});
    }
    render() {
      console.log(this.state);
      return (
          <form>
            <input type="text" placeholder="请输入用户名" onChange={this.handleChange.bind(this, 'name')}/>
            <input type="password" placeholder="请输入密码" onChange={this.handleChange.bind(this, 'password')}/>
            <GenderSelect handleChange={this.handleSelect}></GenderSelect>
          </form>
      )
    }
  }

```

到此React基础知识就介绍完了，当然,这些知识都只是React知识体系中的一部分。在日常开发过程中，如果遇到不能解决的问题，仍然 需要查阅[官方文档](https://facebook.github.io/react/docs/refs-and-the-dom.html)。

