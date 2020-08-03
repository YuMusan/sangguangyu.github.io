# Vue.js
Vue是一套用于构建用户界面的渐进式框架，Vue被设计为自底向上逐层应用，它的核心库只关注视图层，易于上手，还便于与第三方库或既有项目整合。
#### 声明式渲染
Vue.js的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进DOM的系统
```html
<div id="app">
  {{ message }}
</div
```
```js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
现在数据和DOM已经被建立了关联，所有的东西都是响应式的。
#### 条件与循环
```html
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
```
```js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```
这里演示不仅可以把数据绑定到DOM文本或特性，还可以绑定到DOM结构。Vue也提供一个强大的过渡效果系统，可以在Vue插入更新移除元素是自动应用过渡效果。
v-for指令可以绑定数组的数据来渲染一个项目列表
```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
```js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})
```
#### 处理用户输入
如果用户和应用进行交互，可以用v-on指令添加一个事件监听器，通过它调用在Vue实例中定义的方法
```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">反转消息</button>
</div>
```
```js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
Vue还提供了v-model指令，它能轻松实现表单输入和应用状态之间的双向绑定。
```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
```js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
#### 组件化应用构建
组件系统是Vue的另一个重要概念，因为它是一种抽象，允许我们使用小型独立和通常可复用的组件构建大型应用

![alt](https://cn.vuejs.org/images/components.png)

在Vue中，一个组件本质上是一个拥有预定义选项的一个Vue实例。Vue中注册组件很简单
```js
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})

var app = new Vue(...)

```
```html
<ol>
  <!-- 创建一个 todo-item 组件的实例 -->
  <todo-item></todo-item>
</ol>
```
-----
## 创建一个Vue实例
每个Vue应用都是从通过用Vue函数创建一个新的Vue实例开始
```js
var vm = new Vue({
  // 选项
})
```
当创建一个Vue实例是，可以传入一个选项对象。
一个Vue应用由一个通过new vue创建的根Vue实例，以及可选的嵌套的，可复用的组件树组成。举个例子，一个todo应用的组件树是这样
```text
根实例
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```
#### 数据与方法
当一个Vue实例被创建时，它将data对象中的所有属性加入到Vue的响应式系统中。当这些属性的值发生改变，师徒将会产生响应，即匹配更新为新的值。
```js
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的属性
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```
只有当实例被创建时就已经存在于data中的属性才是响应式的。
唯一的例外时使用Object.freeze()，这会阻止修改现有的属性，意味着响应系统无法再追踪变化。
```js
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```
```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```
------
## 实例生命周期钩子
每个Vue实例在被创建时都要经过一系列的初始化过程，例如需要设置数据监听、编译模板。将实例挂载到DOM并在数据变化时更新DOM等。同时在运行的过程中暴露出生命周期钩子函数，在这些钩子函数中，用户可以构造自己的逻辑代码和一些异步操作。
比如`created`钩子可以用来在一个实例被创建之后执行代码：
```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```
生命周期钩子的this上下文指向调用它的Vue实例。
>不要在选项属性或回调上使用箭头函数。比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数并没有 this，this 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。

## 生命周期图示
![alt](https://cn.vuejs.org/images/lifecycle.png)

在图示的生命周期中，钩子函数有：
* beforeCreate
* created
* beforeMount
* mounted
* beforeUpdate
* updated
* beforeDestroy
* destroyed
------
## 模板语法
Vue.js使用基于HTML的模板语法，允许声明式地将DOM绑定至底层Vue实例的数据。所有Vue.js的模板都是合法的HTML，所以能被遵循规范的浏览器和HTML解析器解析。
#### 插值
#### #文本
数据绑定最常见的形式就是使用**Mustache**语法(双大括号)的文本插值：
`<span>Message: {{ msg }}</span>`
双大括号标签将会被替代为对应数据上msg属性的值。无论何时，绑定的数据对象上msg属性发生了改变，插值处的内容都会更新。
#### #原始HTML
双大括号会将数据解释为普通文本，而非HTML代码。为了输出真正的HTML，需要使用`v-html`指令
```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```
```text
Using mustaches:<span style="color:red">this should be red.</span>
Using v-html directive :this should be red.
```
#### #使用JavaScript表达式
对于数据绑定，Vue.js提供完全的JavaScript表达式支持。
```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```
这些表达式会在所属Vue实例的数据作用域下作为JavaScript被解析。
#### 指令
指令是带有`v-`前缀的特殊特性。指令特性的值预期是**单个JavaScript表达式**。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于DOM。`<p v-if="seen">现在你看到我了</p>`。这里,`v-if`指令将根据表达式`seen`的值得真假来插入/移除`<p>`元素。
#### #参数
一些指令能够接受一个**参数**，在指令名称之后以冒号表示。例如，`v-bind`指令可以用于响应式地更新HTML特性`<a v-bind:href="url">...</a>`
在这里href是参数，告知v-bind指令将该元素的href特性与表达式url的值绑定。
v-on指令，它用于监听DOM事件：`<a v-on:click="doSomething">...</a>`
这里参数是监听的事件名。
#### #动态参数
从2.6.0开始，可以用方括号括起来的JavaScript表达式作为一个指令的参数
```html
<!--
注意，参数表达式的写法存在一些约束，如之后的“对动态参数表达式的约束”章节所述。
-->
<a v-bind:[attributeName]="url"> ... </a>
```
这里的`attributeName`会被作为一个JavaScript表达式进行动态求值，求得的值将会作为最终的参数来使用。
#### #修饰符
修饰符是以`.`指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，`.prevent`修饰符告诉`v-on`指令对用出发的事件调用`event.preventDefault`.
```html
<form v-on:submit.prevent="onSubmit">...</form>
```
#### #v-bind/v-on缩写
```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>
<!-- 缩写 -->
<a :href="url">...</a>

<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>
<!-- 缩写 -->
<a @click="doSomething">...</a>
```
-----
