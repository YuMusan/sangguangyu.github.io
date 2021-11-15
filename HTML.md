# HTML

HTML 不是一门编程语言，而是一种用于定义内容结构的标记语言。HTML 由一系列的元素（elements）组成，这些元素可以用来包围不同部分的内容，使其以某种方式呈现或者工作。

一个典型的HTML页面大概是这样
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>我的测试站点</title>
  </head>
  <body>
    <p>这是我的页面</p>
  </body>
</html>
```

1. `<!DOCTYPE html>`: 声明文档类型. 很久以前，早期的HTML(大约1991年2月)，文档类型声明类似于链接，规定了HTML页面必须遵从的良好规则，能自动检测错误和其他有用的东西。使用如下:

        <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
        "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

2. `<html></html>`: `<html>`元素。这个元素包裹了整个完整的页面，是一个根元素。

3. `<head></head>`: `<head>`元素. 这个元素是一个容器，它包含了所有你想包含在HTML页面中但不想在HTML页面中显示的内容。这些内容包括你想在搜索结果中出现的关键字和页面描述，CSS样式，字符集声明等等。

4. `<meta charset="utf-8">`: 这个元素设置文档使用utf-8字符集编码，utf-8字符集包含了人类大部分的文字。基本上它能识别你放上去的所有文本内容。

5. `<title></title>`: 设置页面标题，出现在浏览器标签上，当你标记/收藏页面时它可用来描述页面。

6. `<body></body>`: `<body>`元素。 包含了你访问页面时所有显示在页面上的内容，文本，图片，音频，游戏等等。

## `<head>`标签里有什么？ Metadata-HTML中的元数据

在页面加载完成的时候，标签`<head>`里的内容，是不会在页面中显示出来的。它包含了像页面的`<title>`，CSS，指向自定义图标的链接和其他元数据

### `<title>`文档标题

浏览器页面标签显示的内容

### 元数据meta元素

元数据就是描述数据的数据

可以指定文档中字符的编码，例如`<meta charset="utf-8">`，这样在同一个页面中就可以同事处理中文和藏文。

添加作者和描述，meta元素包含了name和content特性，name指定了meta元素的类型，说明该元素包含了什么类型的信息，content指定了实际的元数据内容。

```html
meta name="author" content="Chris Mills">
<meta name="description" content="The MDN Learning Area aims to provide
complete beginners to the Web with all they need to know to get
started with developing web sites and applications.">
```

指定包含关于页面内容的关键字的页面内容描述是很有用的，它可能让页面在搜索引擎的相关搜索中出现的更多，这种行为在术语上被称为 Search Engine Optimization or SEO。

有些网站还有自己编写的元数据协议

### 自定义图标

在元数据中添加对自定义图标的引用，这些图标将会出现在浏览器打开页面中的标签上以及书签面板中的书签上

添加方式：
1. 将其保存在网站的索引页面相同的目录中，以ico格式保存
2. 在HTML `<head>`中引用：
        <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">

### 应用CSS和JavaScript

现在所有的网站都会使用CSS让网页更加炫酷，使用JavaScript让网页有交互功能，比如视频播放器，地图，游戏等更多功能。它们分别使用`<link> <script>`元素

link元素经常位于文档的头部，link元素有2个属性，rel= "stylesheet"表明是文档的样式表，href包含了样式表文件的路径、
    <link rel="stylesheet" href="my-css-file.css">

script脚本放在文档的尾部是更好的选择

### 为文档设定主语言

通过添加lang属性到HTML开始标签中来实现

    <html lang="zh-CN">


网页排版的结构层次产生各种标签元素，标签的语义化自然就产生。

### 超链接

超链接使得互联网成为一个互联的网络。

通过将文本转换为`<a>`元素内的链接来创建基本链接，给它一个href属性，它将指向希望链接的网址。

添加title属性补充链接有用信息。

文本链接，块级链接
```
//文本链接
<p>我创建了一个指向
<a href="https://www.mozilla.org/zh-CN/"
   title="了解 Mozilla 使命以及如何参与贡献的最佳站点。">Mozilla 主页</a>
的超链接。
</p>

//块级链接
<a href="https://www.mozilla.org/zh-CN/">
  <img src="mozilla-image.png" alt="链接至 Mozilla 主页的 Mozilla 标志">
</a>
```

统一资源定位符Uniform Resource Locator，URL是一个定义了在网络上的位置的文本字符串，使用路径查找文件。


锚定  a标签分配id属性可以连接到HTML文档的特定部分。

下载链接使用download属性都可以提供一个默认的保存文件名

电子邮件链接`<a href="mailto:nowhere@mozilla.org">向 nowhere 发邮件</a>`

这样会打开一个电子邮件发送信息mailto: IURL

### 文档与网站架构

文档虽然多式多样，但是最常见的有以下几部分：

* 页眉，通常横跨于整个页面顶部有一个大标题或一个标志，网站的主要一般信息通常会再次显示

* 导航栏，指向网站各个主要区段的超链接，通常用菜单按钮，链接或标签页表示。类似于标题栏，导航栏通常应在所有网页之间保持一致，否则会让用户感到疑惑

* 主内容

* 侧边栏

* 页脚，横跨页面底部的狭长区域。放置公共信息，通常为次要信息，还可以通过提供快速访问链接进行SEO

一个典型网站的布局：

![网站布局](https://mdn.mozillademos.org/files/16497/snapshot.png)

示例代码：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>二次元俱乐部</title>
    <link href="https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300|Sonsie+One" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=ZCOOL+KuaiLe" rel="stylesheet">
    <link href="style.css" rel="stylesheet">
  </head>

  <body>
    <header> <!-- 本站所有网页的统一主标题 -->
      <h1>聆听电子天籁之音</h1>
    </header>

    <nav> <!-- 本站统一的导航栏 -->
      <ul>
        <li><a href="#">主页</a></li>
        <!-- 共n个导航栏项目，省略…… -->
      </ul>

      <form> <!-- 搜索栏是站点内导航的一个非线性的方式。 -->
        <input type="search" name="q" placeholder="要搜索的内容">
        <input type="submit" value="搜索">
      </form>
    </nav>

    <main> <!-- 网页主体内容 -->
      <article>
        <!-- 此处包含一个 article（一篇文章），内容略…… -->
      </article>

      <aside> <!-- 侧边栏在主内容右侧 -->
        <h2>相关链接</h2>
        <ul>
          <li><a href="#">这是一个超链接</a></li>
          <!-- 侧边栏有n个超链接，略略略…… -->
        </ul>
      </aside>
    </main>

    <footer> <!-- 本站所有网页的统一页脚 -->
      <p>© 2050 某某保留所有权利</p>
    </footer>
  </body>
</html>
```

## 规划一个简单的网站
在完成页面内容的规划后，一般应按部就班的规划整个网站的内容，尽可能给用户最好的体验，需要哪些页面，如何排列组合这些页面，如何互相链接等问题不可忽略，这些工作称为信息架构。在大型网站中，大多数规划工作都可以归结于此。

1. 时刻记住，大多数页面会使用一些相同的元素，例如导航菜单以及页脚内容，记录这邪恶对所有页面都通用的内容

![](https://mdn.mozillademos.org/files/16508/%E9%80%9A%E7%94%A8%E5%86%85%E5%AE%B9.gif)

2. 为页面结构绘制草图，记录每一块的作用：

![](https://mdn.mozillademos.org/files/16509/%E9%A1%B5%E9%9D%A2%E5%B8%83%E5%B1%80.gif)

3. 对于期望添加进站点的所有其他内容展开头脑风暴，直接罗列出来

![](https://mdn.mozillademos.org/files/16522/%E5%8A%9F%E8%83%BD%E5%88%97%E8%A1%A8.gif)

4. 尝试对这些内容进行分组，这样可以了解哪些内容可以放在同一个页面上。

![](https://mdn.mozillademos.org/files/16521/%E5%8A%9F%E8%83%BD%E5%88%86%E7%B1%BB.gif)

5. 试着绘制一个站点地图的草图，使用一个气泡代表网站的一个页面，并绘制连线来表示页面间的一般工作流。主页面一般置于中心，且链接到其他大多数页面，小型网站的大多数页面都可以从主页的导航栏中链接跳转，同时也可以记录内容的显示方式

![](https://mdn.mozillademos.org/files/16523/%E7%AB%99%E7%82%B9%E5%9C%B0%E5%9B%BE.gif)

## 网页中添加图像、视频、音频
`<img>`

`<video>`

`<audio>`

## 嵌入技术

嵌入的历史

很久以前，很流行在网络上使用框架创建网站 — 网站的一小部分存储于单独的HTML页面中。这些被嵌入在一个称为框架集的主文档中，它允许您指定每个框架能够填充在屏幕上的区域，非常像调整表格的列和行的大小。这些做法在90年代中期至90年代后期被认为是比较酷的，有证据表明，将网页分解成较小的块，这样有利于下载速度 —尤其是在那时网络连接速度太慢的情况下更为明显。然而，这些技术有很多问题，随着网络速度越来越快，这些技术带来的问题远超过它们带来的积极因素，所以你再也看不到它们被使用了。

一小段时间之后（20世纪90年代末，21世纪初），插件技术变得非常受欢迎，例如Java Applet和Flash — 这些技术允许网络开发者将丰富的内容嵌入到网页中，例如视频和动画等，这些内容不能通过HTML单独实现。嵌入这些技术是通过诸如`<object>`和较少使用`<embed>`的元素来实现的，当时它们非常有用。由于许多问题，包括可访问性、安全性、文件大小等，它们已经过时了; 如今，大多数移动设备不再支持这些插件，桌面端也逐渐不再支持。

最后，`<iframe>`元素出现了（连同其他嵌入内容的方式，如`<canvas>`，`<video>`等），它提供了一种将整个web页嵌入到另一个网页的方法，看起来就像那个web页是另一个网页的一个`<img>`或其他元素一样。`<iframe>`现在经常被使用。

### `<iframe>`

旨在允许您将其他Web文档嵌入到当前文档中。这很适合将第三方内容嵌入您的网站，您可能无法直接控制，也不希望实现自己的版本 - 例如来自在线视频提供商的视频，Disqus等评论系统，在线地图提供商，广告横幅等。 

HTML内联框架元素iframe表示嵌套的browsing context，它能够将另一个HTML页面嵌入到当前页面中。每个迁入的浏览上下文都有自己的会话历史记录session history和DOM树，包含嵌入内容的浏览上下文称为父级浏览上下文，顶级浏览上下文通常是由window对象表示的浏览器窗口。

```html
<iframe src="https://developer.mozilla.org/en-US/docs/Glossary"
        width="100%" height="500"
        allow="fullscreen" sandbox>
  <p> <a href="https://developer.mozilla.org/en-US/docs/Glossary">
    Fallback link for browsers that don't support iframes
  </a> </p>
</iframe>
```

属性sandbox，该属性对呈现在iframe框架中的内容启用一些额外的限制条件

安全防护： 使用HTTPS，始终使用sadbox属性，配置csp指令
<hr/>

## window.postMessage

<hr/>

## 网页中添加矢量图形




