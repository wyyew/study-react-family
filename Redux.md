## 前导

首先明确一点，Redux 是一个有用的架构，但不是非用不可。事实上，大多数情况，你可以不用它，只用 React 就够了。

曾经有人说过这样一句话。

> "如果你不知道是否需要 Redux，那就是不需要它。"

Redux 的创造者 Dan Abramov 又补充了一句。

> "只有遇到 React 实在解决不了的问题，你才需要 Redux 。"

简单说，如果你的UI层非常简单，没有很多互动，Redux 就是不必要的，用了反而增加复杂性。

- 用户的使用方式非常简单

- 用户之间没有协作

- 不需要与服务器大量交互，也没有使用 WebSocket

- 视图层（View）只从单一来源获取数据

上面这些情况，都不需要使用 Redux。

- 用户的使用方式复杂

- 不同身份的用户有不同的使用方式（比如普通用户和管理员）

- 多个用户之间可以协作

- 与服务器大量交互，或者使用了WebSocket

- View要从多个来源获取数据

上面这些情况才是 Redux 的适用场景：多交互、多数据源。

从组件角度看，如果你的应用有以下场景，可以考虑使用 Redux。

- 某个组件的状态，需要共享

- 某个状态需要在任何地方都可以拿到

- 一个组件需要改变全局状态

- 一个组件需要改变另一个组件的状态

发生上面情况时，如果不使用 Redux 或者其他状态管理工具，不按照一定规律处理状态的读写，代码很快就会变成一团乱麻。你需要一种机制，可以在同一个地方查询状态、改变状态、传播状态的变化。

> React 只是 DOM 的一个抽象层，并不是 Web 应用的完整解决方案。有两个方面，它没涉及。

- 代码结构

- 组件之间的通信

对于大型的复杂应用来说，这两方面恰恰是最关键的。因此，只用 React 没法写大型应用


### Redux 的设计思想

（1）Web 应用是一个状态机，视图与状态是一一对应的。

（2）所有的状态，保存在一个对象里面。

Redux 把界面视为一种状态机，界面里的所有状态、数据都可以由一个状态树来描述。所以对于界面的任何变更都简化成了状态机的变化。

## 四大基本概念

- Action --- 所谓的 action，就是用一个对象描述发生了什么

- reducer --- 这个 action 对象和当前的状态树 state 会被传入到 reducer 中，产生一个新的 state

- 纯函数

- store --- store 的作用就是储存 state，并且监听其变化。

### 概念一 Action

> Action本质上是Js普通对象。我们约定，action 内使用一个字符串类型的type字段来表示将要执行的动作。多数情况下，type会被定义成字符串常量。
另外，当应用规模越来越大时，建议使用单独的模块或文件来存放action。除了type字段外，action对象的结构完全由你自己决定。

```
{type:'INCREMENT'}
{type:'DECREMENT'}
```

### 概念二 reducer

Reducer 函数最重要的特征是，它是一个纯函数。也就是说，只要是同样的输入，必定得到同样的输出。

reducer是一个形式为(state, action) => state 的纯函数，描述了action如何把当前state转变成下一个state.

```
function counter(state = 0, action) {
    switch (action.type) {
        case 'INCREMENT':
          return state + 1;
        case 'DECREMENT':
          return state - 1;
        default:
          return state;
    }
}
```

state 的形式取决于你，可以是基本类型、数组、对象，甚至是Immutable.js生成的数据结构。唯一的要点是当state变化时需要返回全新的对象，而不是修改传入的参数。

### 纯函数

> 纯函数是这样一种函数，输入/输出的数据流全是显式的。显式的意思就是函数与外界交换数据只有一个唯一渠道--参数和返回值。函数从函数外面接受的所有输入信息都通过参数传递到该函数内部，函数输出到函数外部的所有信息都通过返回值传递到该函数的外部。

纯函数不能访问外部变量，它能接触的“外地人”只有来自外部的参数。纯函数不能修改参数，因为这样做可能会把一些信息通过输入参数，夹带到外界。

> Reducer 就是这样的函数，永远不要在Reducer 里做这些操作:

1. 修改传入的参数

2. 执行有副作用的操作，如API请求和路由跳转。

3. 调用非纯函数，如Date.now() 或 Math.random().

函数副作用会给程序设计带来不必要的麻烦，产生难以查找的错误，并且降低程序的可读性。严格的函数式语言要求函数必须无副作用。

由于 Reducer 是纯函数，就可以保证同样的State，必定得到同样的 View。但也正因为这一点，Reducer 函数里面不能改变 State，必须返回一个全新的对象，请参考下面的写法。

```
// State 是一个对象
function reducer(state, action) {
  return Object.assign({}, state, { thingToChange });
  // 或者
  return { ...state, ...newState };
}

// State 是一个数组
function reducer(state, action) {
  return [...state, newItem];
}
```

### store

1. 职能
   
> store 是个全局的对象，将action和以及state联系在一起。store有哪些职能：

- 维持应用的state

- 提供getState() 方法获取state

- 提供dispatch(action) 方法更新state.

- 通过subscribe(listener)注册监听器

2. 创建

> 创建store需要从redux包中导入createStore这个方法：

```
import { creatStore } from 'redux
```

使用reducer纯函数作为每一个参数创建store:

let store = createStore(reducer);

3. 获取与监听

创建完store 使用store.subscribe获取数据，并监听变化：

```
const store = crearteStore(counter);

let currentValue = store.getState();

store.subscribe(() => {
    const previusValue = currentValue;
    currentValue = store.getState();
    console.log('pre state:', previousValue, 'next state:', currentValue);
})
```

### 发起Action 

store 使用dispatch(action)方法发起action，更新state:

```
store.dispatch({type:'INCREMENT'});
```

当发起action 后，就将action传进了store中，使用reducer纯函数执行更新。改变内部state唯一方法是dispatch一个action.这样确保了视图和网络请求都不能直接修改state, 相反它们只能表达想要修改的意图，也就是dispatch一个action.

通向罗马的路有千万条，可惜最后都集中到dispatch这一条路上面了。集中处理的好处很明显，如果你在这条路上放一个日志中间件，所有的变化都会被打印。而且不止打印日志这个功能，社区为我们提供了丰富的中间件，你可以选择需要的放在dispatch这条路上。

```
import { creatStore } from 'redux';

function counter(state = 0, action) {
    switch(action.type) {
        case 'INCREMENT'：
            return state + 1;
         case 'DECREMENT'
          return state - 1;
         default:
            return state;
   }
}

const store = createStore(counter);

let currentValue = store.getState();

const listener = () => {
    const previusValue = currentValue;
    currentValue = store.getState();
    console.log('pre state:', previousValue, 'next state:', currentValue);
}


 store.subscribe(listener);
 
 store.dispatch({type: 'INCREMENT'});
 store.dispatch({type: 'INCREMENT'});
 store.dispatch({type: 'DECREMENT'});
```

### 常用api

- createStore(reducer, [preloadedState], [enhancer])

作用： 创建一个 Redux store 来以存放应用中所有的 state。应用中应有且仅有一个 store。

参数：

1. reducer (Function): 接收两个参数，分别是当前的 state 树和要处理的 action，返回新的 state 树。

2. [preloadedState] (any): 初始时的 state。 在同构应用中，你可以决定是否把服务端传来的 state 混合（hydrate）后传给它，或者从之前保存的用户会话中恢复一个传给它。如果你使用 combineReducers 创建 reducer，它必须是一个普通对象，与传入的 keys 保持同样的结构。否则，你可以自由传入任何 reducer 可理解的内容。

3. [enhancer] (Function): Store enhancer 是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。这与 middleware 相似，多个middleware要用applyMiddleware()把它们组合起来。.

返回值：(Store) ，保存了应用所有 state 的对象。改变 state 的惟一方法是 dispatch action。你也可以 subscribe 监听 state 的变化，然后更新 UI。

- combineReducers(reducers)

作用是，把一个由多个不同 reducer 函数作为 value 的 object，合并成一个最终的 reducer 函数，然后就可以对这个 reducer 调用 createStore。

合并后的 reducer 可以调用各个子 reducer，并把它们的结果合并成一个 state 对象。state 对象的结构由传入的多个 reducer 的 key 决定。

最终，state 对象的结构会是这样的：

```
{
  reducer1: ...
  reducer2: ...
}
```

参数：

reducers (Object): 一个对象，它的值（value） 对应不同的 reducer 函数，这些 reducer 函数后面会被合并成一个。

返回值

(Function)：一个调用 reducers 对象里所有 reducer 的 reducer，并且构造一个与 reducers 对象结构相同的 state 对象。

```
//createStore(combineReducers(...), initialState)


import { combineReducers } from 'redux'
import todos from './todos'
import counter from './counter'

export default combineReducers({
  todos,
  counter
})
```

- applyMiddleware(...middlewares)

作用：它可以把多个middleware 一次性的作为createStore的加强函数。

参数

...middlewares: 遵循 Redux middleware API 的函数。

返回值

(Function) 一个应用了 middleware 后的 store enhancer。这个 store enhancer 的签名是 createStore => createStore，但是最简单的使用方法就是直接作为最后一个 enhancer 参数传递给 createStore() 函数。

- bindActionCreators(actionCreators, dispatch)

作用：

把 action creators 转成拥有同名 keys 的对象，但使用 dispatch 把每个 action creator 包围起来，这样可以直接调用它们。

参数

1. actionCreators (Function or Object): 一个 action creator，或者键值是 action creators 的对象。

2. dispatch (Function): 一个 dispatch 函数，由 Store 实例提供。

返回值

(Function or Object): 一个与原对象类似的对象，只不过这个对象中的的每个函数值都可以直接 dispatch action。如果传入的是一个函数作为 actionCreators，返回的也是一个函数。

- compose(...functions)

作用：　从右到左来组合多个函数。　当需要把多个 store 增强器 依次执行的时候，需要用到它。

参数

(...functions): 需要合成的多个函数。预计每个函数都接收一个参数。它的返回值将作为一个参数提供给它左边的函数，以此类推。另外是最右边的参数可以接受多个参数，因为它将为由此产生的函数提供签名。（译者注：compose(funcA, funcB, funcC) 形象为 

```
compose(funcA(funcB(funcC())))）

const absAndRound = compose(Math.round, Math.abs);
==
const absAndRound = val => Math.round(Math.abs(val))

```

返回值

(Function): 从右到左把接收到的函数合成后的最终函数。


### 总结

- action 是个js对象，它是store数据的唯一来源。

- reducer 是 纯函数， 不要在中做这些事情： 修改传入参数;执行有副作用的操作；调用非纯函数。

- store负责更新、查询、订阅state等多个工作。store是全局唯一的，它将action、 reducer 、 state 等联系在一起。
