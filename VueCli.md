vue cli 是一个基于vue.js进行快速开发的完整系统，提供：
* 通过`@vue/cli`实现的交互式的项目脚手架
* 实现零配置原型开发
* 运行时依赖@vue/cli-serviece，该依赖可升级，基于webpack构建，并带有合理的默认配置，可以通过项目内的配置文件进行配置，通过插件进行扩展
* 丰富的官方插件集合，集成前端生态中最好的工具
* 一套完全图形化的创建和管理vue.js项目的用户界面。

vue cli致力于将vue生态中的工具基础标准化，确保各种构建工具能够基于智能的默认配置即可平稳衔接。

安装  `npm install -g @vue/cli`或者`yarn global add @vue/cli`
检查版本 `vue --version`
升级 `npm update -g @vue/cli`或者`yarn global upgrade --latest @vue/cli`
创建一个项目 `vue create [项目名]`，提示选取一个preset，预置配置，可以选择默认包含了基本的Babel+ESLint设置的preset，也可以选手动选择特性来选取需要的特性。

可以通过vue ui命令以图形化界面创建和管理项目

运行服务 `npm run serve`或者`yarn serve`或者`npx vue-cli-service serve`


## html和静态资源

`public/index.html`文件是一个会被html-webpack-plugin处理的模板，构建过程中，资源链接会被自动注入。

插值，因为index文件被用作模板所以使用lodash template语法插入内容
* `<%= value %>`用来做不转义插值
* `<%- value %>`用来做HTML转义插值
* `<% expression %>` 用来描述 JavaScript 流程控制

preload `<link rel="preload">`是一种resource hint，用来指定页面加载轴很快会被用到的资源，在页面加载的过程中，希望浏览器开始主体渲染之前尽早preload。

默认情况下，一个Vue cli应用会为所有初始化渲染的文件自动生成preload提示。

prefetch `<link rel="prefetch">`是一种resource hint，用来告诉浏览器在页面加载完成后，利用空闲时间提前获取用户未来很能会访问的内容。
默认情况下，一个Vue cli应用会为所有作为async chunk生成的JavaScript文件自动生成prefetch提示

public文件夹
任何放置在public文件夹的静态资源都会被简单复制，而不经过webpack，需要通过绝对路径来引用它们。

推荐资源作为模块依赖图的一部分导入，这样会通过webpack的处理并获得：
* 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求
* 文件丢失会直接在编译时报错，而不是到了用户端才生成404错误。
* 最终生成的文件名包含了内容哈希，不必担心浏览器会缓存它们的老版本。
public目录提供的是一个应急手段，通过绝对路径引用时，留意应用将会部署到哪里，如果应用没有部署在域名的根部，那么需要为URL配置publicPath前缀


### webpack相关

调整webpack配置的方式是在`vue.config.js`中的`configureWebpack`选项中
```js
// vue.config.js
// 对象方式
module.exports = {
  configureWebpack: {
    plugins: [
      new someWebpackPlugin()
    ]
  }
}
//该对象将会被webpack-merge合并入最终的webpack配置
//基于环境有条件的配置，使用函数，该方法的一个参数会收到已经解析好的配置，函数内可以直接修改配置，或者返回一个将会被合并的对象

module.exports = {
  configureWebpack: config => {
    if(process.env.NODE_ENV === 'production') {
      //为生产环境修改配置
    } else {
      //为开发环境修改配置
    }
  }
}
```
### 链式操作

vue cli内部的webpack配置是通过webpack-chain维护的，这个库提供一个webpack原始配置的上层抽象，使其可以定义具名的loader规则和具名插件，并有机会在后期进入规则对它们的选项进行修改。 它允许更细粒度控制其内部配置。一些常见的在`vue.config.js`中`chainWebpack`修改的例子。
```js
//修改loader选项
module.exports = {
  chainWebpack: confgi => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap(options =>{
        //修改选项
        return options
      })
  }
}
```
### 审查项目的webpack配置

vue-cli-service暴露了inspect命令用于审查解析好的webpack配置。该命令会将解析出来的webpack配置，包括链式访问规则和插件的提示打印到stdout，可以将其输出重定向到一个文件以便进行查阅`vue inspect > output.js`

vue.config.js

vue.config.js是一个可选的配置文件，如果项目根目录中存在这个文件，那么它会被`@ve/li-service`自动加载。也可以使用packag.json中的vue字段，但是注意这种写法要严格遵循JSON格式。这个文件导出一个包含选项的对象。
```js
//vue.config.js
/**
 * @type {import('@vue/cli-service').ProjectOptions}
 */
module.exports = {
  // 选项...
}
```
publicPath type:string default: /
部署应用包时的基本URL，用法和webpack的output.publicPath一致，但是请始终使用publicPath而不要直接修改webpack的output.publicPath
publicPath的值跟应用部署的域名路径有关，一个例子
```js
module.exports = {
  publicPath: process.env.NODE_ENV === 'production'
    ? '/production-sub-path/'
    : '/'
}
```
outputDir type:string default:'dist'
当运行vue-cli-service build时生成的生产环境构建文件的目录

assetsDir type:string default: ''
放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录。

indexPath type:string default:'index.html'
指定生成的index.html的输出路径，也可以是一个绝对路径

filenameHashing type:string default:true
默认情况下，生成的静态资源在它们的文件名中包含了hash以便更好的控制缓存。

pages type:object default:undefined
在multi-page模式下构建应用，每个page应该有一个对应的JavaScript入口文件。其值是一个对象，对象的key是入口的名字，value是一个指定entry，template，filename，title，和chunks的对象。
```js
module.exports = {
  pages: {
    index: {
      // page 的入口
      entry: 'src/index/main.js',
      // 模板来源
      template: 'public/index.html',
      // 在 dist/index.html 的输出
      filename: 'index.html',
      // 当使用 title 选项时，
      // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
      title: 'Index Page',
      // 在这个页面中包含的块，默认情况下会包含
      // 提取出来的通用 chunk 和 vendor chunk。
      chunks: ['chunk-vendors', 'chunk-common', 'index']
    },
    // 当使用只有入口的字符串格式时，
    // 模板会被推导为 `public/subpage.html`
    // 并且如果找不到的话，就回退到 `public/index.html`。
    // 输出文件名会被推导为 `subpage.html`。
    subpage: 'src/subpage/main.js'
}
```

lintOnSave Type: boolean | 'warning' | 'default' | 'error'  default:'default'
是否在开发环境下通过eslint-loader在每次保存时lint代码，这个值在@vue/cli-plugin-eslint被安装后生效。
设置位true或者'warning'时，eslint-loader会将lint错误输出为编译警告。默认情况下，警告仅仅会被输出到命令行，且不会使得编译失败。'default'会将lint错误输出为编译错误，导致编译失败，'error'会使得lint警告也输出为编译错误，也将会导致编译失败
当lintOnSave是一个truthy的值是，eslint-loader在开发和生产构建下都会被启用，如果想要在生产构建是禁用eslint-loader

  // vue.config.js
     module.exports = {
      lintOnSave: process.env.NODE_ENV !== 'production'
    }

runtimeCompiler type:boolean default:false
是否使用包含运行时编译器的vue构建版本，设置为true后就可以在vue组建中使用template选项

productionSourceMap type:boolean default:true
如果不需要生产环境的source map，可以将其设置为false以加速生产环境构建。

crossorigin type:string default:undefined
设置生成的 HTML 中 `<link rel="stylesheet">` 和 `<script>` 标签的 crossorigin 属性。该选项仅影响由 html-webpack-plugin 在构建时注入的标签 - 直接写在模版 (public/index.html) 中的标签不受影响。

integrity type:boolean default:false
在生成的 HTML 中的 `<link rel="stylesheet">` 和 `<script>`标签上启用 Subresource Integrity (SRI)。如果你构建后的文件是部署在 CDN 上的，启用该选项可以提供额外的安全性。
需要注意的是该选项仅影响由 html-webpack-plugin 在构建时注入的标签 - 直接写在模版 (public/index.html) 中的标签不受影响。

configureWebpack type:object | function
如果这个值是一个对象，则会通过 webpack-merge 合并到最终的配置中。
如果这个值是一个函数，则会接收被解析的配置作为参数。该函数既可以修改配置并不返回任何东西，也可以返回一个被克隆或合并过的配置版本。

chainWebpack type:function
是一个函数，会接收一个基于 webpack-chain 的 ChainableConfig 实例。允许对内部的 webpack 配置进行更细粒度的修改。

devServer type:object  支持所有webpack-dev-serve选项

devServer.proxy type:string | object
如果前端应用和后端API服务器没有运行在同一个主机上，需要在开发环境中将API请求代理到API服务器。devServer.proxy可以是一个指向开发环境API服务器的字符串，也可以是一个对象，控制更多的代理行为

```js
module.exports = {
  devServer: {
    proxy: 'http://localhost:4000'
  }
}
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  }
}

Babel  可以通过babel.config.js进行配置

Eslint 可以通过 .eslintrc 或 package.json 中的 eslintConfig 字段来配置。

TypeScript 可以通过 tsconfig.json 来配置。


```
-----------------
```js
/**
 *
 * @param {object} matchedParam - 匹配的对象
 * @param {object} incomingParam - 传入的对象
 * @param {Boolean} match - 是否匹配
 * 数据处理：1、去掉空字段，2、补全参数
 */
export function dataProcessing(matchedParam, incomingParam, match) {
  if (match) {
    for (const key in matchedParam) {
      if (matchedParam.hasOwnProperty(key)) {
        const element = incomingParam[key]
        if (element) Object.assign(matchedParam, { [key]: element })
      }
    }
    return matchedParam
  } else {
    for (const key in incomingParam) {
      if (incomingParam.hasOwnProperty(key)) {
        const element = incomingParam[key]
        if (typeof (element) === 'object') {
          if (element.length === 0) delete incomingParam[key]
        } else {
          if (!element) delete incomingParam[key]
        }
      }
    }
    return incomingParam
  }
}
```


```js
// 单选
    handleOneSelect(selection, row) {
      // 当前点击元素是否勾选
      const isIn = selection.find((val) => {
        return val.id === row.id
      })
      // 勾选后添加至已选集合
      if (isIn) {
        for (const item of selection) {
          const itemIsIn = this.curSelect.find((val) => {
            return val.id === item.id
          })
          if (!itemIsIn) this.curSelect.push(item)
        }
      } else {
        // 勾选后去勾选
        this.curSelect.splice(
          this.curSelect.findIndex((val) => {
            return val.id === row.id
          }),
          1
        )
      }
    },
    // 全选
    handleAllSelect(selection) {
      if (selection.length > 0) {
        // 全选合并
        for (const item of this.curPageTableData) {
          const itemIsIn = this.curSelect.find((val) => {
            return val.id === item.id
          })
          if (!itemIsIn) this.curSelect.push(item)
        }
      } else {
        // 全选后取消全选
        for (const item of this.curPageTableData) {
          const index = this.curSelect.findIndex((val) => {
            return val.id === item.id
          })
          this.curSelect.splice(index, 1)
        }
      }
    },
    // 分页处理
    handleCurrentChange(val) {
      this.curPage = val
      // pagedata数据同步
      this.$nextTick(() => {
        // 显示勾选状态
        for (const item of this.curSelect) {
          const selectedRow = this.curPageTableData.find((val) => {
            return val.id === item.id
          })
          if (selectedRow) this.$refs.table.toggleRowSelection(selectedRow, true)
        }
      })
    },
```

组件化，工程化

工程化，路由，状态管理，多语言，axios请求封装api集中处理，协同开发，UI引入快速开发

组件化 项目分成大大小小组件，组件使用配置化

