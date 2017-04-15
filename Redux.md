## 四大基本概念

- Action

- reducer

- state

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
