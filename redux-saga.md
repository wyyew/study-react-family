## 基本概念

1. Middleware API

2. Effect 创建器

3. Effect 组合器

4. 接口

5. 外部 API

### API页面

- [En](https://redux-saga.js.org/docs/api/index.html#delayms-val)

- [cn](http://leonshi.com/redux-saga-in-chinese/docs/api/index.html#saga-helpers)

> Redux-saga是Redux的一个中间件，主要集中处理react架构中的异步处理工作，被定义为generator(ES6)的形式，采用监听 action 来执行有副作用的 task，以保持 action 的简洁性.(函数副作用 指当调用函数时，除了返回函数值之外，还对主调用函数产生附加的影响。例如修改全局变量（函数外的变量）或修改参数。函数副作用会给程序设计带来不必要的麻烦，给程序带来十分难以查找的错误，并且降低程序的可读性。严格的函数式语言要求函数必须无副作用。)
函数的副作用相关的几个概念， Pure Function、 Impure Function、 Referential Transparent。
JS中要想保证函数无副作用这项特性，只能依靠编程人员的习惯，即

> 函数入口使用参数运算，而不修改它

> 函数内不修改函数外的变量，如全局变量

> 运算结果通过函数返回给外部（出口）

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
  
  #### 返回
  
   这个方法返回一个 Task 描述对象
    
```
...
const sagaMiddleware = createSagaMiddleware()
const store = createStore(
  rootReducer,
  applyMiddleware(sagaMiddleware)
)
sagaMiddleware.run(rootSaga)
....
```

### Effect 创建器

Effect 是一个 javascript 对象，里面包含描述副作用的信息，可以通过 yield 传达给 sagaMiddleware 执行。

> 在 redux-saga 世界里，所有的 Effect 都必须被 yield 才会执行,并且原则上来说，所有的 yield 后面也只能跟Effect.

> 注意

以下每个函数都会返回一个 plain Javascript object (纯文本 Javascript 对象) 并且不会执行任何其它的操作。 执行是由 middleware 在上述迭代过程中进行的。 middleware 检查每个 Effect 的信息，并进行相应的操作。

   1. put（产生一个 action）
   
   ```
   put(action)
   ```
  #### 作用
   
      创建一条 Effect 描述信息，指示 middleware 发起一个 action 到 Store。
      
      > 作用和 redux 中的 dispatch 相同。

  #### 参数
   
      action: Object 
      
  #### 注意
  
   put 执行是异步的。即作为一个单独的 microtask，因此不会立即发生。

   2. call（阻塞地调用一个函数）
   
   ```
   call(fn, ...args)
   ```
   
  #### 作用
  
   创建一条 Effect 描述信息，指示 middleware 调用 fn 函数并以 args 为参数。
   
  #### 参数

   - fn: Function - 一个 Generator 函数, 或者返回 Promise 的普通函数

   - args: Array - 一个数组，作为 fn 的参数
   
   #### 注意

      fn 既可以是一个普通函数，也可以是一个 Generator 函数。

   3. fork（非阻塞地调用一个函数）
   
   ```
   fork(fn, ...args)
   ```
   
   #### 作用
   
   创建一条 Effect 描述信息，指示 middleware 以 无阻塞调用 方式执行 fn。

   #### 参数

     - fn: Function - 一个 Generator 函数, 或者返回 Promise 的普通函数

     - args: Array - 一个数组，作为 fn 的参数
    
   #### 注意
  
  fork 类似于 call，可以用来调用普通函数和 Generator 函数。但 fork 的调用是无阻塞的，在等待 fn 返回结果时，middleware 不会暂停 Generator。 相反，一旦 fn 被调用，Generator 立即恢复执行。

   4. take（监听且只监听一次 action）
   
   #### 作用
 
   创建一条 Effect 描述信息，指示 middleware 等待 Store 上指定的 action。 Generator 会暂停，直到一个与 pattern 匹配的 action 被发起。
   
   #### 用以下规则来解释 pattern：

    - 如果调用 take 时参数为空，或者传入 '*'，那将会匹配所有发起的 action（例如，take() 会匹配所有的 action）。

    - 如果是一个函数，action 会在 pattern(action) 返回为 true 时被匹配（例如，take(action => action.entities) 会匹配那些 entities 字段为真的 action）。

    - 如果是一个字符串，action 会在 action.type === pattern 时被匹配（例如，take(INCREMENT_ASYNC)）。

    - 如果参数是一个数组，会针对数组所有项，匹配与 action.type 相等的 action（例如，take([INCREMENT, DECREMENT]) 会匹配 INCREMENT 或 DECREMENT 类型的 action）。

   5. delay（延迟）delay(ms, [val]) 
   
   #### 作用
   
     可以直接使用 delay 来替代 setTimeout 这种会造成回调和嵌套的不优雅的方法。
   
   
   #### 参数
   
     ms： 在ms毫秒之后执行.
   
   #### 返回一个Promise
   
   ```
   setTimeout(_ => dispatch({ type: 'HIDE_ERROR' }), 2000);
   
   yield delay(2000);
	yield put({ type: 'HIDE_ERROR' });
   ```
   
   6. select(获取 Store state 上的数据)
   
   #### 作用
   
   创建一条 Effect 描述信息，指示 middleware 调用提供的选择器获取 Store state 上的数据（例如，返回 selector(getState(), ...args) 的结果）。
   
   > 作用和 redux thunk 中的 getState 相同。
   
   #### 参数

   - selector: Function - 一个 (state, ...args) => args 函数. 通过当前 state 和一些可选参数，返回当前 Store state 上的部分数据。

   - args: Array - 可选参数，传递给选择器（附加在 getState 后）
   
   #### 注意

   如果 select 调用时参数为空（即 yield select()），那 effect 会取得整个的 state（和调用 getState() 的结果一样）。
   
### Effect 组合器

   1. race（只处理最先完成的任务）
   
   #### 作用
  
   创建一条 Effect 描述信息，指示 middleware 在多个 Effect 之间执行一个 race（类似 Promise.race([...]) 的行为）。
   
   #### 参数
  
   effects: Object - 一个 {label: effect, ...} 形式的字典对象
   
   #### 重要提醒
  
  在发起 action 到 store 时，middleware 首先会转发 action 到 reducers 然后通知 Sagas。这意味着，当你查询 Store 的 state， 你获取的是 action 被处理之后的 state。

 并且通过 Generator 实现对于这些副作用的管理，让我们可以用同步的逻辑写一个逻辑复杂的异步流。


