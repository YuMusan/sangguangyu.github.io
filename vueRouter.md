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
