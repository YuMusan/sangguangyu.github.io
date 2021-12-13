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
对比 vue2
一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例
```js
new Vue({
  render: h => h(App)
}).$mount("#app");
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
vue还通过组件实例暴露了一些内置property，如 `$attrs` 和 `$emit`，这些 property 都有一个$前缀，以避免与用户定义的property名冲突。

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
如果想切换多个元素呢？此时可以把一个 `<template>`元素当做不可见的包裹元素，并在上面使用 v-if。

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### v-show
另一个用于条件性展示元素的选项是v-show

```html
<h1 v-show="ok">Hello!</h1>
```

不同的是带有v-show的元素始终会被渲染并保存在DOM中，v-show只是简单的切换元素CSS property display，v-show不支持`<template>`元素，也不支持v-else

v-if和v-show

v-if 是“真正”的条件渲染，因为它会确保在切换过程中，条件块内的事件监听器和子组件适当地被销毁和重建。

v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。

## 列表渲染

v-for 指令基于一个数组来渲染一个列表。v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。

```html
<ul id="array-rendering">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  }
}).mount('#array-rendering')
```
index也可以作为参数

也可以用 v-for 来遍历一个对象的 property。

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```
```js
Vue.createApp({
  data() {
    return {
      myObject: {
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10'
      }
    }
  }
}).mount('#v-for-object')
```
key index也可以作为参数

### 维护状态
Vue 正在更新使用 v-for 渲染的元素列表时，它默认使用“就地更新”的策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。

尽可能在使用 v-for 时提供 key attribute，唯一识别

### 在组件上使用 v-for

```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
></my-component>
```
任何数据都不会被自动传递到组件里，因为组件有自己独立的作用域。为了把迭代数据传递到组件里，要使用 props，不自动将 item 注入到组件里的原因是，这会使得组件与 v-for 的运作紧密耦合。明确组件数据的来源能够使组件在其他场合重复使用。

## 事件处理

使用 v-on 指令 (通常缩写为 @ 符号) 来监听 DOM 事件，并在触发事件时执行一些 JavaScript

```html
<div id="event-with-method">
  <!-- `greet` 是在下面定义的方法名 -->
  <button @click="greet">Greet</button>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      name: 'Vue.js'
    }
  },
  methods: {
    greet(event) {
      // `methods` 内部的 `this` 指向当前活动实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM event
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
}).mount('#event-with-method')
```

### 事件修饰符

在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 v-on 提供了事件修饰符。

```html
<!-- 阻止单击事件继续冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发，   -->
<!-- 而不会等待 `onScroll` 完成，                    -->
<!-- 以防止其中包含 `event.preventDefault()` 的情况  -->
<div @scroll.passive="onScroll">...</div>
```

## 表单输入绑定

用 v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。

v-model 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

* text 和 textarea 元素使用 value property 和 input 事件；
* checkbox 和 radio 使用 checked property 和 change 事件；
* select 字段将 value 作为 prop 并将 change 作为事件。

### 修饰符

.lazy  在“change”时而非“input”时更新

.number  自动将用户的输入值转为数值类型

.trim  自动过滤用户输入的首尾空白字符


## 组件

### 组件注册

定义组件名的方式有两种

```js
// 使用 kebab-case
app.component('my-component-name', {
  /* ... */
})
// 使用 PascalCase
app.component('MyComponentName', {
  /* ... */
})
```

使用ES6 注册

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  }
  // ...
}
```

### 通过 Prop 向子组件传递数据

Prop 是可以在组件上注册的一些自定义 attribute。当一个值被传递给一个 prop attribute 时，它就成为该组件实例中的一个 property。该 property 的值可以在模板中访问，就像任何其他组件 property 一样。

**prop类型**

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // 或任何其他构造函数
}
```
prop 可以通过 v-bind 或简写 : 动态赋值

```html
<!-- 动态赋予一个变量的值 -->
<blog-post :title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post :title="post.title + ' by ' + post.author.name"></blog-post>
```

**Prop验证**
可以为组件的 prop 指定验证要求，如果要求没有被满足，则 Vue 会在浏览器控制台中警告。

```js
app.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 和 `undefined` 值会通过任何类型验证)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组的默认值必须从一个工厂函数返回
      default() {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator(value) {
        // 这个值必须与下列字符串中的其中一个相匹配
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // 具有默认值的函数
    propG: {
      type: Function,
      // 与对象或数组的默认值不同，这不是一个工厂函数——这是一个用作默认值的函数
      default() {
        return 'Default function'
      }
    }
  }
})
```

### 自定义事件

子组件向父组件传值

```js
// 子组件
this.$emit('myEvent')
```
```html
<!-- 父组件 -->
<my-component @my-event="doSomething"></my-component>
```

**验证抛出的事件**

```js
// 子组件
app.component('custom-form', {
  emits: {
    // 验证 submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })
    }
  }
})
```

### 组件使用 v-model

默认情况下，组件上的 v-model 使用 modelValue 作为 prop 和 update:modelValue 作为事件

```html
<!-- 父组件 -->
<my-component v-model:title="bookTitle"></my-component>
```
```js
// 子组件
app.component('my-component', {
  props: {
    title: String
  },
  emits: ['update:title'],
  template: `
    <input
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)">
  `
})
```

## 插槽

slot 可以为复用组件分发不同的内容

父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

子组件 slot:name 占位  父组件 template包裹，v-slot:name

具名插槽

作用域插槽

## Provide / Inject

透传   与react中的context相似

有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。这时使用一对 provide 和 inject。无论组件层次结构有多深，父组件都可以作为其所有子组件的依赖提供者。这个特性有两个部分：父组件有一个 provide 选项来提供数据，子组件有一个 inject 选项来开始使用这些数据。

![透传](https://v3.cn.vuejs.org/images/components_provide.png)


```js
const app = Vue.createApp({})

app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- 模板的其余部分 -->
    </div>
  `
})

app.component('todo-list-statistics', {
  inject: ['user'],
  created() {
    console.log(`Injected property: ${this.user}`) // > 注入的 property: John Doe
  }
})
```

若传值是需访问组件实例 property，需要将 provide 转换为返回对象的函数

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide() {
    return {
      todoLength: this.todos.length
    }
  },
  template: `
    ...
  `
})
```

### 处理响应性

默认情况下，provide/inject 绑定并不是响应式的，可以为 provide 的值分配一个组合式API computed 改变行为,或者响应式对象 reactive

```js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})

app.component('todo-list-statistics', {
  inject: ['todoLength'],
  created() {
    console.log(`Injected property: ${this.todoLength.value}`) // > 注入的 property: 5
  }
})
```

在动态组件上使用 keep-alive 可以使组件实例能够被在它们第一次被创建的时候缓存下来。

```html
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

异步组件

可以实现只在需要的时候才从服务器加载一个模块
```js
import { createApp, defineAsyncComponent } from 'vue'

createApp({
  // ...
  components: {
    AsyncComponent: defineAsyncComponent(() =>
      import('./components/AsyncComponent.vue')
    )
  }
})
```

ref 引用

```js
const app = Vue.createApp({})

app.component('base-input', {
  template: `
    <input ref="input" />
  `,
  methods: {
    focusInput() {
      this.$refs.input.focus()
    }
  },
  mounted() {
    this.focusInput()
  }
})
```

## Mixin

Mixin 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个 mixin 对象可以包含任意组件选项。当组件使用 mixin 对象时，所有 mixin 对象的选项将被“混合”进入该组件本身的选项。

当组件和 mixin 对象含有同名选项时，这些选项将以恰当的方式进行“合并”。在数据的 property 发生冲突时，会以组件自身的数据为优先。

```js
const myMixin = {
  data() {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  data() {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created() {
    console.log(this.$data) // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

同名钩子函数将合并为一个数组，因此都将被调用。另外，mixin 对象的钩子将在组件自身钩子之前调用。

```js
const myMixin = {
  created() {
    console.log('mixin 对象的钩子被调用')
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  created() {
    console.log('组件钩子被调用')
  }
})

// => "mixin 对象的钩子被调用"
// => "组件钩子被调用"
```

值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。

```js
const myMixin = {
  methods: {
    foo() {
      console.log('foo')
    },
    conflicting() {
      console.log('from mixin')
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  methods: {
    bar() {
      console.log('bar')
    },
    conflicting() {
      console.log('from self')
    }
  }
})

const vm = app.mount('#mixins-basic')
vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```
## 自定义指令
除了核心功能默认内置的指令 (例如 v-model 和 v-show)，Vue 也允许注册自定义指令。

全局指令
```js
const app = Vue.createApp({})
// 注册一个全局自定义指令 `v-focus`
app.directive('focus', {
  // 当被绑定的元素挂载到 DOM 中时……
  mounted(el) {
    // 聚焦元素
    el.focus()
  }
})
```
注册局部指令，组件中也接受一个 directives 的选项
```js
directives: {
  focus: {
    // 指令的定义
    mounted(el) {
      el.focus()
    }
  }
}
```
指令中的钩子函数

```js
import { createApp } from 'vue'
const app = createApp({})

// 注册
app.directive('my-directive', {
  // 指令是具有一组生命周期的钩子：
  // 在绑定元素的 attribute 或事件监听器被应用之前调用
  created() {},
  // 在绑定元素的父组件挂载之前调用
  beforeMount() {},
  // 绑定元素的父组件被挂载时调用
  mounted() {},
  // 在包含组件的 VNode 更新之前调用
  beforeUpdate() {},
  // 在包含组件的 VNode 及其子组件的 VNode 更新之后调用
  updated() {},
  // 在绑定元素的父组件卸载之前调用
  beforeUnmount() {},
  // 卸载绑定元素的父组件时调用
  unmounted() {}
})

// 注册 (功能指令)
app.directive('my-directive', () => {
  // 这将被作为 `mounted` 和 `updated` 调用
})

// getter, 如果已注册，则返回指令定义
const myDirective = app.directive('my-directive')
```
钩子函数的参数 (即 el、binding、vnode 和 prevVnode)

## 渲染函数
渲染函数它比模板更接近编译器，Vue 通过建立一个虚拟 DOM 来追踪自己要如何改变真实 DOM。
h() 函数是一个用于创建 VNode 的实用程序。也许可以更准确地将其命名为 createVNode()，但由于频繁使用和简洁，它被称为 h() 。它接受三个参数

```js
// @returns {VNode}
h(
  // {String | Object | Function} tag
  // 一个 HTML 标签名、一个组件、一个异步组件、或
  // 一个函数式组件。
  //
  // 必需的。
  'div',

  // {Object} props
  // 与 attribute、prop 和事件相对应的对象。
  // 这会在模板中用到。
  //
  // 可选的。
  {},

  // {String | Array | Object} children
  // 子 VNodes, 使用 `h()` 构建,
  // 或使用字符串获取 "文本 VNode" 或者
  // 有插槽的对象。
  //
  // 可选的。
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```
## 插件

插件是自包含的代码，通常向 Vue 添加全局级功能。它可以是公开 install() 方法的 object，也可以是 function
它都会收到两个参数：由 Vue 的 createApp 生成的 app 对象和用户传入的选项。
插件还允许使用 inject 为插件的用户提供功能或 attribute。
另外，由于可以访问 app 对象，因此插件可以使用所有其他功能，例如使用 mixin 和 directive

```js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = (key) => {
      return key.split('.')
        .reduce((o, i) => { if (o) return o[i] }, options)
    }

    app.provide('i18n', options)

    app.directive('my-directive', {
      mounted (el, binding, vnode, oldVnode) {
        // some logic ...
      }
      ...
    })

    app.mixin({
      created() {
        // some logic ...
      }
      ...
    })
  }
}
```
在使用 createApp() 初始化 Vue 应用程序后，通过调用 use() 方法将插件添加到应用程序中。

## 组合式API

将同一个逻辑关注点相关代码收集在一起

### setup

setup 选项在组件创建之前执行，一旦 props 被解析，就将作为组合式 API 的入口。

setup 中应该避免使用 this，因为它不会找到组件实例。

setup 选项是一个接收 props 和 context 的函数，setup 返回的所有内容都暴露给组件的其余部分 (计算属性、方法、生命周期钩子等等) 以及组件的模板。

```js
export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: {
      type: String,
      required: true
    }
  },
  setup(props) {
    console.log(props) // { user: '' }

    return {} // 这里返回的任何内容都可以用于组件的其余部分
  }
  // 组件的“其余部分”
}
```
steup()将接收两个参数：props,context

props 是响应式的，当传入新的 prop 时，它将被更新,不能使用 ES6 解构，它会消除 prop 的响应性

```js
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

如果需要解构 prop，可以在 setup 函数中使用 toRefs 函数来完成此操作

```js
import { toRefs } from 'vue'
setup(props) {
  const { title } = toRefs(props)

  console.log(title.value)
}
```

如果 title 是可选的 prop，则传入的 props 中可能没有 title 。在这种情况下，toRefs 将不会为 title 创建一个 ref 。需要使用 toRef 替代：

```js
import { toRef } from 'vue'
setup(props) {
  const title = toRef(props, 'title')
  console.log(title.value)
}
```

context 是一个普通 JavaScript 对象，暴露了其它可能在 setup 中有用的值

```js
export default {
  setup(props, context) {
    // Attribute (非响应式对象，等同于 $attrs)
    console.log(context.attrs)

    // 插槽 (非响应式对象，等同于 $slots)
    console.log(context.slots)

    // 触发事件 (方法，等同于 $emit)
    console.log(context.emit)

    // 暴露公共 property (函数)
    console.log(context.expose)
  }
}

// 解构

export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

执行 setup 时，组件实例尚未被创建。因此，只能访问以下 property：
props
attrs
slots
emit

换句话说，你将无法访问以下组件选项：

data
computed
methods
refs  模板 ref DOM引用


结合模板使用

```vue
<template>
  <div>{{ collectionName }}: {{ readersNumber }} {{ book.title }}</div>
</template>

<script>
  import { ref, reactive } from 'vue'

  export default {
    props: {
      collectionName: String
    },
    setup(props) {
      const readersNumber = ref(0)
      const book = reactive({ title: 'Vue 3 Guide' })

      // 暴露给 template
      return {
        readersNumber,
        book
      }
    }
  }
</script>
```
通过调用 expose ，给它传递一个对象，其中定义的 property 将可以被外部组件实例访问

```js
import { h, ref } from 'vue'
export default {
  setup(props, { expose }) {
    const count = ref(0)
    const increment = () => ++count.value

    expose({
      increment
    })

    return () => h('div', count.value)
  }
}
// 这个 increment 方法现在将可以通过父组件的模板 ref 访问
```

setup 还可以返回一个渲染函数 h，该函数可以直接使用在同一作用域中声明的响应式状态

```js
import { h, ref, reactive } from 'vue'

export default {
  setup() {
    const readersNumber = ref(0)
    const book = reactive({ title: 'Vue 3 Guide' })
    // 请注意这里我们需要显式使用 ref 的 value
    return () => h('div', [readersNumber.value, book.title])
  }
}
```

###  响应性

react 的 Hook概念  不编写 class 的情况下使用 state 以及其他的 React 特性。

Vue 3.0 中，可以通过 响应性API 创建响应性变量，响应式计算属性，响应式侦听器等

**对象创建响应式状态**
为 JavaScript 对象创建响应式状态，可以使用 reactive，该 API 返回一个响应式的对象状态。该响应式转换是“深度转换”——它会影响传递对象的所有嵌套 property。

```js
import { reactive } from 'vue'

// 响应式状态
const state = reactive({
  count: 0
})
```
**创建独立的响应式值**
ref 会返回一个可变的响应式对象，该对象作为一个响应式的引用维护着它内部的值，

```js
import { ref } from 'vue'

const count = ref(0)
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```
当 ref 作为渲染上下文 (从 setup() 中返回的对象) 上的 property 返回并可以在模板中被访问时，它将自动浅层次解包内部值。只有访问嵌套的 ref 时需要在模板中添加 .value

**响应式状态解构**

需要将响应式对象转换为一组 ref

```js
import { reactive, toRefs } from 'vue'

const book = reactive({
  author: 'Vue Team',
  year: '2020',
  title: 'Vue 3 Guide',
  description: 'You are reading this book right now ;)',
  price: 'free'
})

let { author, title } = toRefs(book)

title.value = 'Vue 3 Detailed Guide' // 我们需要使用 .value 作为标题，现在是 ref
console.log(book.title) // 'Vue 3 Detailed Guide'
```

**readonly 防止更改响应式对象**

```js
import { reactive, readonly } from 'vue'

const original = reactive({ count: 0 })

const copy = readonly(original)

// 通过 original 修改 count，将会触发依赖 copy 的侦听器

original.count++

// 通过 copy 修改 count，将导致失败并出现警告
copy.count++ // 警告: "Set operation on key 'count' failed: target is readonly."
```

**计算值**
使用 computed 函数：它接受 getter 函数并为 getter 返回的值返回一个不可变的响应式 ref 对象。

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // error
// 或者，使用一个带有 get 和 set 函数的对象来创建一个可写的 ref 对象。
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```

**watchEffect**

为了根据响应式状态 自动应用和重新应用 副作用，使用 watchEffect 函数。它立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。

当 watchEffect 在组件的 setup() 函数或生命周期钩子被调用时，侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止。


有时副作用函数会执行一些异步的副作用，这些响应需要在其失效时清除 (即完成之前状态已改变了) 。所以侦听副作用传入的函数可以接收一个 onInvalidate 函数作入参，用来注册清理失效时的回调。当以下情况发生时，这个失效回调会被触发：

* 副作用即将重新执行时
* 侦听器被停止 (如果在 setup() 或生命周期钩子函数中使用了 watchEffect，则在组件卸载时)

```js
const data = ref(null)
watchEffect(async onInvalidate => {
  onInvalidate(() => {
    /* ... */
  }) // 在Promise解析之前注册清除函数
  data.value = await fetchData(props.id)
})
```

Vue 的响应性系统会缓存副作用函数，并异步地刷新它们，这样可以避免同一个“tick” 中多个状态改变导致的不必要的重复调用

```vue
<template>
  <div>{{ count }}</div>
</template>

<script>
export default {
  setup() {
    const count = ref(0)
    watchEffect(() => {
      console.log(count.value)
    })
    return {
      count
    }
  }
}
</script>
```

如果需要在组件更新(当与模板引用一起)后重新运行侦听器副作用，我们可以传递带有 flush 选项的附加 options 对象 (默认为 'pre')：

**watch**

watch 需要侦听特定的数据源，并在回调函数中执行副作用。默认情况下，它也是惰性的，即只有当被侦听的源发生变化时才执行回调

与 watchEffect 比较，watch 允许：

* 懒执行副作用；
* 更具体地说明什么状态应该触发侦听器重新运行；
* 访问侦听状态变化前后的值

侦听单个数据源

侦听器数据源可以是返回值的 getter 函数，也可以直接是 ref

```js
// 侦听一个 getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)
// 直接侦听ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```
侦听多个数据源

```js
const firstName = ref('')
const lastName = ref('')

watch([firstName, lastName], (newValues, prevValues) => {
  console.log(newValues, prevValues)
})

firstName.value = 'John' // logs: ["John", ""] ["", ""]
lastName.value = 'Smith' // logs: ["John", "Smith"] ["John", ""]
```

多个同步更改只会触发一次侦听器,可以用 nextTick 等待侦听器在下一步改变之前运行

侦听响应式对象
使用侦听器来比较一个数组或对象的值，这些值是响应式的，要求它有一个由值构成的副本。

```js
const numbers = reactive([1, 2, 3, 4])

watch(
  () => [...numbers],
  (numbers, prevNumbers) => {
    console.log(numbers, prevNumbers)
  }
)

numbers.push(5) // logs: [1,2,3,4,5] [1,2,3,4]
```
尝试检查深度嵌套对象或数组中的 property 变化时，仍然需要 deep 选项设置为 true

```js
const state = reactive({ 
  id: 1,
  attributes: { 
    name: '',
  }
})

watch(
  () => state,
  (state, prevState) => {
    console.log('not deep', state.attributes.name, prevState.attributes.name)
  }
)

watch(
  () => state,
  (state, prevState) => {
    console.log('deep', state.attributes.name, prevState.attributes.name)
  },
  { deep: true }
)

state.attributes.name = 'Alex' // 日志: "deep" "Alex" "Alex"
```

watch 与 watchEffect 在手动停止侦听、清除副作用 (将 onInvalidate 作为第三个参数传递给回调)、刷新时机和调试方面有相同的行为。


### 生命周期钩子

选项式 API	    Hook inside setup
beforeCreate	   Not needed*
created	         Not needed*
beforeMount	     onBeforeMount
mounted	         onMounted
beforeUpdate	   onBeforeUpdate
updated	         onUpdated
beforeUnmount	   onBeforeUnmount
unmounted	       onUnmounted
errorCaptured	   onErrorCaptured
renderTracked	   onRenderTracked
renderTriggered	 onRenderTriggered
activated	       onActivated
deactivated	     onDeactivated