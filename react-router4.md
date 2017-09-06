## 前导

```
import React from 'react'
import {BrowserRouter as Router,Route,Link} from 'react-router-dom'

const Basic = () => (
  <Router>
    <div>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/page1">Page1</Link></li>
        <li><Link to="/page2">Page2</Link></li>
      </ul>

      <hr/>

      <Route exact path="/" component={Home}/>
      <Route path="/page1" component={Page1}/>
      <Route path="/page2" component={Page2}/>
    </div>
  </Router>
)

```

跟之前的版本一样，Router这个组件还是一个容器，但是它的角色变了，4.0的Router下面可以放任意标签了，这意味着使用方式的转变，它更像redux中的provider了。跟之前的版本一样，Router这个组件还是一个容器，但是它的角色变了，4.0的Router下面可以放任意标签了，这意味着使用方式的转变，它更像redux中的provider了。
