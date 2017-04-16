## 四大基本概念

- Action

- reducer

- 纯函数

- store

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