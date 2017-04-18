## 三大核心概念

- Provider

- connect 方法

- 展示组件和容器组件

### Provider

provider 只是一个React组件，它的职能是通过context将store传递给子组件。因为Provider组件是通过context传递store的，所以里面的组件，不管卡跨多少级，都可以通过connect()方法获取store并进行连接。下面是它的原码：

```
export default class Provider extends Component {
  getChildContext() {
  return {store: this.store}
  }
  ...
}
...

Provider.propTypes = {
  store:storeShape.isRequired;
  ...
}
Provider.childContextTypes = {
  store:storeShape.isRequired
}

```

可以看出Provider只是一个使用context传递数据的React组件而已。

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

1. connect第一种写法

```
//例：connect
//
import Counter from '../components/Counter';
import { connect } from 'react-redux';
import * as ActionCreatores from '../actions';

export default connect(
  state => ({ counter: state.counter }),
  ActionCreators
)(Counter)
```

connect 第一个参数是一个函数， 它将state.counter传递给组件的counter属性。connect()的第二个是个对象，这个对象是所有action创建函数的集合。第二个参数将所有的action创建函数组件传到了组件的同名属性（比如，increment被传递到props.increment）中，同时为每个action创建函数隐式绑定了dispatch方法，因此可以直接通过props调用这些action创建函数，无须再使用dispatch来发起它们。

连接工作完成后，组件就可以从props中拿到state和acton创建函数了，而且一量state发生变化，被连接的组件也会自动重新渲染。

2. connect第二种写法

```
import Counter from '../components/Counter';
import { connect } from 'react-redux';
import { increment, decrement, incrementIfOdd, incremntAsync } from '../actions';

export default connect(
  state => ({counter: state.counter}),
  dispatch => ({
    increment:() => dispatch(increment()),
    decrement:() => dispatch(decrement()),
    incrementIfOdd:() => dispatch(incrementIfOdd()),
    incremntAsync:() => dispatch(incremntAsync())
  })
)（Counter);
```

第一个参数不变，connect() 第二个参数是参数为dispatch的函数，该函数返回的对象也会被合并到组件的props中。注意，该写法不会自动为action创建函数绑定dispatch方法，所以需要我们手动来绑定。

3. connect第三种写法

```
import Counter from '../components/Counter';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import * as ActionCreatores from '../actions';

export default connect(
state => ({counter: state.counter})),
dispatch => bindActionCreatores(ActionCreators, dispatch)
)(Counter);
```

connect的第一个参数不变。connect和第二个参数是参数为dispatch的函数，但是函数的返回值使用了redux的bindActionCreators()来减少样板代码。

4. connect第四种写法

```
import React from 'react';
import PropTypes from 'prop-types';
import { connect } from 'react-redux';
import { increment, decrement, incrementIfOdd, incremntAsync } from '../actions';

function Counter ({ counter, dispatch}) {
  return (
    <p>
      Clicked: {counter} times
      {' '}
      <button onClick={() => dispatch(increment)}>+</button>
      {' '}
      <button onClick={() => dispatch(decrement}>-</button>
      {' '}
      <button onClick={() => dispatch(incrementIfOdd}>Increment if odd</button>
      {' '}
      <button onClick={() => dispatch(incrementAsync())}>increment async</button>
    </p>
  )
}

Counter.propTypes = {
  counter:PropTypes.number.isRequired,
  dispatch: PropTypes.func.isRequired
};

export default connect(
  state => ({ counter: state.counter })
)(Counter)

```

第一个参数不变。connect的第二个参数为空。些时connect() 将会自动给组件传递一个dispatch, 让组件自己使用dispatch来发起action创建函数。

5. 第五种connect的写法

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import * as ActionCreatores from '../actions';

//装饰器应该写在类声明上面

@connect(
  state => ({ counter: state.counter }),
  ActionCreatores
)

class Counter extends Component {
  static propTypes = {
    counter:PropTypes.number.isRequired,
    increment: PropTypes.func.isRequired,
    incrementAsync:PropTypes.func.isRequired,
    incrementIfOdd: PropTypes.func.isRequired,
    decrement:PropTypes.func.isRequired
  };
  
  render() {
    const { counter, increment, decrement, incrementIfOdd, incrementAsync } = this.props;
    
    return (
      <p>
      Clicked: {counter} times
      {' '}
      <button onClick={increment}>+</button>
      {' '}
      <button onClick={decrement}>-</button>
      {' '}
      <button onClick={incrementIfOdd}>Increment if odd</button>
      {' '}
      <button onClick={() => incrementAsync()}>increment async</button>
    </p>
    )
  }
}
```

使用装饰器写法将connect()写在组件类声明上面。装饰器写法和前面的写法只是语法上的区别，其他并无二致。但是，如果使用装饰器写法，就不能使用无状态函数来编写组件了，因为装饰器需要加在类声明上。另外，还必须使用static propTypes= {...} 这种静态属性的写法来声明props类型。

### connect原理

connect 是一个嵌套函数。运行connect()后，会生成一个高阶组件。该高阶组件接受一个组件作为参数再次运行，会生成一个新组件。

高阶组件新生成的组件叫connect组件。connect组件从context中获取了来自Provider的store，然后从store中获取state和dispatch，最后将state和经过dispatch加工的action创建函数连接到组件上，并在state变化时重新渲染组件。

> 注意： 假如一个页面中有多个被connect()连接的组件， 这些组件只会在自己对应的state变化时重新渲染，不会相互干扰。因为react-redux在重新渲染组件前会检测传入组件的数据是否变化，如果没有变化，就不会执行渲染。如此一来，我们就不用担心页面中某一个组件数据的变化，会“连累”其他组件重新渲染了，只需要将不同的数据用不同的connect()隔离开即可。

### 展示组件与容器组件

如果你的页面中包含多个组件，是否应该将每个组件都连接到Redux中呢？当然不是。Redux通常只与靠近顶层的组件进行连接，这些组件再将state和action创建函数通过pros传给里面的组件。为了区分这两种组件，我们将与redux连接的组件称为容器组件（container）,将里面的组件称为展示组件（presentational component）.

|           |  展示组件 | 容器组件  |
| :-------- | --------:| :--: |
| 功能  | 决定程序如何显示 |  决定程序如何运行（数据获取、状态更新） |
| 是否连接redux|  否 |  是  |
| 读取数据|从props中读取数据| 订阅 redux的state |
|更新数据|调用来自props的回调函数|发起redux的action|
|生成组件|手动编写|大多通过react redux自动生成|

虽然容器组件与展示组件的约定是react与redux开发中的最佳实践，但是这个约定并非需要死板的遵循。事实上， containers目录中也可以放置没有连接redux的组件，因为有些页面就是不需要连接redux的，但它仍然是其他组件的“容器”。而components目录中也可以放置连接redux的组件。但在绝大多多情况下，我们都应该将redux连接在组件顶层，不要让里面的组件感受到redux的存在。

