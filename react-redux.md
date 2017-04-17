## 两大核心概念

- Provider

- connect 方法

### Provider

所有组件的顶层使用Provider组件给整个程序提供store.

```
import { Provider } from 'react-redux;
...

ReactDom.render(
  <Provider store={store}>
  ...
  </Provider>
)
```

### connect

使用connect()将state和action创建函数绑定到组件中

```
import Counter from '../components/Counter';
import { connect } from 'react-redux';
import * as ActionCreatores from '../actions';

export default connect(
  state => ({ counter: state.counter }),
  ActionCreators
)(Counter)
```

connect 第一个参数是一个函数， 它将state.counter传递给组件的counter属性。connect()的第二个是个对象，这个对象是所有action创建函数的集合。
第二个参数将所有的action创建函数组件传到了组件的同名属性（比如，increment被传递到props.increment）中，同时为每个action创建函数隐式绑定了
dispatch方法，因此可以直接通过props调用这些action创建函数，无须再使用dispatch来发起它们。

连接工作完成后，组件就可以从props中拿到state和acton创建函数了，而且一量state发生变化，被连接的组件也会自动重新渲染。。
