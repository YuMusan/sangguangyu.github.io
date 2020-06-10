## React Router
#### 基础组件 Basic Components
React Router 中有三种类型的组件：router components路由组件,route mathcing components路由匹配组件,navigaition components路由导航组件，在web应用程序中，所使用的路由组件都应该从react-router-dom中导入
```js
import { BrowserRouter as Router, 
HashRouter as Router,
Route,
Switch,
NavLink, 
Link ,
Redirect
} from "react-router-dom";
```
#### 路由
每个react router应用程序的核心应该是一个router组件，对于web项目，react-router-dom提供`BrowserRouter`和`HashRouter`路由，这两个路由会创建一个专门的history对象。如果有一个响应请求的服务器，则应该使用BrowserRouter,如果使用的是静态文件的服务器，则应该使用HashRouter
#### Route 匹配
有两个路由匹配组件Route和Switch
路由匹配是通过比较route的path属性和当前地址的pathname来实现的。当一个route匹配成功时，它将渲染内容，当它不匹配时就会渲染null,没有路径的route将始终被匹配。
```js
// when location = { pathname: '/about' }
<Route path='/about' component={About}/> // renders <About/>
<Route path='/contact' component={Contact}/> // renders null
<Route component={Always}/> // renders <Always/>
```
可以在任何希望根据地址渲染内容的地方添加Route，switch用于将route分组。一个switch会遍历其所有的子route元素，并仅渲染与当前地址匹配的第一个元素，这有助于多个路由的路径匹配相同的路径名，当动画在路由之间过度，且没有路由与当前地址匹配，此时可以渲染一个404组件
```js
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
  {/* when none of the above match, <NoMatch> will be rendered */}
  <Route component={NoMatch} />
</Switch>
```
#### 导航
react router提供了一个Link组件在应用程序中创建链接，无论在何处渲染一个Link，都会在应用程序的HTML中渲染锚(`<a>`)
NavLink是一种特殊类型的Link，当它的to属性与当前地址匹配是，可以将其定义为活跃的。当希望强制导航时，可以渲染一个redirect，当一个redirect渲染时，它将使用它的to属性进行定向。
```js
<Link to="/">Home</Link>
// <a href='/'>Home</a>
// location = { pathname: '/react' }
<NavLink to="/react" activeClassName="hurray">
  React
</NavLink>
// <a href='/react' className='hurray'>React</a>
<Redirect to="/login" from="/"/>
```
#### 路由渲染属性
有三个属性来个route渲染组件：component，render和children。component应该在想渲染现存组件是使用(class组件或者函数组件)，render只有在必须将范围内的变量传递给要渲染的组件时才能使用。不应该使用具有内联函数的 component 属性来传递范围内的变量，因为你将要卸载/重载组件，而这是没必要的。
```js
const Home = () => <div>Home</div>;

const App = () => {
  const someVariable = true;

  return (
    <Switch>
      {/* these are good */}
      <Route exact path="/" component={Home} />
      <Route
        path="/about"
        render={props => <About {...props} extra={someVariable} />}
      />
      {/* do not do this */}
      <Route
        path="/contact"
        component={props => <Contact {...props} extra={someVariable} />}
      />
    </Switch>
  );
};
```
## API 
### BrowerRouter 属性
```js
import { BrowserRouter } from 'react-router-dom'

<BrowserRouter
  basename={optionalString}
  forceRefresh={optionalBool}
  getUserConfirmation={optionalFunc}
  keyLength={optionalNumber}
>
  <App/>
</BrowserRouter>
```
basename:sting
所有地址的基本网址
```js
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // renders <a href="/calendar/today">
```
getUserConfirmation:function
用于确认导航的功能
forceRefresh：bool
如果为true，则路由器将在页面导航中使用整页刷新。
keyLength：number
location.key的长度，默认为6
### HashRouter
使用URL的hash部分即window.location.hash的Router使UI与URL保持同步。
**Hash历史记录不支持location.key或location.state**
basename:string
同BrowserRouter
getUserConfirmation:function
同BrowserRouter
##### hashType:string
用于window.location.hash的编码类型
+ slash" - 创建像 #/ 和的 #/sunshine/lollipops hash 表
+ "noslash" - 创建像 # 和的 #sunshine/lollipops hash 表
children:node,一个用于渲染的单一子元素
### Link
在应用程序周围提供声明式的可访问的导航
```js
import { Link } from 'react-router-dom'

<Link to="/about">About</Link>
```
to:sting
链接位置的字符串表示，通过链接位置的路径名，搜索和hash属性创建
```js
<Link to='/courses?sort=name'/>
```
to:object
具有以下任何属性的对象：
* pathname:表示要链接到的路径的字符串
* search：表示查询参数的字符串形式
* hash：放入网址的hash，例如#a-hash
* state：状态持续到location
```js
<Link to={pathname: '/courses',search: '?sort=name',hash: '#the-hash',state: { fromDashboard: true }} />
```
replace:bool
如果为true，则单击链接将替换历史堆栈中的当前入口而不是添加新入口
```js
<Link to="/courses" replace />
```
innerRef:function
允许访问ref组件的底层
```js
const refCallback = node => {
  // `node` refers to the mounted DOM element or null when unmounted
}

<Link to="/" innerRef={refCallback} />
```
others
其他可以放在`a`上的属性
### Navlink
特殊版本的Link，当它与当前URL匹配时，为其渲染元素添加样式属性
activeClassName：string
要给出的元素的类处于活动状态是，默认的给定类是active，它将与className属性一起使用
```js
<NavLink
  to="/faq"
  className="faqs"
  activeClassName="selected"
>FAQs</NavLink>
```
activeStyle:object
当元素处于active时应用于元素的样式
```js
<NavLink
  to="/faq"
  activeStyle={{
    fontWeight: 'bold',
    color: 'red'
   }}
>FAQs</NavLink>
```
exact:bool
如果为true，则仅在位置完全匹配时才应用active的类、样式
strict：bool
当情况为true，需要考虑位置是否匹配当前的URL时，把pathname尾部的斜线考虑在内。
```js
<NavLink
  strict
  to="/events/"
>Events</NavLink>
```
isActive:function
一个为了确定链接是否处于活动状态而添加额外逻辑的函数，如果不仅仅是想验证链接的路径名与当前的URL的pathname是否匹配，那么就可以使用
```js
/ only consider an event active if its event id is an odd number
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
>Event 123</NavLink>
```
location:object
isActive比较当前的历史location(通常是当前的浏览器URL)为了与不同的位置进行比较，可以传递一个location
### Redirect
渲染Redirect将使导航到一个新的地址，这个新的地址会覆盖history栈中的当前地址，类似服务器端的重定向
```js
import { Route, Redirect } from 'react-router'

<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard"/>
  ) : (
    <PublicHomePage/>
  )
)}/>
```
to:string
重定向到URL，可以使任何path-to-regexp能够理解的有效URL路径。在to中使用的URL参数必须由from覆盖
to:object
重定向到的location，pathname可以使任何path-to-regexp能够理解的有效的URL路径
```js
<Redirect
  to={
    pathname: "/login",
    search: "?utm=your+face",
    state: { referrer: currentLocation }
  }
/>
```
state对象可以通过重定向到组件中的this.props.locations.state来访问。这个新的referrer键将通过路径名为`/login`指向Login组件中的this.props.locations.state.referrer来访问
push：bool
当true时，重定向会将新地址推入history中，而不是替换当前地址
from:string
重定向from的路径名，可以使任何path-to-regexp能够识别的有效的URL路径，所有匹配的URL参数都提供给to中的模式，必须包含在to中使用的所有参数。这只能用于Redirect在Switch内部渲染匹配地址
```js
<Switch>
  <Redirect from="/old-path" to="/new-path" />
  <Route path="/new-path" component={Place} />
</Switch>
// Redirect with matched parameters
<Switch>
  <Redirect from="/users/:id" to="/users/profile/:id" />
  <Route path="/users/profile/:id" component={Profile} />
</Switch>
```
exact:bool
完全匹配from:相当于Route.exact
strict:bool
严格匹配from：相当于Route.strict
### Route
Route组件最基本的职责是在location与Route的path匹配时，呈现一些UI
route有三种渲染的方法
* Route component
* Route render
* Route children
三种渲染方法都有相同的三个Route属性
* match
* location
* history
##### component
只要当位置匹配时才会渲染的react组件
```js
<Route path="/user/:username" component={User}/>

const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```
当使用component，route会在给定组件中React.createElement创建新的React element。这意味着，如果为component组件提供了内联功能，则每次渲染发都会创建一个新组件。这会导致现有组件卸载和安装新组件，而不是仅更新现有组件。

##### render:func
可以传递一个都在位置匹配时调用的函数，而不是使用属性创建新的React element component，改render属性接收所有与route props相同的component 渲染属性
```js
/ convenient inline rendering
<Route path="/home" render={() => <div>Home</div>}/>

// wrapping/composing
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props}/>
    </FadeIn>
  )}/>
)

<FadingRoute path="/cool" component={Something}/>
```
警告： <Route component> 优先于 <Route render> 因此不要在同一个 <Route> 使用两者。
##### children:func
有时需要渲染路径是否匹配位置。在这些情况下，使用函数 children 属性，它的工作原理与渲染完全一样，不同之处在于它是否存在匹配。

children 渲染道具接收所有相同的 route props 作为 component 和 render 方法，如果 Route 与 URL 不匹配，match 则为 null ，这允许动态调整UI界面，基于路线是否匹配，如果路线匹配我们则添加一个 active 类
```js
<ul>
  <ListItemLink to="/somewhere"/>
  <ListItemLink to="/somewhere-else"/>
</ul>

const ListItemLink = ({ to, ...rest }) => (
  <Route path={to} children={({ match }) => (
    <li className={match ? 'active' : ''}>
      <Link to={to} {...rest}/>
    </li>
  )}/>
)

<Route children={({ match, ...rest }) => (
  {/* Animate will always render, so you can use lifecycles
      to animate its child in and out */}
  <Animate>
    {match && <Something {...rest}/>}
  </Animate>
)}/>
```
警告：<Route component> 和 <Route render> 优先于 <Route children> ，因此不要在同一个 <Route> 中使用多个。
##### path:string
任何path-to-regexp可以解析的有效URL路径
`<Route path="/users/:id" component={User}/>`
##### exact:bool
如果为true，则只有在路径完全匹配location pathname时才匹配
`<Route exact path="/one" component={About}/>`
##### strict:bool
如果为true，当真是的路径具有一个斜线将只匹配一个斜线location.pathname，如果有更多的URL段location.pathname，将不起作用
`<Route strict path="/one/" component={About}/>`
path	location.pathname	matches?
/one/	   /one              no
/one/	   /one/    	       yes
/one/	   /one/two	y        es
**警告：**可以使用 strict 来强制执行 location.pathname 没有结尾斜杠，但为了执行此操作，strict 和 exact 必须都是 true 。
##### sensitive:bool
如果路径区分大小写，则为true，则匹配
`<Route sensitive path="/one" component={About}/>`
##### location: object
一个 <Route> 元素尝试其匹配 path 到当前的历史位置（通常是当前浏览器 URL ）。但是，也可以通过location 一个不同 pathname 的匹配。
### history
本文档中的 “history” 以及 “history对象”指的是 history 包中的内容，该包是 React Router 仅有的两大主要依赖之一（除去 React 本身），在不同的 Javascript 环境中，它提供多种不同的形式来实现对 session 历史的管理。
会使用以下术语：
* “browser history” - 在特定 DOM 上的实现，使用于支持 HTML5 history API 的 web 浏览器中
* “hash history” - 在特定 DOM 上的实现，使用于旧版本的 web 浏览器中
* “memory history” - 在内存中的 history 实现，使用于测试或者非 DOM 环境中，例如 React Native

history 对象通常会具有以下属性和方法：
* length - (number 类型) history 堆栈的条目数
* action - (string 类型) 当前的操作(PUSH, REPLACE, POP)
* location - (object 类型) 当前的位置。location 会具有以下属性：
* pathname - (string 类型) URL 路径
* search - (string 类型) URL 中的查询字符串
* hash - (string 类型) URL 的哈希片段
* state - (object 类型) 提供给例如使用 push(path, state) 操作将 location 放入堆栈时的特定 location 状态。只在浏览器和内存历史中可用。
* push(path, [state]) - (function 类型) 在 history 堆栈添加一个新条目
* replace(path, [state]) - (function 类型) 替换在 history 堆栈中的当前条目
* go(n) - (function 类型) 将 history 堆栈中的指针调整 n
* goBack() - (function 类型) 等同于 go(-1)
* goForward() - (function 类型) 等同于 go(1)
* block(prompt) - (function 类型) 阻止跳转。
```js
class Comp extends React.Component {
  componentWillReceiveProps(nextProps) {
    // locationChanged 将为 true
    const locationChanged = nextProps.location !== this.props.location

    // INCORRECT，因为 history 是可变的所以 locationChanged 将一直为 false
    const locationChanged = nextProps.history.location !== this.props.history.location
  }
}

<Route component={Comp}/>
```
### location
location 代表应用程序现在在哪，想让它去哪，或者甚至它曾经在哪，它看起来就像：
```js
{
  key: 'ac3df4', // not with HashHistory!
  pathname: '/somewhere'
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
```
router 将在这几个地方提供一个 location 对象：
* Route component as this.props.location
* Route render as ({ location }) => ()
* Route children as ({ location }) => ()
* withRouter as this.props.location
location 对象永远不会发生变化，因此可以在生命周期钩子中使用它来确定何时导航，这对数据抓取和动画非常有用。
```js
componentWillReceiveProps(nextProps) {
  if (nextProps.location !== this.props.location) {
    // navigated!
  }
}
```
可以将 location 而不是字符串提供给导航的各种位置：
* Web Link to
* Native Link to
* Redirect to
* history.push
* history.replace
通常导航路径只是使用一个字符串，但是如果需要添加一些 “location state”，而且应用程序返回到特定的地址可以使用，这时使用 location 对象。 如果根据导航历史记录而不是仅路径（比如模式）来分支UI，这很有用。
```js
// usually all you need
<Link to="/somewhere"/>

// but you can use a location instead
const location = {
  pathname: '/somewhere',
  state: { fromDashboard: true }
}

<Link to={location}/>
<Redirect to={location}/>
history.push(location)
history.replace(location)
```
最后location可以传递给以下组件：
* Route
* Switch
这将阻止它们在 router 的状态下使用实际的 location。对动画和在等待的导航非常有用，或者任何时候想哄骗一个组件在不同的 location 渲染到真实的位置。
### match
一个 match 对象中包涵了有关如何匹配 URL 的信息。match 对象中包涵以下属性：
* params - (object) key／value 与动态路径的 URL 对应解析
* isExact - (boolean) true 如果匹配整个 URL （没有结尾字符）
* path - (string) 用于匹配的路径模式。被嵌套在 <Route> 中使用
* url - (string) 用于匹配部分的 URL 。被嵌套在 <Link> 中使用

在这些地方可以用到 match 对象：
* Route component as this.props.match
* Route render as ({ match }) => ()
* Route children as ({ match }) => ()
* withRouter as this.props.match
* matchPath as 返回值

###　matchPath
除正常渲染周期外，可以使用与 <Route> 相同的匹配代码，例如，在服务端渲染之前收集数据依赖。
```js
import { matchPath } from 'react-router'

const match = matchPath('/users/123', {
  path: '/users/:id',
  exact: true,
  strict: false
})
```
pathname
第一个参数是想要匹配的pathname。如果在服务器上使用 Node.js,它将是req.path。
props
第二个参数是匹配的 props，它们与 Route 接受的 props 相同：
```js
{
  path, // 例如 /users/:id
  strict, // 选填, 默认为 false
  exact // 选填, 默认为 false
}
```
### withRouter
可以通过withRouter高阶组件访问history对象的属性和最近的 <Route> 的match。 当路由渲染时，withRouter会将已经更新 match,location和history属性传递给被包裹的组件。
```js
import React from 'react'
import PropTypes from 'prop-types'
import { withRouter } from 'react-router'

// 显示当前位置的路径名的简单组件
class ShowTheLocation extends React.Component {
  static propTypes = {
    match: PropTypes.object.isRequired,
    location: PropTypes.object.isRequired,
    history: PropTypes.object.isRequired
  }

  render() {
    const { match, location, history } = this.props

    return (
      <div>You are now at {location.pathname}</div>
    )
  }
}

// 创建一个“connected”的新组件（借用Redux 术语）到路由器。

const ShowTheLocationWithRouter = withRouter(ShowTheLocation)
```
如果正在使用 withRouter 来防止由 shouldComponentUpdate 阻塞的更新，那么用 withRouter 来包裹执行 shouldComponentUpdate 的组件是很重要的
```js
// This gets around shouldComponentUpdate
withRouter(connect(...)(MyComponent))
// or
compose(
  withRouter,
  connect(...)
)(MyComponent)
```
### 使用示例
##### basic
```js
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const BasicExample = () => (
  <Router>
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/topics">Topics</Link>
        </li>
      </ul>

      <hr />

      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/topics" component={Topics} />
    </div>
  </Router>
);

const Home = () => (
  <div>
    <h2>Home</h2>
  </div>
);

const About = () => (
  <div>
    <h2>About</h2>
  </div>
);

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>Rendering with React</Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>Components</Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
      </li>
    </ul>

    <Route path={`${match.url}/:topicId`} component={Topic} />
    <Route
      exact
      path={match.url}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div>
);

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
);

export default BasicExample;
```
