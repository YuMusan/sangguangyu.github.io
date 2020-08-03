# Vue Router
## 介绍
Vue Router是Vue.js官方的路由管理器，它和Vue.js的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：
* 嵌套的路由、视图表
* 模块化的、基于组件的路由配置
* 路由参数、查询、通配符
* 基于Vue.js过渡系统的视图过渡效果
* 细粒度的导航控制
* 带有自动激活的CSS calss的链接
* HTML历史模式或hash模式
* 自定义的滚动条行为

## 起步
用Vue.js + Vue Router创建单页应用，是非常简单的，使用Vue.js，可以通过组合组件来组成应用程序，当要把Vue Router添加进来，需要将组件映射到路由，然后告诉Vue Router在哪里渲染它们。基本例子：
```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```
```js
// 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

// 1. 定义 (路由) 组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')

// 现在，应用已经启动了！
```
通过注入路由器，可以在任何组件内通过`this.$router`访问路由器，也可以通过`this.$route`访问当前路由：
```js
// Home.vue
export default {
  computed: {
    username() {
      // 我们很快就会看到 `params` 是什么
      return this.$route.params.username
    }
  },
  methods: {
    goBack() {
      window.history.length > 1 ? this.$router.go(-1) : this.$router.push('/')
    }
  }
}
```
文档通篇都使用router实例，this.$router和router使用起来完全一样，使用this.$router的原因是并不想在每个独立需要封装路由的组件中都导入路由。
## 动态路由匹配
经常需要把某种模式匹配到所有路由，全都映射到同个组件。有一个user组件，对于所有ID各不相同的用户，都要使用这个组件来渲染，那么可以在vue-router的路由路径中使用动态路径参数dynamic segment来达到这个效果。
```js
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```
现在像/user/foo和/user/bar都将映射到相同的路由
一个"路径参数"使用：标记，当匹配到一个路由时，参数值会被设置到`this.$route.params`,可以在每个组件内使用，于是，可以更新user的模板，输出当前用户的ID：
`const User = {template: '<div>User {{ $route.params.id }}</div>'}`

可以在一个路由中设置多段"路径参数",对应的值都会设置到`$route.params`中。
/user/:username/post/:post_id | { username: 'evan', post_id: '123' }
除了$route.params外，$route对象还提供了其它有用的信息，例如$route.query、$route.hash等等。
## 响应路由参数的变化
当使用路由参数时，例如从/user/foo导航到/user/bar，原来的组件实例会被复用。因为
两个路由都渲染同个组件，比起销毁在创建，复用显得更加高效，不过，这也意味着组件的生命周期钩子不会再被调用。
复用组件时，路由参数的变化作出响应的话，可以简单地watch $route对象：
```js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```
或者使用beforeRouteUpdate导航守卫
```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```
捕获所有路由或404 Not found路由
常规参数只会匹配被/分隔的URL片段中的字符。如果匹配任意路径，可以使用通配符 *
```js
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}
```
当使用通配符路由时，请确保路由的顺序是正确的，也就是说含有通配符的路由应该放在最后。路由`{path: '*'}`通常用于客户端404错误，如果使用history模式，确保正确配置服务器。
当使用一个通配符是，`$route/params`内会自动添加一个名为`pathMatch`参数。它包含了URL通过通配符被匹配的部分：
```js
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```
### 匹配优先级
有时候，同一个路径可以匹配到多个路由，此时匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。
## 嵌套路
实际项目中的应用界面，通常由多层嵌套的组件组合而成，同样地，URL中各段动态路径也按某种结构对应嵌套的各层组件。
```text
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```
借助vue-router，使用嵌套路由配置，可以很简单地表达这种关系。
接着上节创建的app：
```html
<div id="app">
  <router-view></router-view>
</div>
```
```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```

这里的`<router-view>`是最顶层的出口，渲染最高级路由匹配到的组件。同样地，一个被渲染组件同样可以包含自己的嵌套`<router-view>`。例如，在user组件的模板添加一个`<router-view>`:

```js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

要在嵌套的出口中渲染组件，需要在`VueRouter`的参数中使用`children`配置：

```js
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

要注意，以`/`开头的嵌套路径会被当作根路径。这可以充分的使用嵌套组件而无须设置嵌套的路径。

`children`配置就是像`routes`配置一样的路由配置数组，所以呢，可以嵌套多层路由。此时，基于上面的配置，当访问`/user/foo`时，`user`的 出口是不会渲染任何东西，因为没有匹配到合适的子路由。如果想要渲染点什么，可以提供一个空的子路由：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },

        // ...其他子路由
      ]
    }
  ]
})
```

## 编程式的导航

除了使用`<router-link>`创建a标签来定义导航链接，还可以借助router的实例方法，通过编写代码来实现。

`router.push(location, onComplete?, onAbort)`

**注意：在Vue实例内部，可以通过`$router`访问路由实例。因此可以调用`this.$router.push`**。

想要导航到不同的URL，则使用`router.push`方法，这个方法会向history栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的URL。

当点击`<router-link>`时，这个方法会在内部调用，所以，点击`<router-link :to='...'>`等同于调用`router.push(...)`

声明式：`<router-link :to='...'>`，编程式：`router.push(...)`

该方法的参数可以是一个字符串路径，或者一个描述地址的对象。例如：

```js
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

**注意：如果提供了`path`,`parms`会被忽略，上述例子中的`query`并不属于这种情况。取而代之的是下面例子的做法。需要提供路由的`name`或手写完整的带有参数的`path`**:

```js
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

同样的规则也使用与`router-link`组件的to属性。

可选的在`router.push`或`router.replace`中提供`onComplete`和`onAbort`回调作为第二个和第三个参数。这些回调将会在导航成功完成(在所有的异步钩子被解析之后)或终止(导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由)的时候进行相应的调用。可以省略第二个和第三个参数，此时如果支持promise，`router.push`或`router.replace`将返回一个promise

注意：如果目的地和当前路由相同，只有参数发生了改变(比如从一个用户资料到另一个`/users/1` -> `/user/2`)，需要使用`beforeRouteUpdate`来响应这个变化(比如抓取用户信息)。

`router.replace(location, onComplete?, onAbort)`

跟`router.push`很像，唯一的不同就是，它不会向history添加新纪录，而是跟它的方法名一样---替换掉当前的history记录。

声明式：`<router-link :to='...' replace>`，编程式：`router.replace(...)`

`router.go(n)`这个方法的参数是一个整数，意思是在history记录中向前或者后退多少步，类似`window.history.go(n)`。

```js
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```

## 命名路由

有时候，通过一个名称来标识一个路由显得更方便，特别是在链接一个路由，或者是执行一些跳转的时候，可以在创建Router实例的时候，在routes配置中给某个路由设置名称。

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```

要链接到一个命名路由，可以给`router-link`的to属性传一个对象：

`<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>`

跟代码调用`router.push()`是一回事：`router.push({ name: 'user', params: { userId: 123 }})`

示例：

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const Home = { template: '<div>This is Home</div>' }
const Foo = { template: '<div>This is Foo</div>' }
const Bar = { template: '<div>This is Bar {{ $route.params.id }}</div>' }

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/', name: 'home', component: Home },
    { path: '/foo', name: 'foo', component: Foo },
    { path: '/bar/:id', name: 'bar', component: Bar }
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Named Routes</h1>
      <p>Current route name: {{ $route.name }}</p>
      <ul>
        <li><router-link :to="{ name: 'home' }">home</router-link></li>
        <li><router-link :to="{ name: 'foo' }">foo</router-link></li>
        <li><router-link :to="{ name: 'bar', params: { id: 123 }}">bar</router-link></li>
      </ul>
      <router-view class="view"></router-view>
    </div>
  `
}).$mount('#app')
```

## 命名视图

有时候想同时展示多个视图，而不是嵌套展示，例如创建一个布局，有sidebar和main两个视图，命名视图就派上用场了。可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果`router-view`没有设置名字，那么默认为`default`。

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件，确保正确使用components配置

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

## 嵌套命名视图

也有可能使用命名视图创建嵌套视图的复杂布局。这时需要命名用到的嵌套`router-view`组件。

```text
/setings/profile
+----------------------------+
| usersettings
| +-------+---------------+
|| nav | userprofile      |
||     +------------------+
||     | userprofilepreview|

```

* nav只是一个常规组件
* usersettings是一个视图组件
* userProfilereview、userProfile是嵌套的视图组件

`userSettings`组件的`<template>`部分应该是类似下面的这段代码

```html
<!-- UserSettings.vue -->
<div>
  <h1>User Settings</h1>
  <NavBar/>
  <router-view/>
  <router-view name="helper"/>
</div>
```

然后用这个路由配置完成该布局

```js
{
  path: '/settings',
  // 你也可以在顶级路由就配置命名视图
  component: UserSettings,
  children: [{
    path: 'emails',
    component: UserEmailsSubscriptions
  }, {
    path: 'profile',
    components: {
      default: UserProfile,
      helper: UserProfilePreview
    }
  }]
}
```

## 重定向和别名

### 重定向

重定向也是通过`routes`配置来完成，下面例子是`/a`重定向到`/b`

```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

重定向的目标也可以是一个命名的路由

```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```

甚至是一个方法，动态返回重定向目标：

```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const Home = { template: '<router-view></router-view>' }
const Default = { template: '<div>default</div>' }
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
const Baz = { template: '<div>baz</div>' }
const WithParams = { template: '<div>{{ $route.params.id }}</div>' }
const Foobar = { template: '<div>foobar</div>' }
const FooBar = { template: '<div>FooBar</div>' }

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/', component: Home,
      children: [
        { path: '', component: Default },
        { path: 'foo', component: Foo },
        { path: 'bar', component: Bar },
        { path: 'baz', name: 'baz', component: Baz },
        { path: 'with-params/:id', component: WithParams },
        // relative redirect to a sibling route
        { path: 'relative-redirect', redirect: 'foo' }
      ]
    },
    // absolute redirect
    { path: '/absolute-redirect', redirect: '/bar' },
    // dynamic redirect, note that the target route `to` is available for the redirect function
    { path: '/dynamic-redirect/:id?',
      redirect: to => {
        const { hash, params, query } = to
        if (query.to === 'foo') {
          return { path: '/foo', query: null }
        }
        if (hash === '#baz') {
          return { name: 'baz', hash: '' }
        }
        if (params.id) {
          return '/with-params/:id'
        } else {
          return '/bar'
        }
      }
    },
    // named redirect
    { path: '/named-redirect', redirect: { name: 'baz' }},

    // redirect with params
    { path: '/redirect-with-params/:id', redirect: '/with-params/:id' },

    // redirect with caseSensitive
    { path: '/foobar', component: Foobar, caseSensitive: true },

    // redirect with pathToRegexpOptions
    { path: '/FooBar', component: FooBar, pathToRegexpOptions: { sensitive: true }},

    // catch all redirect
    { path: '*', redirect: '/' }
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Redirect</h1>
      <ul>
        <li><router-link to="/relative-redirect">
          /relative-redirect (redirects to /foo)
        </router-link></li>
        <li><router-link to="/relative-redirect?foo=bar">
          /relative-redirect?foo=bar (redirects to /foo?foo=bar)
        </router-link></li>
        <li><router-link to="/absolute-redirect">
          /absolute-redirect (redirects to /bar)
        </router-link></li>
        <li><router-link to="/dynamic-redirect">
          /dynamic-redirect (redirects to /bar)
        </router-link></li>
        <li><router-link to="/dynamic-redirect/123">
          /dynamic-redirect/123 (redirects to /with-params/123)
        </router-link></li>
        <li><router-link to="/dynamic-redirect?to=foo">
          /dynamic-redirect?to=foo (redirects to /foo)
        </router-link></li>
        <li><router-link to="/dynamic-redirect#baz">
          /dynamic-redirect#baz (redirects to /baz)
        </router-link></li>
        <li><router-link to="/named-redirect">
          /named-redirect (redirects to /baz)
        </router-link></li>
        <li><router-link to="/redirect-with-params/123">
          /redirect-with-params/123 (redirects to /with-params/123)
        </router-link></li>
        <li><router-link to="/foobar">
          /foobar
        </router-link></li>
        <li><router-link to="/FooBar">
          /FooBar
        </router-link></li>
        <li><router-link to="/not-found">
          /not-found (redirects to /)
        </router-link></li>
      </ul>
      <router-view class="view"></router-view>
    </div>
  `
}).$mount('#app')
```

### 别名

重定向的意思是，当用户访问`/a`时，URL将会被替换成`/b`，然后匹配路由为`/b`，那么别名又是什么呢。`/a`的别名是`/b`意味着，当用户访问`/b`时，URL会保持为`/b`，但是路由匹配则为`/a`，就像用户访问`/a`一样。对应的路由配置为：

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

别名的功能可以自由地将UI结构映射到任意的URL，而不是受限于配置的嵌套路由结构。

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const Root = { template: '<div>root</div>' }
const Home = { template: '<div><h1>Home</h1><router-view></router-view></div>' }
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
const Baz = { template: '<div>baz</div>' }
const Default = { template: '<div>default</div>' }
const Nested = { template: '<router-view/>' }
const NestedFoo = { template: '<div>nested foo</div>' }

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/root', component: Root, alias: '/root-alias' },
    { path: '/home', component: Home,
      children: [
        // absolute alias
        { path: 'foo', component: Foo, alias: '/foo' },
        // relative alias (alias to /home/bar-alias)
        { path: 'bar', component: Bar, alias: 'bar-alias' },
        // multiple aliases
        { path: 'baz', component: Baz, alias: ['/baz', 'baz-alias'] },
        // default child route with empty string as alias.
        { path: 'default', component: Default, alias: '' },
        // nested alias
        { path: 'nested', component: Nested, alias: 'nested-alias',
          children: [
            { path: 'foo', component: NestedFoo }
          ]
        }
      ]
    }
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Route Alias</h1>
      <ul>
        <li><router-link to="/root-alias">
          /root-alias (renders /root)
        </router-link></li>
        <li><router-link to="/foo">
          /foo (renders /home/foo)
        </router-link></li>
        <li><router-link to="/home/bar-alias">
          /home/bar-alias (renders /home/bar)
        </router-link></li>
        <li><router-link to="/baz">
          /baz (renders /home/baz)
        </router-link></li>
        <li><router-link to="/home/baz-alias">
          /home/baz-alias (renders /home/baz)
        </router-link></li>
        <li><router-link to="/home">
          /home (renders /home/default)
        </router-link></li>
        <li><router-link to="/home/nested-alias/foo">
          /home/nested-alias/foo (renders /home/nested/foo)
        </router-link></li>
      </ul>
      <router-view class="view"></router-view>
    </div>
  `
}).$mount('#app')
```

## 路由组件传参

在组件中使用`$route`会使之与其对应路由形成高度耦合，从而是组件只能在某些特定的URL上使用，限制了灵活性。使用`props`将组件和路由解耦：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```

通过`props`解耦

```js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```

这样便可以在热河地方使用该组件，使得该组件更易于重用和测试。

布尔模式：如果`props`被设置为`true`，`route.params`将会被设置为组件属性。

对象模式：如果`props`是一个对象，它会被按原样设置为组件属性。当`props`是静态的时候有用。

```js
const router = new VueRouter({
  routes: [
    { path: '/promotion/from-newsletter', component: Promotion, props: { newsletterPopup: false } }
  ]
})
```

函数模式：可以创建一个函数返回`props`,这样可以将参数转换成另一种类型，将静态值与基于路由的值结合等。

```js
const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]
})
```

URL`/search?q=vue`会将`{query:'vue'}`作为属性传递个`searchUser`组件。

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Hello from './Hello.vue'

Vue.use(VueRouter)

function dynamicPropsFn (route) {
  const now = new Date()
  return {
    name: (now.getFullYear() + parseInt(route.params.years)) + '!'
  }
}

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/', component: Hello }, // No props, no nothing
    { path: '/hello/:name', component: Hello, props: true }, // Pass route.params to props
    { path: '/static', component: Hello, props: { name: 'world' }}, // static values
    { path: '/dynamic/:years', component: Hello, props: dynamicPropsFn }, // custom logic for mapping between route and props
    { path: '/attrs', component: Hello, props: { name: 'attrs' }}
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Route props</h1>
      <ul>
        <li><router-link to="/">/</router-link></li>
        <li><router-link to="/hello/you">/hello/you</router-link></li>
        <li><router-link to="/static">/static</router-link></li>
        <li><router-link to="/dynamic/1">/dynamic/1</router-link></li>
        <li><router-link to="/attrs">/attrs</router-link></li>
      </ul>
      <router-view class="view" foo="123"></router-view>
    </div>
  `
}).$mount('#app')
```

## HTML5 history 模式

`vue-router`默认hash模式---使用URL的hash来模拟一个完整的URL，于是当URL改变时，页面不会重新加载。如果不想要很丑的hash，可以使用路由的history模式，这种模式充分利用`history.pushState`API来完成URL跳转而无须重新加载页面。

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

当使用history模式时,URL就像正常的URL,例如`http://yoursite.com/user/id`。

## 导航守卫

正如其名，`vue-router`提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种方式植入路由导航过程中：全局的，单个路由独享，或者组件级的。**参数或查询的改变并不会触发进入/离开的导航守卫**，可以通过观察`$route`对象，或者使用`beforeRouteUpdate`的组件内守卫。

### 全局前置守卫

可以使用`router.beforeEach`注册一个全局前置守卫：

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫resolve完之前一直处于等待中。

每个守卫方法接收三个参数：

* `to:Route`:即将要进入的目标路由对象
* `from:Route`:当前导航正要离开的路由
* `next:function`:一定要调用该方法来resolve这个钩子。执行效果依赖`next`方法的调用参数。
  1. `next()`进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是confirmed
  2. `next(false)`:中断当前的导航。如果浏览器的URL改变了，那么URL地址会充值到from路由对应的地址。
  3. `next('/')`或者`next({path:'/'})`:跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。可以向next传递任意位置对象，且允许设置如`replace: true`、`name: 'home'`之类的选项以及任何用在`router-link`的to `prop`或`router.push`中的选项。
  4. `next(error)`：如果传入next的参数是一个error实例，则导航会被终止且该错误会被传递给`router.onError()`注册过的回调。

**确保要调用`next`方法，否者钩子就不会被resolved**

### 全局解析守卫

可以用`router.beforeResolve`注册一个全局守卫。这和`router.beforeEach`类似，区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用。

### 全局后置钩子

也可以注册全局后置钩子，和守卫不同的是，这些钩子不会接受`next`函数也不会改变导航本身

```js
router.afterEach((to, from) => {
  // ...
})
```

可以在路由配置上直接定义`beforeEnter`守卫：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

这些守卫与全局前置守卫的方法参数是一样的。

### 组件内的守卫

最后，可以在路由组件内直接定义一下路由导航守卫

* `beforeRouteEnter`
* `beforeRouteUpdate`
* `beforeRouteLeave`

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

`beforeRouteEnter`守卫不能访问this，因为守卫在导航确认前被调用，因此即将登场的新组建还没被创建。可以通过传一个回调给next来访问组件实例，在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

`beforeRouteEnter`是支持给next传递回调的唯一守卫。对于`beforeRouteUpdate`和`beforeRouteLeave`来说，this已经可用，所以不支持传递回调。

```js
beforeRouteUpdate (to, from, next) {
  // just use `this`
  this.name = to.params.name
  next()
}
```

离开守卫通常用来禁止用户还未保存修改前突然离开。该导航可以通过`next(false)`来取消。

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

### 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用离开守卫。
3. 调用全局的`beforeEach`守卫
4. 在重用的组件里调用`beforeRouteUpdate`守卫
5. 在路由配置里调用`beforeEnter`
6. 解析异步路由组件
7. 在被激活的组件里调用`beforeRouteEnter`
8. 调用全局的`beforeResolve`守卫
9. 导航被确认
10. 调用全局的`afterEach`钩子
11. 触发DOM更新
12. 用创建好的实例调用`beforeRouteEnter`守卫中传给next的回调函数

## 路由元信息

定义路由的时候可以配置`meta`字段:

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          // a meta field
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```

如何访问这个`meta`字段呢？

`routes`配置中的每个路由对象为路由记录。路由记录可以是嵌套的，因此，当一个路由匹配成功后，可能匹配多个路由记录。根据上面的路由配置`/foo/bar`这个URL将会匹配父路由记录以及子路由记录。一个路由匹配到的所有路由记录会暴露为`$route`对象的`$route.matched`数组，因此，需要遍历`$route.matched`来检查路由记录中的`meta`字段。下面例子展示在全局导航守卫中检查元字段:

```js
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // this route requires auth, check if logged in
    // if not, redirect to login page.
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next() // 确保一定要调用 next()
  }
})
```

## 过渡动效

`<router-view>`是基本的动态组件。所以可以用`<transition>`组件给它添加一些过渡效果。

### 基于路由的动态过渡

可以基于当前路由与路由的变化关系，动态设置过渡效果。

```html
<!-- 使用动态的 transition name -->
<transition :name="transitionName">
  <router-view></router-view>
</transition>
```

```js
// 接着在父组件内
// watch $route 决定使用哪种过渡
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

完整例子:

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const Home = {
  template: `
    <div class="home">
      <h2>Home</h2>
      <p>hello</p>
    </div>
  `
}

const Parent = {
  data () {
    return {
      transitionName: 'slide-left'
    }
  },
  beforeRouteUpdate (to, from, next) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
    next()
  },
  template: `
    <div class="parent">
      <h2>Parent</h2>
      <transition :name="transitionName">
        <router-view class="child-view"></router-view>
      </transition>
    </div>
  `
}

const Default = { template: '<div class="default">default</div>' }
const Foo = { template: '<div class="foo">foo</div>' }
const Bar = { template: '<div class="bar">bar</div>' }

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/', component: Home },
    { path: '/parent', component: Parent,
      children: [
        { path: '', component: Default },
        { path: 'foo', component: Foo },
        { path: 'bar', component: Bar }
      ]
    }
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Transitions</h1>
      <ul>
        <li><router-link to="/">/</router-link></li>
        <li><router-link to="/parent">/parent</router-link></li>
        <li><router-link to="/parent/foo">/parent/foo</router-link></li>
        <li><router-link to="/parent/bar">/parent/bar</router-link></li>
      </ul>
      <transition name="fade" mode="out-in">
        <router-view class="view"></router-view>
      </transition>
    </div>
  `
}).$mount('#app')
```

## 数据获取

有时候，进入某个路由后需要从服务器获取数据。比如，渲染用户信息时，需要从服务器获取用户的数据。通过两种方式来实现:

* 导航完成之后获取:先完成导航，然后在接下来的组件生命周期钩子中获取数据，在数据获取期间显示加载中之类的指示。
* 导航完成之前获取:导航完成前，在路由进入的守卫中获取数据，在数据获取成功后执行导航。

### 导航完成后获取数据

当使用这种方式时，会马上导航和渲染组件，然后在组件的`created`钩子中获取数据。有机会在数据获取期间展示一个loading状态，还可以在不同视图间展示不同的loading状态。

假设有一个post组件，需要基于`$route.params.id`获取文章数据:

```html
<template>
  <div class="post">
    <div v-if="loading" class="loading">
      Loading...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
```

```js
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace getPost with your data fetching util / API wrapper
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

### 导航完成前获取数据

通过这种方式，在导航传入新的路由前获取数据，可以在接下来的组件的`beforeRouteEnter`守卫中获取数据，当数据获取成功后只调用`next`方法。

```js
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  // 路由改变前，组件就已经渲染完了
  // 逻辑稍稍不同
  beforeRouteUpdate (to, from, next) {
    this.post = null
    getPost(to.params.id, (err, post) => {
      this.setData(err, post)
      next()
    })
  },
  methods: {
    setData (err, post) {
      if (err) {
        this.error = err.toString()
      } else {
        this.post = post
      }
    }
  }
}
```

在为后面的视图获取数据时，用户会停留在当前的界面，因此建议在数据获取期间，显示一些进度条或者别的指示。如果数据获取失败，同样有必要展示一些全局的错误提醒。

## 滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。`vue-router`能做好，而且更好，它让你可以自定义路由切换时，页面如何滚动。**注意:这个功能只在支持`history.pushState`的浏览器中可用**。

当创建一个Route实例，可以提供一个`scrollBehavior`方法：

```js
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return 期望滚动到哪个的位置
  }
})
```

`scrollBehavior`方法接受to和from路由对象。第三个参数`savedPosition`当且仅当`popstate`导航(通过浏览器的前进、后退按钮触发)时才可用。

这个方法返回滚动位置的信息对象，长这样：

* `{x: number, y: number}`
* `{selector: string, offset? : {x: number, y: number}}`

如果返回一个falsy的值，或者是一个空对象，那么不会发生滚动。

```js
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

对于所有路由导航，简单地让页面滚动到顶部。返回`savePosition`，在按下后退、前进按钮时，就会像浏览器的原生表现那样：

```js
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition
  } else {
    return { x: 0, y: 0 }
  }
}
```

模拟**滚动到锚点**的行为:

```js
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
    }
  }
}
```

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const Home = { template: '<div class="home">home</div>' }
const Foo = { template: '<div class="foo">foo</div>' }
const Bar = {
  template: `
    <div class="bar">
      bar
      <div style="height:1500px"></div>
      <p id="anchor" style="height:500px">Anchor</p>
      <p id="anchor2" style="height:500px">Anchor2</p>
      <p id="1number">with number</p>
    </div>
  `
}

// scrollBehavior:
// - only available in html5 history mode
// - defaults to no scroll behavior
// - return false to prevent scroll
const scrollBehavior = function (to, from, savedPosition) {
  if (savedPosition) {
    // savedPosition is only available for popstate navigations.
    return savedPosition
  } else {
    const position = {}

    // scroll to anchor by returning the selector
    if (to.hash) {
      position.selector = to.hash

      // specify offset of the element
      if (to.hash === '#anchor2') {
        position.offset = { y: 100 }
      }

      // bypass #1number check
      if (/^#\d/.test(to.hash) || document.querySelector(to.hash)) {
        return position
      }

      // if the returned position is falsy or an empty object,
      // will retain current scroll position.
      return false
    }

    return new Promise(resolve => {
      // check if any matched route config has meta that requires scrolling to top
      if (to.matched.some(m => m.meta.scrollToTop)) {
        // coords will be used if no selector is provided,
        // or if the selector didn't match any element.
        position.x = 0
        position.y = 0
      }

      // wait for the out transition to complete (if necessary)
      this.app.$root.$once('triggerScroll', () => {
        // if the resolved position is falsy or an empty object,
        // will retain current scroll position.
        resolve(position)
      })
    })
  }
}

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  scrollBehavior,
  routes: [
    { path: '/', component: Home, meta: { scrollToTop: true }},
    { path: '/foo', component: Foo },
    { path: '/bar', component: Bar, meta: { scrollToTop: true }}
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Scroll Behavior</h1>
      <ul>
        <li><router-link to="/">/</router-link></li>
        <li><router-link to="/foo">/foo</router-link></li>
        <li><router-link to="/bar">/bar</router-link></li>
        <li><router-link to="/bar#anchor">/bar#anchor</router-link></li>
        <li><router-link to="/bar#anchor2">/bar#anchor2</router-link></li>
        <li><router-link to="/bar#1number">/bar#1number</router-link></li>
      </ul>
      <transition name="fade" mode="out-in" @after-leave="afterLeave">
        <router-view class="view"></router-view>
      </transition>
    </div>
  `,
  methods: {
    afterLeave () {
      this.$root.$emit('triggerScroll')
    }
  }
}).$mount('#app')
```

### 异步滚动

也可以返回一个Promise来得出预期的位置描述：

```js
scrollBehavior (to, from, savedPosition) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ x: 0, y: 0 })
    }, 500)
  })
}
```

将其挂载到从页面级别的过渡组件的事件上，令其滚动行为和页面过渡一起良好运行。考虑到用例的多样性和复杂性，仅提供这个原始的接口，以支持不同用户场景的具体实现。

## 路由懒加载

当打包构建应用时，JavaScript包会变得非常大，影响页面加载。如果把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，会非常高效。结合vue的异步组件和webpack的代码分割功能，轻松实现路由组件的懒加载。

定义一个能够被webpack自动代码分割的异步组件`const Foo = () => import('./Foo.vue')`，在路由配置中什么都不需要改变，只需要像往常一样使用`Foo`

```js
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
```

### 把组件按组分块

有时候把某个路由下的所有组件都打包在同个异步块中，只需要命名chunk，一个特殊的注释语法来提供chunk name

```js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

webpack会将任何一个异步模块与相同的块名组合到相同的异步块中。

# API

## `<router-link>`

`<router-linker>`组件支持用户在具有路由功能的应用中导航。通过`to`属性指定目标地址，默认渲染成带有正确链接的`<a>`标签，通过配置`tag`属性生成别的标签。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的css类名。`<router-link>`比起写死的`<a href='...'>`会好一些：

* 无论是HTML5 history模式还是hash模式，它的表现行为一致，所以，当切换路由模式，或者使用hash模式，无须作任何变动。
* 在HTML5 history模式下，`router-link`会守卫点击事件，让浏览器不再重新加载页面。
* 在HTML5 history模式下使用base选项后，所有的to属性不需要写了(基路径)。

`v-slot`API

`router-link`通过一个作用域插槽暴露底层的定制能力。这是一个更高阶的API，主要面向库作者，但也可以为开发者提供便利，多数情况用在一个类似NavLink这样的组件中。**在使用`v-slot`API时，需要向`router-link`传入一个单独的子元素。**否者`router-linker`将会把子元素包裹在一个`span`元素内。

```html
<router-link
  to="/about"
  v-slot="{ href, route, navigate, isActive, isExactActive }"
>
  <NavLink :active="isActive" :href="href" @click="navigate"
  >
      {{ route.fullPath }}
  </NavLink>
</router-link>
```

* href:解析后的URL，将会作为一个`a`元素的href attribute。
* route:解析后的规范化的地址。
* navigate: 触发导航的函数。会在必要时自动阻止事件，和`router-link`同理。
* isActive:如果需要应用激活的class则为true。允许应用一个任意的class。
* isExactActive:如果需要应用精确激活的class则为true。允许应用一个任意的class。

### 示例：将激活的class应用在外层元素

有时候可能想把激活的class应用到一个外部元素而不是`<a>`标签本身，这时可以在一个`router-link`中包裹该元素并使用`v-slot`property来创建链接。

```html
<router-link
  to="/foo"
  v-slot="{ href, route, navigate, isActive, isExactActive }"
>
  <li
    :class="[isActive && 'router-link-active', isExactActive && 'router-link-exact-active']"
  >
    <a :href="href" @click="navigate">{{ route.fullPath }}</a>
  </li>
</router-link>
```

## `<router-link>` Props

to---类型:`string | Location`   required

表示目标路由的链接。当被点击后，内部会立刻把`to`的值传到`router.push()`，所以这个值可以是一个字符串或者是描述目标位置的对象。

```html
<!-- 字符串 -->
<router-link to="home">Home</router-link>
<!-- 渲染结果 -->
<a href="home">Home</a>

<!-- 使用 v-bind 的 JS 表达式 -->
<router-link v-bind:to="'home'">Home</router-link>

<!-- 不写 v-bind 也可以，就像绑定别的属性一样 -->
<router-link :to="'home'">Home</router-link>

<!-- 同上 -->
<router-link :to="{ path: 'home' }">Home</router-link>

<!-- 命名的路由 -->
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

<!-- 带查询参数，下面的结果为 /register?plan=private -->
<router-link :to="{ path: 'register', query: { plan: 'private' }}">Register</router-link>
```

replace---类型:boolean，默认值:false

设置replace属性后，当点击时，会调用`router.replace()`而不是`router.push`，于是导航后不会留下history记录。`<router-link :to="{ path: '/abc'}" replace></router-link>`

append---类型:boolean，默认值:false

设置append属性后，则在当前(相对)路径前添加基路径。例如，从`/a`导航到一个相对路径`b`，如果没有配置`append`，则路径为`/b`，如果配了，则为`/a/b`，`<router-link :to="{ path: 'relative/path'}" append></router-link>`

tag---类型:string，默认值:'a'

有时候想要`<router-link>`渲染成某种标签，例如`<li>`。可以使用`tag` prop类指定何种标签，同样它还是会监听点击，触发导航。

```html
<router-link to="/foo" tag="li">foo</router-link>
<!-- 渲染结果 -->
<li>foo</li>
```

active-class---类型:string，默认值:"router-link-active"

设置链接激活时使用的css类名，默认值可以通过路由的构造选项`linkActiveClass`来全局配置。

exact---类型:boolean，默认值:false

"是否激活"默认类名的依据是**是否匹配**，链接使用"精确匹配模式"

```html
<!-- 这个链接只会在地址为 / 的时候被激活 -->
<router-link to="/" exact></router-link>
```

event---类型:`string | Array<string>`，默认值`'click'`

声明可以用来触发导航的事件。可以是一个字符串或是一个包含字符串的数组。

exact-active-class---类型:string，默认值:"router-link-exact-active"

配置当前链接被精确匹配的时候应该激活的class。注意默认值也是可以通过路由构造函数选项`linkExactActiveClass`进行全局配置。

### `<router-view>`

`<router-view>`组件是一个functional组件，渲染路径匹配到的视图组件。`<router-view>`渲染组件还可以内嵌自己的`<router-view>`，根据嵌套路径，渲染嵌套组件。其他属性都直接传给渲染的组件，很多时候，每个路由的数据都是包含在路由参数中。因为它也是个组件，所以可以配合`<transition>`和`<keep-alive>`使用。如果两个结合一起用，要确保在内层使用`<keep-alive>`

```html
<transition>
  <keep-alive>
    <router-view></router-view>
  </keep-alive>
</transition>
```

### `<router-view>` Props

name---类型:string，默认值: "default"。如果`<router-view>`设置名称，则会渲染对应的路由配置中`components`下的相应组件。

### Router构建选项

routes---类型:`Array<RouteConfig>` ，`<RouteConfig>`的类型定义:

```typescript
interface RouteConfig = {
  path: string,
  component?: Component,
  name?: string, // 命名路由
  components?: { [name: string]: Component }, // 命名视图组件
  redirect?: string | Location | Function,
  props?: boolean | Object | Function,
  alias?: string | Array<string>,
  children?: Array<RouteConfig>, // 嵌套路由
  beforeEnter?: (to: Route, from: Route, next: Function) => void,
  meta?: any,

  // 2.6.0+
  caseSensitive?: boolean, // 匹配规则是否大小写敏感？(默认值：false)
  pathToRegexpOptions?: Object // 编译正则的选项
}
```

mode---类型:string，默认值:`'hash'(浏览器环境) | 'abstract'(Node.js环境)`

可选值:`'hash' | 'history' | 'abstract'`
