# webpack
### 概念
本质上，webpack是一个现代JavaScript应用程序的静态模块打包器(module bundler)。当webpack处理应用程序时，它会递归的构建一个依赖关系图(dependency graph),其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个bundle。
理解四个核心概念：
* 人口entry
* 输出output
* loader
* 插件plugins
### 入口entry
入口起点entry point指示webpack应该使用哪个模块，来作为构建其内部依赖图的开始。
进入入口起点后,webpack会找出有哪些模块和库是入口起点依赖的(直接的和间接的)
每个依赖项随机被处理，最后输出到称之为bundles的文件中。通过在webpack配置中配置entry属性，来指定一个入口起点(或多个入口起点)。默认值为`./src`
`webpack.config.js`
```js
//entry配置的最简单例子
module.exports = {
	entry: './path/to/my/entry/file.js'
}
```
### 出口output
output属性告诉webpack在哪里输出它所创建的bundles，以及如何命名这些文件，默认值为`./dist`。
基本上，整个个应用程序结构，都会别编译到指定的输出路径的文件夹中。通过在配置中制定一个output字段，来配置这些处理过程。
`webpack.config.js`
```js
const path = require('path')
module.exports = {
	entry: './path/to/my/entry/file.js'
	output: {
		path: path.resolve(__dirname,'dist'),
		filename: 'my-first-webpack.bundle.js'	
	}
}
```
在上面的示例中，通过`output.filename and output.path`属性，来告诉webpack bundle的名称，已经想要bundle生成(emit)到哪里.
### loader
loader让webpack能够处理那些非JavaScript文件。loader可以将所有类型文件转换为webpack能够处理的有效模块，然后利用webpack的打包能力，进行处理。
本质上，webpack loader将所有类型的文件，转换为应用程序的依赖图(和最终的bundle)可以直接引用的模块。

>loader能够import导入任何类型的模块，这是webpack特有的功能，其他打包程序或任务执行器可能不支持。

在更高层面，webpack配置中loader有两个目标：
1. test属性，用于标识出应该被对应的loader进行转换的某个或某些文件。
2. use属性，标识进行转换时，应该使用哪个loader

`webpack.config.js`
```js
const path = require('path');
const config = {
	output: {
		filename: 'my-first-webpack.bundle.js'
	},
	module: {
		rules: [
			{test: /\.txt$/, use: 'raw-loader'}
		]
	}
};
module.exports =  config;
```
以上配置中，对一个单独的module对象定义了rules属性，里面包含两个必须属性：test和use，这告诉webpack编译器(compiler)如下信息：
>嘿，webpack编译器，当你碰到*在`require()/import`*语句中被解析为'.txt'的路径时，在你对它打包前，先使用`raw-loader`转换一下。

### 插件plugins
loader被用于转换某些类型的模块，而插件则可以用用执行分为更广的任务。
插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。
插件接口功能极其强大，可以用来处理各种各样的任务。
想要使用一个插件，只需要require()它，然后把它添加到plugins数组中。
多数插件可以通过选项option自定义。也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过new操作符来创建它的一个实例。
`webpack.config.js`
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');//通过npm安装
const webpack = require('webpack');//用于访问内置插件

const config = {
	module: {
		rules: [
			{test: /\.txt$/, use: 'raw-loader'}
		]
	},
	plugins: [
		new HtmlWebpackPlugin({template: './src/index.html'})
	]
};
module.exports = config;
```
### 模式
通过选择development或production之中的一个，来设置mode参数，可以启用相应模式下的webpack内置的优化。
`module.exports = {mode: 'production'};
`
### 入口起点[entry points]
##### 单个入口语法
用法`entry: string|Array<string>`
`webpack.config.js`
```js
const config = {
	entry: './path/to/my/entry/file.js'
}
module.exports = config;
```
entry属性的单个入口语法，是下面的简写：
```js
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```
>向entry传入一个数组时会发生什么？
向entry属性传入[文件路径数组]将创建'多个主入口'。
如果想要多个依赖文件一起注入，并且将他们的依赖graph导向到一个chunk时，传入数组的方式就很有用。

##### 对象语法
用法：`entry:{[entryChunkName: string]: string|Arry<string>}`
`webpack.config.js`
```js
const config = {
	entry: {
		app: './src/app.js',
		//第三方库入口
		verndors: './src/vendors.js'
	}
}
module.exports = config;
```
>可扩展webpack配置是指，可重用并且可以与其他配置组合使用。
这用于将关注点concern从环境environment、构建目标build target、运行时runtime中分离。
然后用专门的工具webpack-merge将它们合并。

##### 常见场景
**分离应用程序APP和第三方库vendor入口**
```js
const config = {
	entry: {
		app: './src/app.js',
		//第三方库入口
		verndors: './src/vendors.js'
	}
}
module.exports = config;
```
*这是什么？* 从表面上，这告诉我们webpack从app.js和vendor.js开始创建依赖图。
这些依赖图是彼此完全分离互相独立的。这种方式比较常见于，只有一个入口起点(不包括verdor)的单页应用程序中。
此设置允许你使用 CommonsChunkPlugin 从「应用程序 bundle」中提取 vendor 引用(vendor reference) 到 vendor bundle，
并把引用 vendor 的部分替换为 `__webpack_require__()` 调用。如果应用程序 bundle 中没有 vendor 代码，
那么你可以在 webpack 中实现被称为长效缓存的通用模式。
**多页面应用程序**
`webpack.config.js`
```js
const config = {
	entry: {
		pageOne: './src/pageOne/index.js',
		pageTwo: './src/pageTwo/index.js',
		pageThree: './src/pageThree/index.js',
	}
}
```
在多页应用中，（每当页面跳转时）服务器将为你获取一个新的 HTML 文档。页面重新加载新文档，并且资源被重新下载。
然而，这给了我们特殊的机会去做很多事：使用CommonsChunkPlugin为每个页面间的应用程序共享代码创建bundle。
由于入口起点增多，多页应用能够复用入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。
### 输出output
配置output选项可以控制webpack如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置。
##### 用法usage
在webpack中配置output属性的最低要求是，将它的值设置为一个对象，包括以下两点：
* filename用于输出文件的文件名
* 目标输出目录path的绝对路径

```js
const config = {
	output: {
		filename: 'bundle.js',
		path: '/home/proj/public/assets'
	}
}
```

此配置将一个单独的bundle.js文件输出到`/home/proj/public/assets`目录中。
##### 多个入口起点
如果配置创建了多个单独的chunk(例如,使用多个入口起点或使用像commonsChunPlugin这样的插件),则应该使用占位符substitutions来确保每个文件具有唯一的名称。
```js
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}
// 写入到硬盘：./dist/app.js, ./dist/search.js
````
##### 高级进阶
以下是使用CDN和资源hash的复杂示例：
```js
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```
在编译时不知道最终输出文件的 publicPath 的情况下，publicPath 可以留空，并且在入口起点文件运行时动态设置。如果你在编译时不知道 publicPath，你可以先忽略它，并且在入口起点设置`__webpack_public_path__`
```js
__webpack_public_path__ = myRuntimePublicPath

// 剩余的应用程序入口
```
##### 模式mode
提供mode配置选项，告知webpack使用相应模式的内置优化。	
只在配置中提供mode选项：
`module.exports = {
  mode: 'production'
};
`
或者从 CLI 参数中传递：`webpack --mode=production`
支持以下字符串值：
* development : 会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。
* production : 会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin.

```js
// webpack.development.config.js
module.exports = {
+ mode: 'development'
- plugins: [
-   new webpack.NamedModulesPlugin(),
-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
- ]
}
```
```js
// webpack.production.config.js
module.exports = {
+  mode: 'production',
-  plugins: [
-    new UglifyJsPlugin(/* ... */),
-    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
-    new webpack.optimize.ModuleConcatenationPlugin(),
-    new webpack.NoEmitOnErrorsPlugin()
-  ]
}
```
### loader
loader用于对模块的源代码进行转换。loader可以让你在import或加载模块时预处理文件。因此loader类似于其他构建工具中'任务'，并提供了处理前端构建步骤的强大方法。loader可以将文件从不同的语言(如Typescript)转换为JavaScript，或将内联图像转换为data URL。loader甚至允许直接在JavaScript模块中import CSS文件！
##### 示例
例如，使用loader告诉webpack加载css文件，或者将typescript转为JavaScript。首先安装相对应的loader。

    npm install --save-dev css-loader
    npm install --save-dev ts-loader

然后指示webpack对每个.css使用`css-loader`,以及对所有.ts文件使用`ts-loader`:
```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' }
    ]
  }
};
```
##### 使用loader
在应用程序中，有三种使用loader的方式。
* 配置：在webpack.config.js文件中指定loader。
* 内联：在每个import语句中显式指定loader。
* CLI ：在shell命令中指定它们。
##### 配置configuration
`moudle.rules`允许在webpack配置中指定多个loader。这是展示loader的一种简明方式，并且有助于使代码变得简洁。同事对各个loader有个全局概览：
```js
module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
```

##### 内联
可以在import语句或任何等效于import的方式中指定loader。使用!将资源中的loader分开。分开的每个部分都相对于当前目录解析。
`import Styles from 'style-loader!css-loader?modules!./styles.css';`
通过前置所有规则及使用!，可以对应覆盖到配置中的任意loader。
选项可以传递查询参数，例如`?key=value&foo=bar`,或者一个JSON对象，例如`?{"key":"value","foo":"bar"}`

##### CLI
也可以通过CLI使用loader：`webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'`
这会对 .jade 文件使用 jade-loader，对 .css 文件使用 style-loader 和 css-loader。

##### loader特性
* loader支持链式传递，能够对资源使用流水线(pipeline).一组链式的loader将按照相反的顺序执行。loader链中的第一个loader返回值给下一个loader。在最后一个loader，返回webpack所预期的JavaScript。
* loader可以是同步的，也可以是异步的。
* loader运行在node.js中，并且能够执行任何可能的操作。
* loader接受查询参数，用于对loader传递配置。
* loader也能够使用option对象进行配置。
* 除了使用`package.json`常见的main属性，开可以将普通的npm模块导出为loader，做法是在`package.json`里定义一个loader字段。
* 插件plugin可以为loader带来更多特性。
* loader能够产生额外的任意文件。

loader通过预处理函数，为JavaScript生态系统提供更多能力。可以更加灵活地引入细粒度逻辑，例如压缩、打包、语言翻译和其他。
##### 解析loader
loader遵循标准的模块解析。多数情况下，loader将从模块路径(通常将模块路径认为是npm install,node_modules)解析。
loader模块需要导出为一个函数，并且使用node.js兼容的JavaScript缩写。通常使用npm进行管理，但是也可以将自定义loader作为应用程序中的文件。按照约定，loader通常被命名为`xxx-loader`(例如json-loader)
### 插件plugins
插件是webpack的支柱功能。webpack自身也是构建与，在webpack配置中用到的相同的插件系统之上，插件的目的在于解决loader无法实现的其他事。
##### 剖析
webpack插件是一个具有apply属性的JavaScript对象。apply属性会被webpack compiler调用，并且compiler对象可在整个编译生命周期访问。
```js
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';
class ConsoleLogOnBuildWebpackPlugin {
	apply(compiler) {
		compiler.hooks.run.tap(pluginName,compilation => {
			console.log("webpack构建过程开始！")
		})
	}
}
```
compiler hook的tap方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有hook中复用。
##### 配置
`webpack.config.js`
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');
const path = require('path');

const config = {
	entry: './path/to/my/entry/file.js'
	output: {
		filename: 'my-first-webpack.bundle.js',
		path: path.resolve(__dirname,'dist')
	},
	moudle: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				use: 'babel-loader'
			}
		]
	},
	plugins: [
		new webpack.optimize.UglifyJsPlugin(),
		new HtmlWebpackPlugin({template: './src/index.html'})
	]
};

moudle.exports = config;
```
##### 配置configuration
应该注意到，很少有webpack配置看起来完全相同。这是因为webpack的配置文件，是导出一个对象的JavaScript文件。此对象，由webpack根据对象定义的属性进行解析；
