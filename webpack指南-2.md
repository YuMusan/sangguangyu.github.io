### 提取模板 Extracting Boilerplate
就像之前从代码分离了解到，CommonsChunkPlugin可以用于将模板分离到单独的文件中，然而，CommonsChunkPlugin有一个少为人知的功能是，能够在每次修改后的构建结果中，将webpack的样板和manifest提取出来。通过指定entry配置中未用到的名称，此插件会自动将需要的内容提取到单独的包中：
```js
const path = require('path');
+ const webpack = require('webpack');
  const {CleanWebpackPlugin} = require('clean-webpack-plugin');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    entry: './src/index.js',
    plugins: [
      new CleanWebpackPlugin(),
      new HtmlWebpackPlugin({
        title: 'Caching'
      }),
+     new webpack.optimize.CommonsChunkPlugin({
+       name: 'manifest'
+     })
    ],
    output: {
      filename: '[name].[chunkhash].js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
再次构建，然后查看提取出来的manifest bundle：
```text
Hash: 80552632979856ddab34
Version: webpack 3.3.0
Time: 1512ms
                           Asset       Size  Chunks                    Chunk Names
    main.5ec8e954e32d66dee1aa.js     542 kB       0  [emitted]  [big]  main
manifest.719796322be98041fff2.js    5.82 kB       1  [emitted]         manifest
                      index.html  275 bytes          [emitted]
   [0] ./src/index.js 336 bytes {0} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
    + 1 hidden module
```
也可以将第三方库library提取到单独的vendor chunk文件中，这是因为，它们很少像本地源代码那样频繁修改。利用客户端的长效缓存机制，通过命中缓存来消除请求，并减少向服务器获取资源 ，同时还能保证客户端代码和服务器端代码版本一致。可以通过使用心得entry起点，以及再额外配置一个CommonsChunkPlugin实例的组合方式来实现：
```js
  var path = require('path');
  const webpack = require('webpack');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
-   entry: './src/index.js',
+   entry: {
+     main: './src/index.js',
+     vendor: [
+       'lodash'
+     ]
+   },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Caching'
      }),
+     new webpack.optimize.CommonsChunkPlugin({
+       name: 'vendor'
+     }),
      new webpack.optimize.CommonsChunkPlugin({
        name: 'manifest'
      })
    ],
    output: {
      filename: '[name].[chunkhash].js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
>注意，引入顺序在这里很重要，CommonsChunkPlugin的vendor实例，必须在manifest实例之前引入

再次构建，然后查看新的vendor bundle
```text
Hash: 69eb92ebf8935413280d
Version: webpack 3.3.0
Time: 1502ms
                           Asset       Size  Chunks                    Chunk Names
  vendor.8196d409d2f988123318.js     541 kB       0  [emitted]  [big]  vendor
    main.0ac0ae2d4a11214ccd19.js  791 bytes       1  [emitted]         main
manifest.004a1114de8bcf026622.js    5.85 kB       2  [emitted]         manifest
                      index.html  352 bytes          [emitted]
   [1] ./src/index.js 336 bytes {1} [built]
   [2] (webpack)/buildin/global.js 509 bytes {0} [built]
   [3] (webpack)/buildin/module.js 517 bytes {0} [built]
   [4] multi lodash 28 bytes {0} [built]
    + 1 hidden module
```
### 模块标识符module identifiers
向项目中再添加一个模块print.js
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

`+ export default function print(text) {
+   console.log(text);
+ };`

`src/index.js`
```js
import _ from 'lodash';
+ import Print from './print';

  function component() {
    var element = document.createElement('div');

    // lodash 是由当前 script 脚本 import 导入进来的
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.onclick = Print.bind(null, 'Hello webpack!');

    return element;
  }

  document.body.appendChild(component());
```
