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

现在我们一般会用ES6的Class语法来声明一个React组件，它包含一个能够返回HTML的render方法。（当然也可以用函数声明，我们在之后会聊到）

```
class MyComponent extends React.Component {
  render() {
    return (
      <p>Hello React!</p>
    )
  }
}
```
### 概念二：JSX是什么玩意儿？

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
