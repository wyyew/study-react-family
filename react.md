## 五大核心概念：

- 组件

- JSX

- Props & State & context

- 组件API

- 组件类型

### 概念一：React组件的作用

你需要了解有关React的第一要点就是——组件。你编写的所有React代码基本上就是一个包含许多小组件在内的大组件。

那么到底什么是组件呢？我们可以拿HTML标签 <select> 来举一个很恰当的例子。原生的下拉框标签不止包括边框、文本、下拉箭头，它还掌控着自身打开关闭的逻辑。

图0

现在来设想一下你需要构建一个你自定义样式和行为逻辑的<select>：

图片1

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

JSX乍看起来可能很奇怪，不过你慢慢会习惯的。

是的我知道，按照我们以往的传统，应该尽量把HTML和JavaScript的代码分开才是。不过看样子现在忘记这教条才是提高你前端开发效率的正道。

我们还是来举几个JSX实际应用的例子吧，比如：

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

你可以通过{}大括号来在JSX中显示JS变量：

```
class MyComponent extends React.Component {
  render() {
    return (
      <h3>JS表达式</h3>
      <p>今天是:{new Date()}</p>
    )
  }
}
```

你不再需要什么前端模板标签之类的东西了，你可以直接在JSX中使用三元运算符一类的逻辑：

```
class MyComponent extends React.Component {
  render() {
    return (
      <p>Hello {this.props.someVar ? 'world' : 'Kitty'}</p>
    )
  }
}
```
样式分为内联样式、内嵌样式、链接式等，这里证讲一下内联样式的写法。

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
