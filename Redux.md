### Redux 的设计思想

（1）Web 应用是一个状态机，视图与状态是一一对应的。

（2）所有的状态，保存在一个对象里面。

## 四大基本概念

- Action

- reducer

- 纯函数

- store

## 常用api

### 概念一 Action

> Action本质上是Js普通对象。我们约定，action 内使用一个字符串类型的type字段来表示将要执行的动作。多数情况下，type会被定义成字符串常量。
另外，当应用规模越来越大时，建议使用单独的模块或文件来存放action。除了type字段外，action对象的结构完全由你自己决定。

```
{type:'INCREMENT'}
{type:'DECREMENT'}
```

### 概念二 reducer

reducer是一个形式为(state, action) => state 的纯函数，描述了action如何把state转变成下一个state.

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

- actionCreators (Function or Object): 一个 action creator，或者键值是 action creators 的对象。

- dispatch (Function): 一个 dispatch 函数，由 Store 实例提供。

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
