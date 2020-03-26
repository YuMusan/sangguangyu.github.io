# 指南
### 安装
前提条件：在开始之前，请确保安装node.js的最新版本，使用旧版本，可能遇到各种问题，因为可能缺少webpack功能以及/或者缺少相关package包。
```js
//安装最新版本
npm install --save-dev webpack
//安装特定版本
npm install --save-dev webpack@<version>
//如果使用webpack4+版本，还需要安装CLI
npm install --save-dev webpack-cli

//最新体验版本
npm install webpack@beta
npm install webpack/webpack#<tagname/branchname>
```
对应大多数项目，建议本地安装，这可以在引入破坏式变更的依赖时，更容易分别升级项目。
安装最新体验版本要小心，它们可能仍然包含bug，因此不应该用于生产环境。

## 起步
#### 基本安装
首先创建一个目录，初始化npm，然后再本地安装webpack，接着安装webpac-cli
```js
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```
现在将创建以下目录结构、文件和内容：
`project`
```text
webpack-demo
  |- package.json
+ |- index.html
+ |- /src
+   |- index.js
```
`src/index.js`
```js
function component() {
  var element = document.createElement('div');

  // Lodash（目前通过一个 script 脚本引入）对于执行这一行是必需的
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  return element;
}

document.body.appendChild(component());
```
`index.html`
```html
<!doctype html>
<html>
  <head>
    <title>起步</title>
    <script src="https://unpkg.com/lodash@4.16.6"></script>
  </head>
  <body>
    <script src="./src/index.js"></script>
  </body>
</html>
```
调整`package.json`文件，确保安装包是私有的(private),并且移除main入口。这可以防止意外发布代码。
`package.json`
```text
{
    "name": "webpack-demo",
    "version": "1.0.0",
    "description": "",
+   "private": true,
-   "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "webpack": "^4.0.1",
      "webpack-cli": "^2.0.9"
    },
    "dependencies": {}
  }
```
到这步，示例中`<script>`标签之间存在隐式依赖关系。`index.js`文件执行之前，还依赖于页面中引入的loadash。之所以说是隐式的是因为`index.js`并未显式声明需要引入lodash，只是假定推测已经存在一个全局变量`_`.
使用这种方式去管理JavaScript项目会有一些问题。
* 无法立即体现，脚本的执行依赖于外部扩展库
* 如果依赖不存在，或者引入顺序错误，应用程序将无法正常运行。
* 如果依赖被引入但是并没有使用，浏览器将被迫下载无用代码。

这时，使用webpack来管理这些脚本。
### 创建一个bundle文件

>bundle是由多个不同的模块生成,bundles包含了已经过加载和编译的最终源文件版本

首先，稍微调整下目录结构，将源代码`/src`从分发代码`/dist`中分离出来。源代码是用于书写和编辑的代码。分发代码是构建过程产生的代码最小化和优化后的输出目录，最终将在浏览器中加载：
```text
webpack-demo
  |- package.json
+ |- /dist
+   |- index.html
- |- index.html
  |- /src
    |- index.js
```
要在index.js中打包lodash依赖，需要在本地安装library，依赖库。
> 在安装一个要打包到生产环境的安装包时，你应该使用 `npm install --save`，如果你在安装一个用于开发环境的安装包（例如，linter, 测试库等），你应该使用 `npm install --save-dev`

现在，在脚本中`import lodash`：
`src/index.js`
```js
+ import _ from 'lodash';
+
  function component() {
    var element = document.createElement('div');

-   // Lodash, currently included via a script, is required for this line to work
+   // Lodash, now imported by this script
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
```
现在，由于通过打包来合成脚本，必须更新`index.html`文件.因为现在是通过import引入lodash，所以将`loadash <script>`删除，然后修改另一个`<script>`标签来加载bundle，而不是原始的`/src`文件：
`dist/index.html`
```html
<!doctype html>
  <html>
   <head>
     <title>起步</title>
-    <script src="https://unpkg.com/lodash@4.16.6"></script>
   </head>
   <body>
-    <script src="./src/index.js"></script>
+    <script src="main.js"></script>
   </body>
  </html>
```
在这个设置中，`index.js`显示要求引入的lodash必须存在，然后将它绑定为`_`(没有全局作用域污染)。通过声明模块所需的依赖的依赖，webpack能够利用这些信息去构建依赖图，然后使用图生成一个优化过的，会议正确顺序执行的bundle。
可以这样说，执行`npx webpack`,会将脚本作为入口起点，然后输出为`main.js`.
```shell
npx webpack

Hash: dabab1bac2b940c1462b
Version: webpack 4.0.1
Time: 3003ms
Built at: 2018-2-26 22:42:11
    Asset      Size  Chunks             Chunk Names
main.js  69.6 KiB       0  [emitted]  main
Entrypoint main = main.js
   [1] (webpack)/buildin/module.js 519 bytes {0} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] ./src/index.js 256 bytes {0} [built]
    + 1 hidden module

WARNING in configuration(配置警告)
The 'mode' option has not been set. Set 'mode' option to 'development' or 'production' to enable defaults for this environment.('mode' 选项还未设置。将 'mode' 选项设置为 'development' 或 'production'，来启用环境默认值。)
```
在浏览器打开`index.html`,如果一切访问正常，可以看到以下文本：'Hello webpack'

### 使用配置文件
大多数项目需要很复杂的设置，使用配置文件设置，比手动输入大量命令要高效的多。
`project`
```text
webpack-demo
  |- package.json
+ |- webpack.config.js
  |- /dist
    |- index.html
  |- /src
    |- index.js
```
`webpack.config.js`
```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```
通过新配置文件再次执行构建：
`npx webpack --config webpack.config.js`
配置文件具有更多的灵活性，通过配置方式指定loader规则、插件plugins、解析选项resolve options，以及其他增强功能。

#### npm脚本
考虑到用CLI方式来运行本地的webpack不方便，可以设置一个快捷方式。在package.json中添加一个npm脚本。
`package.json`
```js
{
    "name": "webpack-demo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
+     "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "webpack": "^4.0.1",
      "webpack-cli": "^2.0.9",
      "lodash": "^4.17.5"
    }
  }
```
现在，可以使用`npm run build`命令来替代之前使用的npx命令。
```shell
npm run build

Hash: dabab1bac2b940c1462b
Version: webpack 4.0.1
Time: 323ms
Built at: 2018-2-26 22:50:25
    Asset      Size  Chunks             Chunk Names
bundle.js  69.6 KiB       0  [emitted]  main
Entrypoint main = bundle.js
   [1] (webpack)/buildin/module.js 519 bytes {0} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] ./src/index.js 256 bytes {0} [built]
    + 1 hidden module

WARNING in configuration(配置警告)
The 'mode' option has not been set. Set 'mode' option to 'development' or 'production' to enable defaults for this environment.('mode' 选项还未设置。将 'mode' 选项设置为 'development' 或 'production'，来启用环境默认值。)
```
此刻，已经实现了一个基本的构建过程。项目结构应该和如下类似
`project`
```text
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
  |- bundle.js
  |- index.html
|- /src
  |- index.js
|- /node_modules
```
## 管理资源
