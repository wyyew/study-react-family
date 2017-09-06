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

> 通过Route路由的组件，可以拿到一个match参数，这个参数是一个对象，其中包含几个数据：

- isExact：刚才已经说过这个关键字，表示是为作全等匹配

- params:path中包含的一些额外数据

- path:Route组件path属性的值

- url:实际url的hash值

我们来实现一下刚才的Page2组件：

```

const Page2 = ({ match }) => (
  <div>
    <h2>Page2</h2>
    <ul>
      <li>
        <Link to={`${match.url}/branch1`}>
          branch1
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/branch2`}>
          branch2
        </Link>
      </li>
      <li>
        <Link to={`${match.url}/branch3`}>
          branch3
        </Link>
      </li>
    </ul>

    <Route path={`${match.url}/:branchId`} component={Branch} />
    <Route exact path={match.url} render={() => (
      <h3>Default Information</h3>
    )} />
  </div>
)

const Branch = ({ match }) => {
  console.log(match);
  return (
    <div>
      <h3>{match.params.branchId}</h3>
    </div>
  )
}

```

> 需要注意的就只有Route的path中冒号":"后的部分相当于通配符，而匹配到的url将会把匹配的部分作为match.param中的属性传递给组件，属性名就是冒号后的字符串。

## Router标签

到了4.0版本，在react-router-dom中直接将这三种history作了内置，于是我们看到了BrowserRouter、HashRouter、MemoryRouter这三种Router，当然，你依然可以使用React-router中的Router，然后自己通过createHistory来创建history来传入。

react-router的history库依然使用的是 https://github.com/ReactTraining/history

在例子中你可能注意到了Route的几个prop

- exact: propType.bool

- path: propType.string

- component: propType.func

- render: propType.func

他们都不是必填项，注意，如果path没有赋值，那么此Route就是默认渲染的。
Route的作用就是当url和Route中path属性的值匹配时，就渲染component中的组件或者render中的内容。

当然，Route其实还有几个属性，比如location，strict,chilren 希望你们自己去了解一下。

说到这，那么Route的内部是怎样实现这个机制的呢？不难猜测肯定是用一个匹配的方法来实现的，那么Route是怎么知道url更新了然后进行重新匹配并渲染的呢？

整理一下思路，在一个web 应用中，改变url无非是2种方式，一种是利用超链接进行跳转，另一种是使用浏览器的前进和回退功能。前者的在触发Link的跳转事件之后触发，而后者呢？Route利用的是我们上面说到过的history的listen方法来监听url的变化。为了防止引入新的库，Route的创作者选择了使用html5中的popState事件，只要点击了浏览器的前进或者后退按钮，这个事件就会触发，我们来看一下Route的代码：

```
class Route extends Component {
  static propTypes: {
    path: PropTypes.string,
    exact: PropTypes.bool,
    component: PropTypes.func,
    render: PropTypes.func,
  }

  componentWillMount() {
    addEventListener("popstate", this.handlePop)
  }

  componentWillUnmount() {
    removeEventListener("popstate", this.handlePop)
  }

  handlePop = () => {
    this.forceUpdate()
  }

  render() {
    const {
      path,
      exact,
      component,
      render,
    } = this.props

    //location是一个全局变量
    const match = matchPath(location.pathname, { path, exact })

    return (
      //有趣的是从这里我们可以看出各属性渲染的优先级，component第一
      component ? (
        match ? React.createElement(component, props) : null
      ) : render ? ( // render prop is next, only called if there's a match
        match ? render(props) : null
      ) : children ? ( // children come last, always called
        typeof children === 'function' ? (
          children(props)
        ) : !Array.isArray(children) || children.length ? ( // Preact defaults to empty children array
          React.Children.only(children)
        ) : (
              null
            )
      ) : (
              null
            )
    )
  }
}

```

Route在组件将要Mount的时候添加popState事件的监听，每当popState事件触发，就使用forceUpdate强制刷新，从而基于当前的location.pathname进行一次匹配，再根据结果渲染。

PS：现在最新的代码中，Route源码其实是通过componentWillReceiveProps中setState来实现重新渲染的，match属性是作为Route组件的state存在的.

那么这个关键的matchPath方法是怎么实现的呢？
Route引入了一个外部library：path-to-regexp。这个pathToRegexp方法用于返回一个满足要求的正则表达式，举个例子：

```

let keys = [],keys2=[]
let re = pathToRegexp('/foo/:bar', keys)
//re = /^\/foo\/([^\/]+?)\/?$/i  keys = [{ name: 'bar', prefix: '/', delimiter: '/', optional: false, repeat: false, pattern: '[^\\/]+?' }]   

let re2 = pathToRegexp('/foo/bar', keys2)
//re2 = /^\/foo\/bar(?:\/(?=$))?$/i  keys2 = []

```

关于它的详细信息你可以看这里:https://github.com/pillarjs/path-to-regexp

值得一提的是matchPath方法中对匹配结果作了缓存，如果是已经匹配过的字符串，就不用再进行一次pathToRegexp了。

随后的代码就清晰了：

```
const match = re.exec(pathname)

if (!match)
  return null

const [ url, ...values ] = match
const isExact = pathname === url

//如果exact为true，需要pathname===url
if (exact && !isExact)
  return null

return {
  path, 
  url: path === '/' && url === '' ? '/' : url, 
  isExact, 
  params: keys.reduce((memo, key, index) => {
    memo[key.name] = values[index]
    return memo
  }, {})
}

```

## Link

还记得上面说到的改变url的两种方式吗，我们来说说另一种，Link，看一下它的参数：

```

static propTypes = {
    onClick: PropTypes.func,
    target: PropTypes.string,
    replace: PropTypes.bool,
    to: PropTypes.oneOfType([
      PropTypes.string,
      PropTypes.object
    ]).isRequired
}

```

onClick就不说了，target属性就是a标签的target属性，to相当于href。
而replace的意思跳转的链接是否覆盖history中当前的url，若为true,新的url将会覆盖history中的当前值，而不是向其中添加一个新的。

```

handleClick = (event) => {
  if (this.props.onClick)
    this.props.onClick(event)

  if (
    !event.defaultPrevented && // 是否阻止了默认事件
    event.button === 0 && // 确定是鼠标左键点击
    !this.props.target && // 避免打开新窗口的情况
    !isModifiedEvent(event) // 无视特殊的key值，是否同时按下了ctrl、shift、alt、meta
  ) {
    event.preventDefault()

    const { history } = this.context.router
    const { replace, to } = this.props

    if (replace) {
      history.replace(to)
    } else {
      history.push(to)
    }
  }
}

```

需要注意的是，history.push和history.replace使用的是pushState方法和replaceState方法。

## Redirect

```

class Redirect extends React.Component {
  //...省略一部分代码

  isStatic() {
    return this.context.router && this.context.router.staticContext
  }

  componentWillMount() {
    if (this.isStatic())
      this.perform()
  }

  componentDidMount() {
    if (!this.isStatic())
      this.perform()
  }

  perform() {
    const { history } = this.context.router
    const { push, to } = this.props

    if (push) {
      history.push(to)
    } else {
      history.replace(to)
    }
  }

  render() {
    return null
  }
}

```

很容易注意到这个组件并没有UI，render方法return了一个null。很容易产生这样一个疑问，既然没有UI为什么react-router的创造者依然选择将Redirect写成一个组件呢？

我想我们可以从作者口中的"Just Components API"中窥得原因吧。
