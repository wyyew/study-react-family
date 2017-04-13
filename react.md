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

这其实就是React能够帮你做到的。React组件能够像原生的HTML标签一样输出特定的界面元素，并且也能包括一些元素相关逻辑功能的代码。

现在我们一般会用ES6的Class语法来声明一个React组件，它包含一个能够返回HTML的render方法。（当然也可以用函数声明，我们在之后会聊到）

```
class MyComponent extends Component {
  render() {
    return (
      <p>Hello React!</p>
    )
  }
}
```
