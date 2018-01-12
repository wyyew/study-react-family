## dva入门

- 易学易用：仅有 6 个 api，对 redux 用户尤其友好

- elm 概念：通过 reducers, effects 和 subscriptions 组织 model

- 支持 mobile 和 react-native：跨平台 ([react-native](https://github.com/sorrycc/dva-example-react-native) 例子)

- 支持 HMR：目前基于 [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr) 支持 components、routes 和 models 的 HMR

- 动态加载 Model 和路由：按需加载加快访问速度 ([例子](https://github.com/dvajs/dva/blob/master/packages/dva-example-user-dashboard/src/router.js))

- 插件机制：比如 dva-loading 可以自动处理 loading 状态，不用一遍遍地写 showLoading 和 hideLoading

- 完善的语法分析库 [dva-ast：](https://github.com/dvajs/dva-ast) [dva-cli](https://github.com/dvajs/dva-cli) 基于此实现了智能创建 model, router 等

- 支持 TypeScript：通过 d.ts ([例子](https://github.com/sorrycc/dva-boilerplate-typescript))

### Demos

- [Count:](https://stackblitz.com/edit/dva-example-count) 简单计数器

- [User Dashboard:](https://github.com/dvajs/dva/tree/master/packages/dva-example-user-dashboard) 用户管理

- [HackerNews:](https://github.com/dvajs/dva-hackernews) ([Demo](https://dvajs.github.io/dva-hackernews/#/top?_k=743noh))，HackerNews Clone

- [antd-admin:](https://github.com/zuiidea/antd-admin) ([Demo](http://antd-admin.zuiidea.com/login?from=%2F))，基于 antd 和 dva 的后台管理应用

- [github-stars:](https://github.com/sorrycc/github-stars) ([Demo](http://sorrycc.github.io/github-stars/#/?_k=rmj86f))，Github Star 管理应用

- [react-native-dva-starter:](https://github.com/nihgwu/react-native-dva-starter) 集成了 dva 和 react-navigation 典型应用场景的 React Native 实例

- [dva-example-nextjs:](https://github.com/dvajs/dva/tree/master/packages/dva-example-nextjs) 和 next.js 整合使用

- [Account System:](https://github.com/yvanwangl/AccountSystem) 小型库存管理系统

### 初始化环境配置

> > .roadhogrc 

```
{
  "entry": "src/index.js",
  "env": {
    "development": {
      "extraBabelPlugins": [
        "dva-hmr",
        "transform-runtime",
        ["import",{"libraryName":"antd","style":true}]
      ]
    },
    "production": {
      "extraBabelPlugins": [
        "transform-runtime",
        ["import",{"libraryName":"antd","style":true}]
      ]
    }
  }
}
```

### 初始化dva

```
import dva from 'dva';
import './index.css';

// 1. Initialize
const app = dva();

// 2. Plugins
// app.use({});

// 3. Model
// app.model(require('./models/example'));

// 4. Router
app.router(require('./router'));

// 5. Start
app.start('#root');
```

- const app = dva()

 这部分是用来做dva初始化的部分 先给大家看一下完整的接口
 
 ```
 const  app = dva({
  history,
  initialState,
  onError,
  onAction,
  onStateChange,
  onReducer,
  onEffect,
  onHmr,
  extraReducers,
  extraEnhancers,
 })
 
 ```
 > 在这里 你可以设置全局state 全部error 还有包括router的事件 state的事件 等等,都可以直接统一的在这边进行设置与管理,还有history这个参数是从react-router中来的
 
 > 常用的 history 有三种形式，但是你也可以使用React-Router实现自定义的history
 
 - browserHistory
 
 - hashHistory
 
 - createMemoryHistory
 
 相关地址:[react-router dva](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md)初始化

- app.use(hooks)

> 配置 hooks 或者注册插件。（插件最终返回的是 hooks ）
  
  比如注册 [dva-loading](https://github.com/dvajs/dva/tree/master/packages/dva-loading) 插件的例子：
  
  ```
  import createLoading from 'dva-loading';
  ...
  app.use(createLoading(opts));

  ```
hooks 包含：

```

onError((err, dispatch) => {})

```

effect 执行错误或 subscription 通过 done 主动抛错时触发，可用于管理全局出错状态。

>**注意：**subscription 并没有加 try...catch，所以有错误时需通过第二个参数 done 主动抛错。例子：

```
app.model({
  subscriptions: {
    setup({ dispatch }, done) {
      done(e);
    },
  },
});
```

如果我们用 antd，那么最简单的全局错误处理通常会这么做：

```

 import { message } from 'antd';
 const app = dva({
  onError(e) {
    message.error(e.message, /* duration */3);
  },
});
 
 ```
 
 ```
 
 onAction(fn | fn[])
 
 ```
 
 在 action 被 dispatch 时触发，用于注册 redux 中间件。支持函数或函数数组格式。
 
 例如我们要通过 [redux-logger](https://github.com/evgenyrodionov/redux-logger) 打印日志：
 
 ```
 
 import createLogger from 'redux-logger';
const app = dva({
  onAction: createLogger(opts),
});

```

```
onStateChange(fn)

```

state 改变时触发，可用于同步 state 到 localStorage，服务器端等

```

onReducer(fn)


```

封装 reducer 执行。比如借助 redux-undo 实现 redo/undo ：

```

import undoable from 'redux-undo';
const app = dva({
  onReducer: reducer => {
    return (state, action) => {
      const undoOpts = {};
      const newState = undoable(reducer, undoOpts)(state, action);
      // 由于 dva 同步了 routing 数据，所以需要把这部分还原
      return { ...newState, routing: newState.present.routing };
    },
  },
});

```

**onEffect(fn)**

封装 effect 执行。比如 [dva-loading](https://github.com/dvajs/dva/tree/master/packages/dva-loading) 基于此实现了自动处理 loading 状态。

**onHmr(fn)**

热替换相关，目前用于 [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr) 。

**extraReducers**

指定额外的 reducer，比如 [redux-form](https://github.com/erikras/redux-form) 需要指定额外的 form reducer：

```

import { reducer as formReducer } from 'redux-form'
const app = dva({
  extraReducers: {
    form: formReducer,
  },
});

```

**extraEnhancers**

指定额外的 [StoreEnhancer](https://github.com/reactjs/redux/blob/master/docs/Glossary.md#store-enhancer) ，比如结合 [redux-persist](https://github.com/rt2zz/redux-persist) 的使用：

```

import { persistStore, autoRehydrate } from 'redux-persist';
const app = dva({
  extraEnhancers: [autoRehydrate()],
});
persistStore(app._store);

```

**app.model(model)**

这个是用来接收你发送的action的,model 是 dva 中最重要的概念。以下是典型的例子：

```

app.model({
  namespace: 'todo',
	state: [],
  reducers: {
    add(state, { payload: todo }) {
      // 保存数据到 state
      return [...state, todo];
    },
  },
  effects: {
    *save({ payload: todo }, { put, call }) {
      // 调用 saveTodoToServer，成功后触发 `add` action 保存到 state
      yield call(saveTodoToServer, todo);
      yield put({ type: 'add', payload: todo });
    },
  },
  subscriptions: {
    setup({ history, dispatch }) {
      // 监听 history 变化，当进入 `/` 时触发 `load` action
      return history.listen(({ pathname }) => {
        if (pathname === '/') {
          dispatch({ type: 'load' });
        }
      });
    },
  },
});

```

model 包含 5 个属性：

- **namespace**

> model 的命名空间，同时也是他在全局 state 上的属性，只能用字符串，不支持通过 . 的方式创建多层命名空间。


