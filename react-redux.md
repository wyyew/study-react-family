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
