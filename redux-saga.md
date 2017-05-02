## 基本概念

1. Middleware API

2.Effect 创建器

3. Effect 组合器

4. 接口

5. 外部 API


> Redux-saga是Redux的一个中间件，主要集中处理react架构中的异步处理工作，被定义为generator(ES6)的形式，采用监听的形式进行工作

### Middleware API


1. createSagaMiddleware(...sagas)

   #### 作用

     创建一个 Redux 中间件，将 Sagas 与 Redux Store 建立连接。

   #### 参数

    sagas: Array - Generator 函数列表

    - sagas 中的每个 Generator 函数被调用时，都会被传入 Redux Store 的 getState 方法作为第一个参数。

    - sagas 中的每个函数都必须返回一个 Generator 对象。 middleware 会迭代这个 Generator 并执行所有 yield 后的 Effect.

2. middleware.run(saga, ...args)

  #### 作用

    动态执行 saga。用于 applyMiddleware 阶段之后执行 Sagas。
  
  #### 参数

    - saga: Function: 一个 Generator 函数
    
    - args: Array: 提供给 saga 的参数 (除了 Store 的 getState 方法)
    
  #### 返回值
  
    这个方法返回一个 Task 描述对象
    
```
...
import createSagaMiddleware from 'redux-saga';
import trunk from 'redux-thunk'

export default function configureStore() {
  const sagaMiddleware = createSagaMiddleware()
  return {
    ...createStore(rootReducer,
      compose(
        applyMiddleware(sagaMiddleware, createLogger, trunk),
        window.devToolsExtension ? window.devToolsExtension() : f => f
      )
    ),
    runSaga: sagaMiddleware.run
  }
} 

....

import rootSaga from './sagas'

const store = configureStore();
const states = store.getState();
store.runSaga(rootSaga)
....
```

### Effect 创建器

> 注意

以下每个函数都会返回一个 plain Javascript object (纯文本 Javascript 对象) 并且不会执行任何其它的操作。 执行是由 middleware 在上述迭代过程中进行的。 middleware 检查每个 Effect 的信息，并进行相应的操作。
