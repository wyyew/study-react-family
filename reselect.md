### 提高 selector 的性能

> 把 react 与 redux 结合的时候，react-redux 提供了一个极其重要的方法：connect，它的作用就是选取 redux store 中的需要的 state 与 dispatch, 交由 connect 去绑定到 react 组件的 props 中：

```
import { connect } from 'react-redux';
import { toggleTodo } from '../actions'
import TodoList from '../components/TodoList'

// 我们需要向 TodoList 中注入一个名为 todos 的 prop
// 它通过以下这个函数从 state 中提取出来：
const mapStateToProps = (state) => {
    // 下面这个函数就是所谓的selector
    todos: state.todos.filter(i => i.completed)
    // 其它props...
}

const mapDispatchToProps = (dispatch) => {
	onTodoClick: (id) => {
		dispatch(toggleTodo(id))
	}
}

// 绑定到组件上
const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```

在这里需要指定哪些 state 属性被注入到 component 的 props 中，这是通过一个叫 selector 的函数完成的。

上面这个例子存在一个明显的性能问题，每当组件有任何更新时都会调用一次 state.todos.filter 来计算 todos，但我们实际上只需要在 state.todos 变化时重新计算即可，每次更新都重算一遍是非常不合适的做法。下面介绍的这个 reselect 就能帮你省去这些没必要的重新计算。

你可能会注意到，selector 实际上就是一个『纯函数』：

```
selector(state) => some props
```

而纯函数是具有可缓存性的，即对于同样的输入参数，永远会得到相同的输出值,reselect 的原理就是如此，每次调用 selector 函数之前，它会判断参数与之前缓存的是否有差异，若无差异，则直接返回缓存的结果，反之则重新计算：
