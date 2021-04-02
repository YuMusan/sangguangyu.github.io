# vue 3

## 应用 & 组件实例

### 创建一个应用

vue3中都是通过createApp函数创建一个新的应用实例开始：

`const app = Vue.createApp({/*选项*/})`

该应用实例用来在应用中注册全局组件。

```js
const app = Vue.createApp({})
app.component('APP',AppComponent)
app.directive('focus',FocusDirective)
app.use(localePlugin)

// 应用实例暴露的大多方法都会返回同一实例，允许链式写法
Vue.createApp({})
    .component('APP',AppComponent)
    .directive('focus',FocusDirective)
    .use(localePlugin)
```

## 根组件

传递给createApp的选项用于配置根组件，挂在应用时，该组件被用作渲染的起点。
一个应用需要被挂载到一个DOM元素中。

```js
const RootComponent = {
    // 选项
}
const app = Vue.CreateApp(RootComponent)
const vm = app.mount('#app')
```
mount 不返回应用本身，它返回的是根组件实例。尽管本页面上所有的示例都只需要一个单一的组件就可以，但是大多数的真实应用都是被组织成一个嵌套的可重用的组件树。

```text
Root Component
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

每个组件都有自己的组件实例vm。

### 组件实例property

data property，在data中定义的property是通过组件实例暴露的

```js
const app = Vue.create({
    data() {
        return { count:4 }
    }
})
const vm = app.mount('#app')
console.log(vm.count) // => 4
```
还有各种其他的组件选项可以将用户定义的property添加到组件实例中， 例如methos,props,computed,inject和setup。组件中的所有property，无论如何定义，都可以在组件的模板中访问。
vue还通过组件实例暴露了一些内置property，如$attrs和$emit，这些property都有一个$前缀，以避免与用户定义的property名冲突。

## 生命周期钩子

每个组件在被创建时都要经过一系列的初始化过程---例如，设置数据监听，编译模板，将实例挂载到DOM并在数据变化时更新DOM等，同时在这个过程中也会运行一些叫做生命周期钩子的函数，可以给用户在不同阶段添加自己的代码。
比如在created钩子可以用来在实例被创建之后执行代码：

```js
Vue.createApp({
  data() {
    return { count: 1}
  },
  created() {
    // `this` 指向 vm 实例
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

也有一些其他的钩子，在实例生命周期的不同阶段被调用，如mounted、updated和unmounted，生命周期钩子的this上下文指向调用它得当前活动实例。

### 生命周期图示

![生命周期图](https://v3.cn.vuejs.org/images/lifecycle.svg)

## 模板语法

vue.js使用了基于HTML的模板语法，允许开发者声明式地将DOM绑定至底层组件实例的数据。所有vue.js的模板都是合法的HTML，能被遵循规范的浏览器和HTML解析器解析。

在底层实现上，vue将模板编译成虚拟DOM渲染函数，结合响应式系统，vue计算出最少需要需要重新渲染多少组件，并把DOM操作次数减到最少。如果熟悉虚拟DOM并且偏爱JavaScript的原始力量，也可以直接写渲染函数render，使用可选的jsx语法。

## Data property 和方法

### Data property
组件的data选项是一个函数，vue在创建新组件实例的过程中调用此函数，它返回一个对象，然后vue会通过响应性系统将其包裹起来，并以$data 的形式存储在组件实例中，该对象的任何顶级property也直接通过组件实例暴露出来：

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.$data.count) // => 4
console.log(vm.count)       // => 4

// 修改 vm.count 的值也会更新 $data.count
vm.count = 5
console.log(vm.$data.count) // => 5

// 反之亦然
vm.$data.count = 6
console.log(vm.count) // => 6
```
### 方法
使用methods选项向组件实例添加方法，它是一个包含所需方法的对象：

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  },
  methods: {
    increment() {
      // `this` 指向该组件实例
      this.count++
    }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4

vm.increment()

console.log(vm.count) // => 5
```

vue自动为methods绑定this，以便它始终指向组件实例，确保方法在用作事件监听或回调时保持正确的this指向。定义methods时应避免使用箭头函数，这会阻止vue绑定恰当的this指向。

这些methods和组件实例的其他property一样可以在组件的模板中被访问，在模板中，通常被当做事件监听使用

```html
<button @click="increment">Up vote</button>
```

在上面的例子中，点击button会调用increment方法。

### 防抖和节流

vue没有内置支持防抖和节流，但是可以用lodash等库来实现。
如果某个组件仅使用一次，可以在methods中直接应用防抖：

```html
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
  Vue.createApp({
    methods: {
      // 用 Lodash 的防抖函数
      click: _.debounce(function() {
        // ... 响应点击 ...
      }, 500)
    }
  }).mount('#app')
</script>
```

但是，这种方法在可复用组件中有潜在的问题，因为它们共享相同的防抖函数。为了使组件实例彼此独立，可以在生命周期购子的created中添加该防抖函数

```js
app.component('save-button', {
  created() {
    // 用 Lodash 的防抖函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 响应点击 ...
    }
  },
  template: `
    <button @click="debouncedClick">
      Save
    </button>
  `
})
```

## 计算属性和侦听器
### 计算属性

模板内的表达式非常便利，但是设计它们的初衷是用于简单运算，在模板中放入太多的逻辑会让模板过重且难以维护。例如，一个嵌套数组对象：
```js
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
})
```
想根据author是否已经有一些书来显示不同的消息

```html
<div id="computed-basics">
  <p>Has published books:</p>
  <span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
</div>
```

此时，模板不再是简单的和声明性的，它执行的计算取决于author.books如果要在模板中多次包含此计算，问题会变得更糟。所以对于任何包含响应式数据的复杂逻辑，都应该使用计算属性。

```html
<div id="computed-basics">
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // 计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向 vm 实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}).mount('#computed-basics')
```

这里声明了一个计算属性publishedBooksMessage，更改data中books数组的值，可以看到它的相应更改。可以像普通属性一样将数据绑定到模板中的计算属性，vue知道vm.publishedBooksMessage依赖于vm.author.books，因此当books发生改变是，所有依赖publishedBooksMessage的绑定也会更新，而且是声明的方式创建依赖关系：计算属性的getter函数没有副作用，它更易于测试和理解。

**计算属性是基于它们的反应依赖关系缓存的**，计算属性只在相关响应式依赖发生改变时，才会重新求值，这意味着，只要books还没有发生改变，多次访问publishBooksMessage计算属性会立即返回之前的计算结果，而不必再次执行函数。

这也同样意味着下面的计算属性将不再更新，因为Date.now()不是响应式依赖

```js
computed: {
  now() {
    return Date.now()
  }
}
```
相比之下，每当重新渲染时，调用方法将总会再次执行函数。
为什么需要缓存？假设有一个性能开销比较大的计算属性list，它需要遍历一个巨大的数组并做大量的计算。然后可能有其他的计算属性依赖于list，如果没有缓存，将不可避免的多次执行list的getter,如果不希望有缓存，用method来替代。

### 计算属性的setter

计算属性默认只有getter，不过在需要时也可以提供一个setter

```js
// ...
computed: {
  fullName: {
    // getter
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```
再运行vm.fullName='john Doe'时，setter会被调用，vm.firstName和vm.lastName也会相应被更新。

### 侦听器

vue通过watch选项提供了一个更通用的方法，来响应数据的变化，当需要数据变化时执行异步或开销较大的操作时，这个方式是最有用的
```html
div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</div>
```

```html
<!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
<!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
  const watchExampleVM = Vue.createApp({
    data() {
      return {
        question: '',
        answer: 'Questions usually contain a question mark. ;-)'
      }
    },
    watch: {
      // whenever question changes, this function will run
      question(newQuestion, oldQuestion) {
        if (newQuestion.indexOf('?') > -1) {
          this.getAnswer()
        }
      }
    },
    methods: {
      getAnswer() {
        this.answer = 'Thinking...'
        axios
          .get('https://yesno.wtf/api')
          .then(response => {
            this.answer = response.data.answer
          })
          .catch(error => {
            this.answer = 'Error! Could not reach the API. ' + error
          })
      }
    }
  }).mount('#watch-example')
</script>
```

这个示例中，使用watch选项允许执行异步操作，限制执行该操作的频率，并在得到最终结果前，设置中间状态，这些都是计算属性无法做到的。

## Class与Style绑定
操作元素的class列表和内联样式是数据绑定的一个常见需求，因为都是attribute，所以用v-bind处理它们。

### 绑定HTML Class
对象语法，传给:class(v-bind:class的简写)一个对象，以动态地切换class： `<div :class="{ active: isActive }"></div>`

上面的语法表示active这个class存在与否将取决于数据property isActive的truthiness.

数组语法，可以把一个数组传给:class，以应用一个class列表
```html
<div :class="[activeClass, errorClass]"></div>
```
```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```
也可在数组语法中使用对象语法
```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### 绑定内联样式

对象语法    数组语法

## 条件渲染
### v-if

v-if指令用于条件性的渲染一块内容，这块内容只会在指令的表达式返回truthy值的时候被渲染。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>


<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

### v-show
另一个用于条件性展示元素的选项是v-show

```html
<h1 v-show="ok">Hello!</h1>
```

不同的是带有v-show的元素始终会被渲染并保存在DOM中，v-show只是简单的切换元素CSS property display，v-show不支持`<template>`元素，也不支持v-else

v-if和v-show

v-ifpm

