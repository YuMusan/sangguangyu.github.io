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
现在尝试整合一些其他资源，比如图像。接着上个示例来。
#### 加载CSS
为了从JavaScript模块中，import一个css文件,需要在module配置中安装并添加style-loader和css-loader:
`npm install --save-dev style-loader css-loader`
`webpack.config.js`
```js
const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
+   module: {
+     rules: [
+       {
+         test: /\.css$/,
+         use: [
+           'style-loader',
+           'css-loader'
+         ]
+       }
+     ]
+   }
  };
```
>webpack根据正则表达式，来确定应该查找哪些文件，并将其提供给指定的loader。在这种情况下，以`.css`结尾的全部文件，都将被提供给`style-loader`和`css-loader`.

这样可以在依赖于此样式的文件中`import './style.css'`.现在，当该模块运行时，含有css字符串的`<style>`标签，将被插入到html文件的`<head>`中。现在尝试一下，通过在项目中添加新的`style.css`文件，并将其导入到`index.js`中。
`project`
```text
webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
    |- bundle.js
    |- index.html
  |- /src
+   |- style.css
    |- index.js
  |- /node_modules
```

`src/style.css`

`.hello {
  color: red;
}`

`src/index.js`

```js
import _ from 'lodash';
+ import './style.css';

  function component() {
    var element = document.createElement('div');

    // lodash 是由当前 script 脚本 import 导入进来的
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.classList.add('hello');

    return element;
  }

  document.body.appendChild(component());
```
现在运行构建命令`npm run build`,浏览器中打开`index.html`，看到字体样式红色。
#### 加载图片
使用file-loader，可以轻松将图片混合到css中。
`npm install --save-dev file-loader`
`webpack.config.js`
```js
const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ]
        },
+       {
+         test: /\.(png|svg|jpg|gif)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```
现在，当`import MyImage from './my-image.png'`，该图像将被处理并添加到 output 目录，_并且_ MyImage 变量将包含该图像在处理后的最终url。
向项目添加一个图像，可以使用任何你喜欢的图像。
`project`
```text
 webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
    |- bundle.js
    |- index.html
  |- /src
+   |- icon.png
    |- style.css
    |- index.js
  |- /node_modules
```
`src/index.js`
```js
import _ from 'lodash';
  import './style.css';
+ import Icon from './icon.png';

  function component() {
    var element = document.createElement('div');

    // Lodash，现在由此脚本导入
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
    element.classList.add('hello');

+   // 将图像添加到现有的 div。
+   var myIcon = new Image();
+   myIcon.src = Icon;
+
+   element.appendChild(myIcon);

    return element;
  }

  document.body.appendChild(component());
```
`src/style.css`
``` css
.hello {
    color: red;
+   background: url('./icon.png');
  }
```
现在重新构建，并再次打开index.html文件，可以看到背景图片以及文本胖旁有张图片
>压缩和优化图像，查看image-webpack-loader和url-loader，以了解增强加载处理图片功能。
#### 加载字体
那么，像字体这样的其他资源如何处理，file-loader和url-loader可以接受并加载任何文件，然后将其输出到构建目录。这就是说，可以将它们用于任何类型的文件，包括字体。
`webpack.config.js`
```js
const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    //加载资源
    module: {
      rules: [
      	//css文件
        {
          test: /\.css$/,
          use: [
            'style-loader',
            'css-loader'
          ]
        },
        //加载图片
        {
          test: /\.(png|svg|jpg|gif)$/,
          use: [
            'file-loader'
          ]
        },
        //加载字体
+       {
+         test: /\.(woff|woff2|eot|ttf|otf)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```
在项目中添加一些字体文件
```text
webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
    |- bundle.js
    |- index.html
  |- /src
+   |- my-font.woff
+   |- my-font.woff2
    |- icon.png
    |- style.css
    |- index.js
  |- /node_modules
```
通过配置好 loader 并将字体文件放在合适的地方，可以通过一个@font-face声明引入。本地的url(...)指令会被webpack获取处理，就像它处理图片资源一样：
```css
+ @font-face {
+   font-family: 'MyFont';
+   src:  url('./my-font.woff2') format('woff2'),
+         url('./my-font.woff') format('woff');
+   font-weight: 600;
+   font-style: normal;
+ }

  .hello {
    color: red;
+   font-family: 'MyFont';
    background: url('./icon.png');
  }
```
现在重新构建，看看是否处理字体。如果一切顺利，应该能看到变化。

#### 回退处理
进行清理工作，为下一部分的指南做好准备。
```text
webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
    |- bundle.js
    |- index.html
  |- /src
-   |- data.xml
-   |- my-font.woff
-   |- my-font.woff2
-   |- icon.png
-   |- style.css
    |- index.js
  |- /node_modules
```
```js
const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
-   module: {
-     rules: [
-       {
-         test: /\.css$/,
-         use: [
-           'style-loader',
-           'css-loader'
-         ]
-       },
-       {
-         test: /\.(png|svg|jpg|gif)$/,
-         use: [
-           'file-loader'
-         ]
-       },
-       {
-         test: /\.(woff|woff2|eot|ttf|otf)$/,
-         use: [
-           'file-loader'
-         ]
-       },
-     ]
-   }
  };
```
```js
import _ from 'lodash';
- import './style.css';
- import Icon from './icon.png';
-
  function component() {
    var element = document.createElement('div');
-
-   // Lodash，现在通过 script 标签导入
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
-   element.classList.add('hello');
-
-   // 将图像添加到我们已有的 div 中。
-   var myIcon = new Image();
-   myIcon.src = Icon;
-
-   element.appendChild(myIcon);

    return element;
  }

  document.body.appendChild(component());
```
## 管理输出
到目前为止，在index.html文件中手动引入所有资源，然而随着应用程序增长，并且一旦开始对文件名使用hash并输出多个bundle，手动管理会变得困难起来。可以通过一些插件，会使过程更容易操控。
#### 预先准备
```text
 webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
  |- /src
    |- index.js
+   |- print.js
  |- /node_modules
```
在`src/print.js`中添加一些逻辑
```js
export default function printMe() {
  console.log('I get called from print.js!');
}
```
并且在`src/index.js`文件中使用这个函数：
```js
import _ from 'lodash';
+ import printMe from './print.js';

  function component() {
    var element = document.createElement('div');
+   var btn = document.createElement('button');

    element.innerHTML = _.join(['Hello', 'webpack'], ' ');

+   btn.innerHTML = 'Click me and check the console!';
+   btn.onclick = printMe;
+
+   element.appendChild(btn);

    return element;
  }

  document.body.appendChild(component());
```
还要更新`dist/index.html`文件，来为webpack分离入口做好准备：
```html
<!doctype html>
  <html>
    <head>
-     <title>Asset Management</title>
+     <title>Output Management</title>
+     <script src="./print.bundle.js"></script>
    </head>
    <body>
-     <script src="./bundle.js"></script>
+     <script src="./app.bundle.js"></script>
    </body>
  </html>
```
现在调整配置，在entry添加`src/print.js`作为新的入口起点print，然后修改 output,以便根据入口起点名称动态生成bundle名称：
```js
const path = require('path');

  module.exports = {
-   entry: './src/index.js',
+   entry: {
+     app: './src/index.js',
+     print: './src/print.js'
+   },
    output: {
-     filename: 'bundle.js',
+     filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
``` 
可以看到在dist文件夹中生成print.bundle.js和app.bundle.js文件，这和在index.html文件中指定的文件名称相对应，在浏览器打开index.html，就可以看到在点击按钮时会发生什么。
但是，如果更改了一个入口起点的名称，甚至添加了一个新的名称，会发生什么？生成的包将被重命名在一个构建中，但是index.html文件仍然会引用旧的名字。这时用`HtmlWebpackPlugin`来解决这个问题。
### 设定HtmlWebpackPlugin
首先安装插件，并且调整`webpack.config.js`:
`npm install --save-dev html-webpack-plugin`
`webpack.config.js`
```js
const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
+   plugins: [
+     new HtmlWebpackPlugin({
+       title: 'Output Management'
+     })
+   ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
`HtmlWebpackPlugin`会默认生成`index.html`文件。这样新生成的index.html会将原来的替换掉，所有的bundle会自动添加到html 中。想要了解更多 HtmlWebpackPlugin 插件提供的全部功能和选项，去[HtmlWebpackPlugin](https://github.com/jantimon/html-webpack-plugin)仓库。

### 清理/dist文件夹
在每次构建前清理/dist文件夹，清理旧的文件，只会生成用到的文件，推荐使用`clean-webpakc-plugin`，是一个比较普及的管理插件。
`npm install clean-webpack-plugin --save-dev`

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            title: 'Output Management'
        })
    ],
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```

现在执行`npm run build`，再检查/dist 文件夹。应该不会再看到旧的文件，只有构建后生成的文件！

### manifest
webpack及其插件似乎“知道”应该哪些文件生成。答案是，通过 manifest，webpack 能够对「你的模块映射到输出 bundle 的过程」保持追踪。如果对通过其他方式来管理 webpack 的输出更感兴趣，那么首先了解 manifest 是个好的开始。

## 开发

建立一个开发环境，使开发变得更容易些。

#### 使用source map

当webpack打包源代码是，可能会很难追踪到错误和警告在源代码中的原始位置。为了更容易的追踪错误和警告，JavaScript提供了source map功能，将编译后的代码映射回原始源代码。
对于本指南，使用`inline-source-map`选项，仅解释说明，不要用于生产环境。

```js
const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
+   devtool: 'inline-source-map',
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Development'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
[source map](https://www.webpackjs.com/configuration/devtool)有很多不同的选项可用。但是此时，若编译后产生错误，会指出错误源在哪里。


>每次要编译代码时，手动运行`npm run build`会变得很麻烦。
webpac中有几个不同的选项，可以帮助在代码发生变化后自动编译代码。
1. webpack's Watch Mode
2. webpack-dev-server
3. webpack-dev-middleware

#### 使用观察模式
可以指示webpack'watch'依赖图中的所有文件以进行更改。如果其中一个文件被更新，代码将被重新编译，不必手动运行整个构建。
添加一个用于启动webpack的观察模式的npm script脚本：
```js
// package.json
{
    "name": "development",
    "version": "1.0.0",
    "description": "",
    "main": "webpack.config.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
+     "watch": "webpack --watch",
      "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "clean-webpack-plugin": "^0.1.16",
      "css-loader": "^0.28.4",
      "csv-loader": "^2.1.1",
      "file-loader": "^0.11.2",
      "html-webpack-plugin": "^2.29.0",
      "style-loader": "^0.18.2",
      "webpack": "^3.0.0",
      "xml-loader": "^1.2.1"
    }
  }
```
现在在命令行中运行`npm run watch`，就会看到webpack编译代码，然而却不会退出命令行。这是因为script脚本还在观察文件。
现在，webpack观察文件的同时，更改console的内容：
```js
export default function printMe() {
-   cosnole.log('I get called from print.js!');
+   console.log('I get called from print.js!');
}
```
保存文件并检查终端窗口，可以看到webpack自动重新编译修改后的模块。唯一的缺点是，为了看到修改后的实际效果，需要刷新浏览器。而`webpack-dev-server`恰巧可以实现这样的功能。
### 使用webpack-dev-server
`webpack-dev-server`提供了一个简单的web服务器，并且能实时重新加载(live reloading). `npm install --save-dev webpack-dev-server`  
修改配置文件，告诉开发服务器dev server，在哪里查找文件。
```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    devtool: 'inline-source-map',
+   devServer: {
+     contentBase: './dist'
+   },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Development'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
以上配置告知`webpack-dev-server`，在`localhost:8080`下建立服务，将dist目录下的文件，作为可访问文件。添加一个script脚本，可以直接运行开发服务器dev server。
```js
{
    "name": "development",
    "version": "1.0.0",
    "description": "",
    "main": "webpack.config.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "watch": "webpack --watch",
+     "start": "webpack-dev-server --open",
      "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "clean-webpack-plugin": "^0.1.16",
      "css-loader": "^0.28.4",
      "csv-loader": "^2.1.1",
      "file-loader": "^0.11.2",
      "html-webpack-plugin": "^2.29.0",
      "style-loader": "^0.18.2",
      "webpack": "^3.0.0",
      "xml-loader": "^1.2.1"
    }
  }
```
#### webpack-dev-middleware
`webpack-dev-middleware`是一个容器wrapper，它可以把webpack处理后的文件传递给一个服务器server。webpack-dev-server在内部使用了它。它也可以作为一个单独的包来使用，以便进行更多自定义设置来实现更多的需求。下面是一个webpack-dev-middleware配合express server的示例。
`npm install --save-dev express webpack-dev-middleware`
接下来对webpack的配置做一些调整，确保中间件功能能够正确启用：
```js
const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    devtool: 'inline-source-map',
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist'),
+     publicPath: '/'
    }
  };
```
publicPath会在服务器脚本中用到，以确保文件资源能够在`http://localhost:3000`下正确访问，下一步设置自定义的express服务：
```text
 webpack-demo
  |- package.json
  |- webpack.config.js
+ |- server.js
  |- /dist
  |- /src
    |- index.js
    |- print.js
  |- /node_modules
```

`server.js`

```js
const express = require('express');
const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');

const app = express();
const config = require('./webpack.config.js');
const compiler = webpack(config);

// Tell express to use the webpack-dev-middleware and use the webpack.config.js
// configuration file as a base.
app.use(webpackDevMiddleware(compiler, {
  publicPath: config.output.publicPath
}));

// Serve the files on port 3000.
app.listen(3000, function () {
  console.log('Example app listening on port 3000!\n');
});
```
现在，添加一个npm script，以使更方便运行服务
```js
{
    "name": "development",
    "version": "1.0.0",
    "description": "",
    "main": "webpack.config.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "watch": "webpack --watch",
      "start": "webpack-dev-server --open",
+     "server": "node server.js",
      "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "clean-webpack-plugin": "^0.1.16",
      "css-loader": "^0.28.4",
      "csv-loader": "^2.1.1",
      "express": "^4.15.3",
      "file-loader": "^0.11.2",
      "html-webpack-plugin": "^2.29.0",
      "style-loader": "^0.18.2",
      "webpack": "^3.0.0",
      "webpack-dev-middleware": "^1.12.0",
      "xml-loader": "^1.2.1"
    }
  }
```
现在，在终端执行`npm run server`,打开浏览器，跳转到`http://localhost:3000`，可以看到webpack应用程序已经运行。
### 模块热替换
模块热替换HMR是webpack提供的最有用的功能之一。它允许在运行时更新各种模块，无需进行完全刷新。
#### 启用HMR
启用此功能相当简单，更新webpack-dev-server的配置，和使用webpack内置的HMR插件。还需要删掉print.js的入口起点，因为它现在正被index.js模块使用。
```js
const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
+ const webpack = require('webpack');

  module.exports = {
    entry: {
-      app: './src/index.js',
-      print: './src/print.js'
+      app: './src/index.js'
    },
    devtool: 'inline-source-map',
    devServer: {
      contentBase: './dist',
+     hot: true
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Hot Module Replacement'
      }),
+     new webpack.NamedModulesPlugin(),
+     new webpack.HotModuleReplacementPlugin()
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
添加了`NameModulesPlugin`,以便更容易查看要修补的依赖。在起步阶段，将通过在命令行运行`npm start`来启动并运行devserver。
现在修改index.js文件，以便当print.js内部发生变更时可以告诉webpack接受更新的模块。
```js
import _ from 'lodash';
  import printMe from './print.js';

  function component() {
    var element = document.createElement('div');
    var btn = document.createElement('button');

    element.innerHTML = _.join(['Hello', 'webpack'], ' ');

    btn.innerHTML = 'Click me and check the console!';
    btn.onclick = printMe;

    element.appendChild(btn);

    return element;
  }

  document.body.appendChild(component());
+
+ if (module.hot) {
+   module.hot.accept('./print.js', function() {
+     console.log('Accepting the updated printMe module!');
+     printMe();
+   })
+ }
```
更改`print.js`中`console.log`的输出内容，将会在浏览器中看到如下的输出。
```text
[HMR] Waiting for update signal from WDS...
main.js:4395 [WDS] Hot Module Replacement enabled.
+ 2main.js:4395 [WDS] App updated. Recompiling...
+ main.js:4395 [WDS] App hot update...
+ main.js:4330 [HMR] Checking for updates on the server...
+ main.js:10024 Accepting the updated printMe module!
+ 0.4b8ee77….hot-update.js:10 Updating print.js...
+ main.js:4330 [HMR] Updated modules:
+ main.js:4330 [HMR]  - 20
+ main.js:4330 [HMR] Consider using the NamedModulesPlugin for module names.
```
### 通过Node.js API
当使用webpack dev server和Node.js API时，不要将dev server选项放在webpack配置对象中(webpack config object)。而是，在创建选项时，将其作为第二个参数传递。例如：
`new WebpackDevServer(compiler,options)`
想要启用HMR，还需要修改webpack配置对象，使其包含HMR入口起点。`webpack-devserver`packag中具有一个叫着`addDevServerEntrypoints`的方法，可通过这种方法来实现。
`dev-server.js`
```js
const webpackDevServer = require('webpack-dev-server');
const webpack = require('webpack');

const config = require('./webpack.config.js');
const options = {
  contentBase: './dist',
  hot: true,
  host: 'localhost'
};

webpackDevServer.addDevServerEntrypoints(config, options);
const compiler = webpack(config);
const server = new webpackDevServer(compiler, options);

server.listen(5000, 'localhost', () => {
  console.log('dev server listening on port 5000');
});
```
借助于很多loader，使得很多模块热替换的过程变得很容易。
## tree shaking
tree shaking是一个术语，通常用于描述移除JavaScript上下文中的未引用代码，它依赖于ES2015模块系统中的静态结构特性，例如import和export。
### 将文件标记为无副作用(side-effect-free)
在一个纯粹的ESM模块世界中，识别出哪些文件有副作用很简单，但是此时有必要向webpack的compiler提供提示哪些代码是纯粹部分。
通过package.json的sideEffects属性来实现。
`{
  "name": "your-project",
  "sideEffects": false
}`
如同上面提到的，如果所有代码都不包含副作用，就可以简单地将该属性标记为false，来告知webpack，它可以安全地删除未用到的export导出。

>副作用的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个export或多个export，举例说明，例如polyfill，它影响全局作用域，并且通常不提供export。

如果有的代码确实有一些副作用，那么可以改为提供一个数组，数组方式支持相关文件的相对路径、绝对路径和glob模式。它在内部使用[micromatch](https://github.com/micromatch/micromatch#matching-features)
>任何导入的文件都会受到tree shaking 的影响，这意味着，如果在项目中使用类似css-loader，并导入css文件，则需要将其添加到side effect列表中，以免在生产模式中无意将它删除
```js
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js",
    "*.css"
  ]
}
```
最后，还可以再module.rules配置选项中设置sideEffects
```js

```
### 压缩输出
通过如上方式，已经可以通过import和export语法，找出那些需要删除的'未使用代码dead code'，然而目的不仅仅是要找出，还需要再bundle中删除它们。为此，将使用-p(production)这个webpack编译标记，来启用ugligyjs压缩插件。

`webpack.config.js`
```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
+ },
+ mode: "production"
};
```
准备就绪后，运行命令npm run build，然后查看输出结果是否发生改变。
>可以将应用程序想象成一棵树，绿色表示实际用到的源码和library，是树上活的树叶，灰色表示无用的代码，是秋天树上枯萎的树叶，为了除去死去的树叶，必须摇动这棵树，使它们落下。
## 生产环境构建
### 配置
开发环境development和生产环境production的构建目标差异很大，在开发环境中，需要具有强大的、具有实时重新加载live reloading或热替换hot module replacement能力的source map和localhost server。而在生产环境中，目标则转向于关注更小的bundle，更轻量的source map，以及更优化的资源，以改善加载时间。为了遵循逻辑分离，通常建议为每个环境编写彼此独立的webpack配置。
虽然，将生产环境和开发环境做了略微区分，但是，还是会遵循不重复原则，保留一个通用配置，为了将配置合并在一起，使用一个名为webpac-merge的工具，通过通用配置，就可以不必在环境特定的配置中重复代码。
先从安装webpack-merge开始，将之前指南中已经成型的那些代码再次进行分离
`npm install --save-dev webpack-merge`
```text
  webpack-demo
  |- package.json
- |- webpack.config.js
+ |- webpack.common.js
+ |- webpack.dev.js
+ |- webpack.prod.js
  |- /dist
  |- /src
    |- index.js
    |- math.js
  |- /node_modules
```
`webpack.common.js`
```js
+ const path = require('path');
+ const {CleanWebpackPlugin} = require('clean-webpack-plugin');
+ const HtmlWebpackPlugin = require('html-webpack-plugin');
+
+ module.exports = {
+   entry: {
+     app: './src/index.js'
+   },
+   plugins: [
+     new CleanWebpackPlugin(),
+     new HtmlWebpackPlugin({
+       title: 'Production'
+     })
+   ],
+   output: {
+     filename: '[name].bundle.js',
+     path: path.resolve(__dirname, 'dist')
+   }
+ };
```
`webpack.dev.js`
```js
+ const merge = require('webpack-merge');
+ const common = require('./webpack.common.js');
+
+ module.exports = merge(common, {
+   devtool: 'inline-source-map',
+   devServer: {
+     contentBase: './dist'
+   }
+ });
```
`webpack.prod.js`
```js
+ const merge = require('webpack-merge');
+ const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
+ const common = require('./webpack.common.js');
+
+ module.exports = merge(common, {
+   plugins: [
+     new UglifyJSPlugin()
+   ]
+ });
```
在webpa.common.js中，设置了entry和output属性，并且在其中引入两个环境公用的全部插件。在webpack.dev.js中，为此环境添加了推荐的devtool和简单的devServer配置，最后在webpack.prod.js中，引入之前在tree shaking中介绍过的UglifyJSPlugin。
### npm scripts
现在，把script重新指向到新配置，将npm start定义为开发环境脚本，并在其中使用webpack-dev-server，将npm run build定义为生产环境脚本：
```js
{
    "name": "development",
    "version": "1.0.0",
    "description": "",
    "main": "webpack.config.js",
    "scripts": {
-     "start": "webpack-dev-server --open",
+     "start": "webpack-dev-server --open --config webpack.dev.js",
-     "build": "webpack"
+     "build": "webpack --config webpack.prod.js"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "clean-webpack-plugin": "^0.1.17",
      "css-loader": "^0.28.4",
      "csv-loader": "^2.1.1",
      "express": "^4.15.3",
      "file-loader": "^0.11.2",
      "html-webpack-plugin": "^2.29.0",
      "style-loader": "^0.18.2",
      "webpack": "^3.0.0",
      "webpack-dev-middleware": "^1.12.0",
      "webpack-dev-server": "^2.9.1",
      "webpack-merge": "^4.1.0",
      "xml-loader": "^1.2.1"
    }
  }
```
### Minification
虽然UglifyJSPlugin是代码压缩方面比较好的选择，但是还有一些其他可选择项。比如：`BabeMinifyWebpackPlugin`、`ClosureCompilerPlugin`
确保新插件具有删除未引用代码的能力足矣。
### source map
鼓励在生产环境中启用source map，因为它们对调试源码和运行基准测试很有帮助，但是针对生产环境用途，选择一个构建快速的推荐配置，将在生产环境中使用source-map选项，而不是在开发环境中用到的inline-source-map
`webpack.prod.js`
```js
const merge = require('webpack-merge');
  const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
  const common = require('./webpack.common.js');

  module.exports = merge(common, {
+   devtool: 'source-map',
    plugins: [
-     new UglifyJSPlugin()
+     new UglifyJSPlugin({
+       sourceMap: true
+     })
    ]
  });
```
### 指定环境
许多library将通过与`process.env.NODE_ENV`环境变量关联，以决定library中应该引用哪些内容。例如，当不处于生产环境中时，某些library为了使调试变得容易，可能会添加额外的日志记录和测试。而且，当使用·`process.env.NODE_ENV === 'production'`时，一些library可能针对具体用户的环境进行代码优化，从而删除或添加一些重要代码。可以使用webpack内置的DefinePlugin为所有的依赖定义这个变量：
`webpack.prod.js`
```js
+ const webpack = require('webpack');
  const merge = require('webpack-merge');
  const UglifyJSPlugin = require('uglifyjs-webpack-plugin');
  const common = require('./webpack.common.js');

  module.exports = merge(common, {
    devtool: 'source-map',
    plugins: [
      new UglifyJSPlugin({
        sourceMap: true
+     }),
+     new webpack.DefinePlugin({
+       'process.env.NODE_ENV': JSON.stringify('production')
+     })
    ]
  });
```
>技术上讲，NODE_ENV是一个由Node.js暴露给执行脚本的系统环境变量。通常用于决定在开发环境与生产环境下，服务器工具、构建脚本和客户端library的行为。

如果正在使用像react这样的library，那么在添加此DefinePlugin插件后，应该看到bundle大小显著下降。还要注意，任何位于/src的本地代码都可以关联到press.env.NODE_ENV环境变量，所以以下检查也是有效地：
`src/index.js`
```js
import { cube } from './math.js';
+
+ if (process.env.NODE_ENV !== 'production') {
+   console.log('Looks like we are in development mode!');
+ }

  function component() {
    var element = document.createElement('pre');

    element.innerHTML = [
      'Hello webpack!',
      '5 cubed is equal to ' + cube(5)
    ].join('\n\n');

    return element;
  }

  document.body.appendChild(component());
```
## 代码分离
代码分离是webpack中最引人注目的特性之一，此特性能够把代码分离到不同的bundle中，然后可以按需加载或并行加载这些文件，代码分离可以用于获取更小的bundle，以及控制资源加载优先级，如果使用合理，会极大影响加载时间。
有三种常用的代码分离方法：
* 入口起点：使用entry配置手动地分离代码。
* 防止重复：使用CommonsChunkPlugin去重和分离chunk。
* 动态导入：通过模块的内联函数调用来分离代码。
### 入口起点entry points
这是迄今为止最简单最直观的分离代码的方式。不过这种方式手动配置较多，也有一些陷阱。先看看如何从main bundle中分离另一个模块。
```text
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
+ |- another-module.js
|- /node_modules
```
`another-module.js`
```js
import _ from 'lodash';

console.log(
  _.join(['Another', 'module', 'loaded!'], ' ')
);
```
`webpack.config.js`
```js
const path = require('path');
const HTMLWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    index: './src/index.js',
    another: './src/another-module.js'
  },
  plugins: [
    new HTMLWebpackPlugin({
      title: 'Code Splitting'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```
这种方法存在一些问题：
* 如果入口chunks之间包含重复的模块，那些重复模块都会被引入到各个bundle中。
* 方法不够灵活，并且不能将核心应用程序逻辑进行动态拆分代码

接着，通过使用CommonsChunkPlugin来移除重复的模块。
### 防止重复prevent duplication
CommonsChunkPlugin插件可以将公共的依赖模块提取到已有的入口chunk中，或者提取到一个新生成的chunk。使用这个插件，将之前示例中重复的lodash模块去除：
`webpack.config.js`
```js
const path = require('path');
+ const webpack = require('webpack');
  const HTMLWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: {
      index: './src/index.js',
      another: './src/another-module.js'
    },
    plugins: [
      new HTMLWebpackPlugin({
        title: 'Code Splitting'
+     }),
+     new webpack.optimize.CommonsChunkPlugin({
+       name: 'common' // 指定公共 bundle 的名称。
+     })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
这里使用CommonsChunkPlugin之后，应该可以看出，index.bundle.js中已经移除了重复的依赖模块。需要注意的是，CommonsChunkPlugin插件将lodash分离到单独的chunk，并且将其从main bundle中移除，减轻了大小。执行npm run build查看效果
```shell
Hash: 70a59f8d46ff12575481
Version: webpack 2.6.1
Time: 510ms
            Asset       Size  Chunks                    Chunk Names
  index.bundle.js  665 bytes       0  [emitted]         index
another.bundle.js  537 bytes       1  [emitted]         another
 common.bundle.js     547 kB       2  [emitted]  [big]  common
   [0] ./~/lodash/lodash.js 540 kB {2} [built]
   [1] (webpack)/buildin/global.js 509 bytes {2} [built]
   [2] (webpack)/buildin/module.js 517 bytes {2} [built]
   [3] ./src/another-module.js 87 bytes {1} [built]
   [4] ./src/index.js 216 bytes {0} [built]
```
以下是由社区提供的一些对于代码分离很有帮助的插件和loaders:
* [ExtractTextPlugin](https://www.webpackjs.com/plugins/extract-text-webpack-plugin)：用于将CSS从主应用程序中分离。
* [bundle-loader](https://www.webpackjs.com/loaders/bundle-loader)：用于分离代码和延迟加载生成的bundle。
* [promise-loader](https://github.com/gaearon/promise-loader)：类似于bundle-loader，但是使用的是promises。

CommonsChunkPlugin插件还可以通过使用显示的vendor chunks功能，从应用程序代码中分离vendor模块。

### 动态导入dynamic imports
当涉及到动态代码拆分时，webpack提供了两个类似的技术。对于动态导入，第一种，也是优先选择的方式是，使用import()语法，第二种，则是使用webpack特定的require.ensure。先尝试第一种：

>import()调用会在内部用到promises。

现在，不使用静态导入lodash，通过使用动态导入来分离一个chunk：
`src/index.js`
```js
- import _ from 'lodash';
-
- function component() {
+ function getComponent() {
-   var element = document.createElement('div');
-
-   // Lodash, now imported by this script
-   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   return import(/* webpackChunkName: "lodash" */ 'lodash').then(_ => {
+     var element = document.createElement('div');
+
+     element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+
+     return element;
+
+   }).catch(error => 'An error occurred while loading the component');
  }

- document.body.appendChild(component());
+ getComponent().then(component => {
+   document.body.appendChild(component);
+ })
```
注意，在注释中使用了webpackChunkName，这样做会使bundle被命名为lodash.bundle.js，而不是[id].bundle.js。执行webpack，查看lodash是否会分离到一个单独的bundle：
```text
Hash: a27e5bf1dd73c675d5c9
Version: webpack 2.6.1
Time: 544ms
           Asset     Size  Chunks                    Chunk Names
lodash.bundle.js   541 kB       0  [emitted]  [big]  lodash
 index.bundle.js  6.35 kB       1  [emitted]         index
   [0] ./~/lodash/lodash.js 540 kB {0} [built]
   [1] ./src/index.js 377 bytes {1} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
```
由于import()会返回一个promise，因此它可以和async函数一起使用，但是需要使用像Babel这样的预处理器和Syntax Dynamic Import Babel Plugin，下面通过async函数简化代码：
```js
- function getComponent() {
+ async function getComponent() {
-   return import(/* webpackChunkName: "lodash" */ 'lodash').then(_ => {
-     var element = document.createElement('div');
-
-     element.innerHTML = _.join(['Hello', 'webpack'], ' ');
-
-     return element;
-
-   }).catch(error => 'An error occurred while loading the component');
+   var element = document.createElement('div');
+   const _ = await import(/* webpackChunkName: "lodash" */ 'lodash');
+
+   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+
+   return element;
  }

  getComponent().then(component => {
    document.body.appendChild(component);
  });
```
### bundle分析bundle analysis
如果以分离代码作为开始，那么就以检查模块作为结束，分析输出结果是很有用处的，官方分析工具是一个好的初始选择，下面是一些社区支持的可选工具：
* webpack-chart：webpack数据交互饼图
* webpack-visualizer：可视化并分析你的bundle，检查那些模块占用空间，哪些可能是重复使用的。
* webpack-bundle-analyzer：一款分析bundle内容的插件及CLI工具，以便捷的、交互式、可缩放的树状图形式展现给用户。

## 懒加载
懒加载或者按需加载，是一种很好的优化网页或应用的方式。这种方式实际上是先把代码放在一些逻辑断点处分离开，然后在一些代码中完成某些操作后，立即引用或即将引用另外一些新的代码块。这样可以加快应用的初始加载速度，减轻应用总体体积，因为某些代码块可能永远不会被加载。
### 示例
在代码分离的例子基础上，进一步做些调整来说明这个概念。import()的代码确实会在脚本运行的时候产生一个分离的代码块lodash.bundle.js，会在技术概念上懒加载它，此时出现一个问题，加载这个包不需要用户的交互，意思是每次加载页面的时候都会请求它，这样对应用并不会产生好的影响，还会对性能产生负面影响。
这时，增加一个交互，当用户点击按钮的时候用console打印一些文字，但是会等到第一次交互的时候再去加载那个代码块。
```text
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
+ |- print.js
|- /node_modules
```
`src/print.js`
```js
console.log('The print.js module has loaded! See the network tab in dev tools...');

export default () => {
  console.log('Button Clicked: Here\'s "some text"!');
}
```
`src/index.js`
```js
+ import _ from 'lodash';
+
- async function getComponent() {
+ function component() {
    var element = document.createElement('div');
-   const _ = await import(/* webpackChunkName: "lodash" */ 'lodash');
+   var button = document.createElement('button');
+   var br = document.createElement('br');

+   button.innerHTML = 'Click me and look at the console!';
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.appendChild(br);
+   element.appendChild(button);
+
+   // Note that because a network request is involved, some indication
+   // of loading would need to be shown in a production-level site/app.
+   button.onclick = e => import(/* webpackChunkName: "print" */ './print').then(module => {
+     var print = module.default;
+
+     print();
+   });

    return element;
  }

- getComponent().then(component => {
-   document.body.appendChild(component);
- });
+ document.body.appendChild(component());
```
>当调用ES6模块的import()方法时，必须指向模块的.default值，因为它才是promise被处理后返回的实际的module对象。

现在运行webpack来验证懒加载功能：
```text
Hash: e0f95cc0bda81c2a1340
Version: webpack 3.0.0
Time: 1378ms
          Asset       Size  Chunks                    Chunk Names
print.bundle.js  417 bytes       0  [emitted]         print
index.bundle.js     548 kB       1  [emitted]  [big]  index
     index.html  189 bytes          [emitted]
   [0] ./src/index.js 742 bytes {1} [built]
   [2] (webpack)/buildin/global.js 509 bytes {1} [built]
   [3] (webpack)/buildin/module.js 517 bytes {1} [built]
   [4] ./src/print.js 165 bytes {0} [built]
    + 1 hidden module
Child html-webpack-plugin for "index.html":
       [2] (webpack)/buildin/global.js 509 bytes {0} [built]
       [3] (webpack)/buildin/module.js 517 bytes {0} [built]
        + 2 hidden modules
```
### 框架
许多框架和类库对于如何使用它们自己的方式实现懒加载都有自己的建议
* react：[Code Splitting and Lazy Loading](https://reacttraining.com/react-router/web/guides/code-splitting)
* Vue：[Lazy Load in Vue using Webpack's code splitting](https://alexjoverm.github.io/2017/07/16/Lazy-load-in-Vue-using-Webpack-s-code-splitting/)

## 缓存
>本指南继续沿用起步、管理输出和代码分离中的代码示例。

以上，使用webpack打包模块化后的应用程序，webpack会生成一个可部署的/dist目录，然后把打包后的内容放置在此目录中。只要/dist目录中的内容部署到服务器上，客户端(通常是浏览器)就能够访问此服务器的网站及其资源。而最后一步获取资源是比较耗费时间，这就是为什么浏览器使用一种名为缓存的技术。可以通过缓存，以降低网络流量，使网站加载速度更快，然而，如果在部署新版本时不更新资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本，由于缓存的存在，需要获取新的代码时，就会显得很棘手。
此指南的重点在于通过必要的配置，以确保webpack编译生成的文件能够被客户端缓存，而在内容变化后，能够请求到新的文件。
### 输出文件的文件名Output Filenames
通过使用output.filename进行文件名替换，可以确保浏览器获取到修改后的文件，[hash]替换可以用于在文件名包含一个构建相关的hash，但是更好的方式是使用[chunkhash]替换，在文件名中包含一个chunk相关的hash。
使用起步中的示例，以及管理输出中的plugins来作为项目的基础，所以不必手动处理维护index.html文件：
```text
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
|- /node_modules
```
`webpack.config.js`
```js
const path = require('path');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: './src/index.js',
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
-       title: 'Output Management'
+       title: 'Caching'
      })
    ],
    output: {
-     filename: 'bundle.js',
+     filename: '[name].[chunkhash].js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
使用此配置，然后运行构建脚本npm run build，应该产生以下输出：
```text
Hash: f7a289a94c5e4cd1e566
Version: webpack 3.5.1
Time: 835ms
                       Asset       Size  Chunks                    Chunk Names
main.7e2c49a622975ebd9b7e.js     544 kB       0  [emitted]  [big]  main
                  index.html  197 bytes          [emitted]
   [0] ./src/index.js 216 bytes {0} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
    + 1 hidden module
Child html-webpack-plugin for "index.html":
     1 asset
       [2] (webpack)/buildin/global.js 509 bytes {0} [built]
       [3] (webpack)/buildin/module.js 517 bytes {0} [built]
        + 2 hidden modules
```
可以看到，bundle的名称是内容(通过hash)的映射，如果不做修改，然后再次运行构建，一般情况下会认为文件名保持不变，然而，如果真的运行，可能发现情况并非如此。
```text
Hash: f7a289a94c5e4cd1e566
Version: webpack 3.5.1
Time: 835ms
                       Asset       Size  Chunks                    Chunk Names
main.205199ab45963f6a62ec.js     544 kB       0  [emitted]  [big]  main
                  index.html  197 bytes          [emitted]
   [0] ./src/index.js 216 bytes {0} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
    + 1 hidden module
Child html-webpack-plugin for "index.html":
     1 asset
       [2] (webpack)/buildin/global.js 509 bytes {0} [built]
       [3] (webpack)/buildin/module.js 517 bytes {0} [built]
        + 2 hidden modules
```
这也是因为webpack在入口chunk中，包含了某些样板boilerplate，特别是runtime和manifest。样板是指webpack运行时的引导代码。

