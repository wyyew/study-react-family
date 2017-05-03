### 提高 selector 的性能

把 react 与 redux 结合的时候，react-redux 提供了一个极其重要的方法：connect，它的作用就是选取 redux store 中的需要的 state 与 dispatch, 交由 connect 去绑定到 react 组件的 props 中：
