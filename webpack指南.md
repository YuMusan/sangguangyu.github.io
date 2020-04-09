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
