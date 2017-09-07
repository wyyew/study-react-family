## 前导

> 它遵循react的设计理念，即万物皆组件。所以 RR4 只是一堆 提供了导航功能的组件（还有若干对象和方法），具有声明式（引入即用），可组合性的特点.

RR4 本次采用单代码仓库模型架构（monorepo），这意味者这个仓库里面有若干相互独立的包，分别是：

- react-router React Router 核心

- react-router-dom 用于 DOM 绑定的 React Router

- react-router-native 用于 React Native 的 React Router

- react-router-redux React Router 和 Redux 的集成

- react-router-config 静态路由配置的小助手

react-router 和react-router-dom，他们两个只要引用一个就行了，不同之处就是后者比前者多出了 <Link> <BrowserRouter> 这样的 DOM 类组件。
  
如果搭配 redux ，你还需要使用 react-router-redux。

## 组件

### BrowserRouter

一个使用了 HTML5 history API 的高阶路由组件，保证你的 UI 界面和 URL 保持同步。此组件拥有以下属性：

- basename: string

作用：为所有位置添加一个基准URL

使用场景：假如你需要把页面部署到服务器的二级目录，你可以使用 basename 设置到此目录。

```
<BrowserRouter basename="/minooo" />
<Link to="/react" /> // 最终渲染为 <a href="/minooo/react">
```
- getUserConfirmation: func

作用：导航到此页面前执行的函数，默认使用 window.confirm

使用场景：当需要用户进入页面前执行什么操作时可用，不过一般用到的不多。

```
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message)
  callback(allowTransition)
}

<BrowserRouter getUserConfirmation={getConfirmation('Are you sure?', yourCallBack)} />
```
- forceRefresh: bool

作用：当浏览器不支持 html5 的 history API 时强制刷新页面。

使用场景：同上。

```
const supportsHistory = 'pushState' in window.history
<BrowserRouter forceRefresh={!supportsHistory} />
```

- keyLength: number

作用：设置它里面路由的 location.key 的长度。默认是6。（key的作用：点击同一个链接时，每次该路由下的 location.key都会改变，可以通过 key 的变化来刷新页面。）

使用场景：按需设置。

```
<BrowserRouter keyLength={12} />
```

- children: node

作用：渲染唯一子元素。

使用场景：作为一个 Reac t组件，天生自带 children 属性。


### Route

<Route> 也许是 RR4 中最重要的组件了，它最基本的职责就是当页面的访问地址与 Route 上的 path 匹配时，就渲染出对应的 UI 界面。

<Route> 自带三个 render method 和三个 props 。

render methods 分别是：

- Route component
  
 > component

 >> 只有当访问地址和路由匹配时，一个 React component 才会被渲染，此时此组件接受 route props (match, location, history)。
当使用 component 时，router 将使用 React.createElement 根据给定的 component 创建一个新的 React 元素。这意味着如果你使用内联函数（inline function）传值给 component将会产生不必要的重复装载。对于内联渲染（inline rendering）, 建议使用 renderprop。

```
<Route path="/user/:username" component={User} />
const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```
  
- Route render
  
  > render: func

 >> 此方法适用于内联渲染，而且不会产生上文说的重复装载问题。

```
// 内联渲染
<Route path="/home" render={() => <h1>Home</h1} />

// 包装 组合
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props} />
    </FaseIn>
  )} />
)

<FadingRoute path="/cool" component={Something} />
```

- Route children
  
  > children: func
  
  >> 有时候你可能只想知道访问地址是否被匹配，然后改变下别的东西，而不仅仅是对应的页面。

```
<ul>
  <ListItemLink to="/somewhere" />
  <ListItemLink to="/somewhere-ele" />
</ul>

const ListItemLink = ({ to, ...rest }) => (
  <Route path={to} children={({ match }) => (
    <li className={match ? 'active' : ''}>
      <Link to={to} {...rest} />
    </li>
  )}
)
```

- path: string

任何可以被 path-to-regexp解析的有效 URL 路径

```
<Route path="/users/:id" component={User} />
```

如果不给path，那么路由将总是匹配。

- exact: bool

如果为 true，path 为 '/one' 的路由将不能匹配 '/one/two'，反之，亦然。

- strict: bool

对路径末尾斜杠的匹配。如果为 true。path 为 '/one/' 将不能匹配 '/one' 但可以匹配 '/one/two'。

如果要确保路由没有末尾斜杠，那么 strict 和 exact 都必须同时为 true


每种 render method 都有不同的应用场景，同一个<Route> 应该只使用一种 render method ，大部分情况下你将使用 component .
  
props 分别是：

- match

- location

- history

所有的 render method 无一例外都将被传入这些 props。


### Link

为你的应用提供声明式，无障碍导航。

- to: string

作用：跳转到指定路径
使用场景：如果只是单纯的跳转就直接用字符串形式的路径。

```
<Link to="/courses" />
```

- to: object

作用：携带参数跳转到指定路径
作用场景：比如你点击的这个链接将要跳转的页面需要展示此链接对应的内容，又比如这是个支付跳转，需要把商品的价格等信息传递过去。

```
<Link to={{
  pathname: '/course',
  search: '?sort=name',
  state: { price: 18 }
}} />
```

- replace: bool

为 true 时，点击链接后将使用新地址替换掉上一次访问的地址，什么意思呢，比如：你依次访问 '/one' '/two' '/three' ’/four' 这四个地址，如果回退，将依次回退至 '/three' '/two' '/one' ，这符合我们的预期，假如我们把链接 '/three' 中的 replace 设为 true 时。依次点击 one two three four 然后再回退会发生什么呢？会依次退至 '/three' '/one'！


#### NavLink

> 这是 <Link> 的特殊版，顾名思义这就是为页面导航准备的。因为导航需要有 “激活状态”。

- activeClassName: string

导航选中激活时候应用的样式名，默认样式名为 active

```
<NavLink
  to="/about"
  activeClassName="selected"
>MyBlog</NavLink>
activeStyle: object
```
如果不想使用样式名就直接写style

```
<NavLink
  to="/about"
  activeStyle={{ color: 'green', fontWeight: 'bold' }}
>MyBlog</NavLink>
```
- exact: bool

若为 true，只有当访问地址严格匹配时激活样式才会应用


- strict: bool

若为 true，只有当访问地址后缀斜杠严格匹配（有或无）时激活样式才会应用


- isActive: func

决定导航是否激活，或者在导航激活时候做点别的事情。不管怎样，它不能决定对应页面是否可以渲染。

### Switch

只渲染出第一个与当前访问地址匹配的 <Route> 或 <Redirect>。

思考如下代码，如果你访问 /about，那么组件 About User Nomatch 都将被渲染出来，因为他们对应的路由与访问的地址 /about 匹配。这显然不是我们想要的，我们只想渲染出第一个匹配的路由就可以了，于是 <Switch> 应运而生！

```
<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```

也许你会问，为什么 RR4 机制里不默认匹配第一个符合要求的呢，答：这种设计允许我们将多个 <Route> 组合到应用程序中，例如侧边栏（sidebars），面包屑 等等。

另外，<Switch> 对于转场动画也非常适用，因为被渲染的路由和前一个被渲染的路由处于同一个节点位置！
  
  ```
  <Fade>
  <Switch>
    {/* 用了Switch 这里每次只匹配一个路由，所有只有一个节点。 */}
    <Route/>
    <Route/>
  </Switch>
</Fade>

<Fade>
  <Route/>
  <Route/>
  {/* 不用 Switch 这里可能就会匹配多个路由了，即便匹配不到，也会返回一个null，使动画计算增加了一些麻烦。 */}
</Fade>
```
- children: node

Switch 下的子节点只能是 Route 或 Redirect 元素。只有与当前访问地址匹配的第一个子节点才会被渲染。Route 元素用它们的 path 属性匹配，Redirect 元素使用它们的 from 属性匹配。如果没有对应的 path 或 from，那么它们将匹配任何当前访问地址。
  
  

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

跟之前的版本一样，Router这个组件还是一个容器，但是它的角色变了，4.0的Router下面可以放任意标签了，这意味着使用方式的转变，它更像redux中的provider了。

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
