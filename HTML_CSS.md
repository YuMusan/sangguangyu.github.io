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

window.psotMessage()方法可以安全的实现跨源通信。通常对于两个不同页面的脚本，只有当执行它们的页面处于具有相同的协议，通常为https，端口号，443为https的默认值，以及主机两个页面的模数Document.domain设置为相同的值时，两个脚本才能相互通信。

window.postMessage()方法提供了一种受控机制来规避此限制。

从广义上将，一个窗口获得对另一个窗口的引用，比如targetWindow = window.opener，然后在窗口上调用targetWindow.postMessage()方法分发一个MessageEvent消息，接收消息的窗口根据需要处理此事件。传递给window.postMessage()的参数通过消息事件对象暴露给接收消息的窗口。

语法： `otherWindow.postMessage(message, targetOrigin, [transfer])`

message，将要发送给其他window的数据，它将会被结构化克隆算法序列化

targetOrigin，指定哪些窗口可以接收到消息事件，只有协议地址端口三者完全匹配消息才会被发送。

the dispatched event其他窗口触发message事件

执行如下代码，其他window可以监听分发的message
```js

window.addEventListener("message", receiveMessage, false);

function receiveMessage(event)
{
  // For Chrome, the origin property is in the event.originalEvent
  // object.
  // 这里不准确，chrome没有这个属性
  // var origin = event.origin || event.originalEvent.origin;
  var origin = event.origin
  if (origin !== "http://example.org:8080")
    return;

  // ...
}
```
message的属性
data，传递过来的对象
origin，调用postMessage时消息发送发窗口的origin，字符串由协议域名端口号拼接而成
source，对发送消息的窗口对象的引用，可以使用此来在具有不同origin的两个窗口之间建立双向通信。

```js
/*
 * A窗口的域名是<http://example.com:8080>，以下是A窗口的script标签下的代码：
 */

var popup = window.open(...popup details...);

// 如果弹出框没有被阻止且加载完成

// 这行语句没有发送信息出去，即使假设当前页面没有改变location（因为targetOrigin设置不对）
popup.postMessage("The user is 'bob' and the password is 'secret'",
                  "https://secure.example.net");

// 假设当前页面没有改变location，这条语句会成功添加message到发送队列中去（targetOrigin设置对了）
popup.postMessage("hello there!", "http://example.org");

function receiveMessage(event)
{
  // 我们能相信信息的发送者吗?  (也许这个发送者和我们最初打开的不是同一个页面).
  if (event.origin !== "http://example.org")
    return;

  // event.source 是我们通过window.open打开的弹出页面 popup
  // event.data 是 popup发送给当前页面的消息 "hi there yourself!  the secret response is: rheeeeet!"
}
window.addEventListener("message", receiveMessage, false);
```


```js
/*
 * 弹出页 popup 域名是<http://example.org>，以下是script标签中的代码:
 */

//当A页面postMessage被调用后，这个function被addEventListener调用
function receiveMessage(event)
{
  // 我们能信任信息来源吗？
  if (event.origin !== "http://example.com:8080")
    return;

  // event.source 就当前弹出页的来源页面
  // event.data 是 "hello there!"

  // 假设你已经验证了所受到信息的origin (任何时候你都应该这样做), 一个很方便的方式就是把event.source
  // 作为回信的对象，并且把event.origin作为targetOrigin
  event.source.postMessage("hi there yourself!  the secret response " +
                            "is: rheeeeet!",
                            event.origin);
}

window.addEventListener("message", receiveMessage, false);
```


<hr/>

## 网页中添加矢量图形

在网上，会和两种类型的图片打交道，位图和矢量图
  * 位图使用像素网格来定义 — 一个位图文件精确得包含了每个像素的位置和它的色彩信息。流行的位图格式包括 Bitmap (.bmp), PNG (.png), JPEG (.jpg), and GIF (.gif.)

  *矢量图使用算法来定义 — 一个矢量图文件包含了图形和路径的定义，电脑可以根据这些定义计算出当它们在屏幕上渲染时应该呈现的样子。 SVG 格式可以让创造用于 Web 的精彩的矢量图形。

svg是描述矢量图像的XML语言，它基本上是像HTML一样的标记，只是你有许多不同的元素来定义要显示在图像中的形状，以及要应用于这些形状的效果。 SVG用于标记图形，而不是内容

```xml
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     xmlns="http://www.w3.org/2000/svg">
  <rect width="100%" height="100%" fill="black" />
  <circle cx="150" cy="100" r="90" fill="blue" />
</svg>
```

快捷方式通过img元素嵌入SVG

```html
<img
    src="equilateral.svg"
    alt="triangle with all three sides equal"
    height="87px"
    width="100px" />
```

优点
* 快速，熟悉的图像语法与alt属性中提供的内置文本等效。
* 可以通过在`<a>`元素嵌套`<img>`，使图像轻松地成为超链接。
缺点
* 无法使用JavaScript操作图像。
* 不能用CSS伪类来重设图像样式（如:focus）。

### 响应式图片
分辨率切换：不同的尺寸

img元素的两个新属性srcset和sizes，来提供更多额外的资源图像和提示，帮助浏览器选择正确的资源

```html
<img srcset="elva-fairy-320w.jpg 320w,
             elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 320px) 280px,
            (max-width: 480px) 440px,
            800px"
     src="elva-fairy-800w.jpg" alt="Elva dressed as a fairy">
```

srcset定义了允许浏览器选择的图像集，以及每个图像的大小。  文件名 空格 图像的固有宽度
sizes定义了一组媒体条件并且指明当某些媒体条件为真时，什么样的图片尺寸是最佳选择
媒体条件 空格 媒体条件为真时，图像将填充的槽的宽度

美术设计
涉及更改该显示的图像以适应不同的图像显示尺寸

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
  <source media="(min-width: 800px)" srcset="elva-800w.jpg">
  <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
</picture>
```

* `<source>`元素包含一个media属性，这一属性包含一个媒体条件决定哪张图片会显示,条件返回真，那么就会显示这张图片。在这种情况下，如果视窗的宽度为799px或更少，第一个`<source>`元素的图片就会显示。如果视窗的宽度是800px或更大，就显示第二张图片。

* srcset属性包含要显示图片的路径。`<source>`可以使用引用多个图像的srcset属性，还有sizes属性。可以通过一个 `<picture>`元素提供多个图片，不过也可以给每个图片提供多分辨率的图片。实际上，不可能经常做这样的事情。

* 在任何情况下，都必须在 `</picture>`之前正确提供一个`<img>`元素以及它的src和alt属性，否则不会有图片显示。当媒体条件都不返回真的时候,它会提供图片；如果浏览器不支持 `<picture>`元素时，它可以作为后备方案。



美术设计：想为不同布局提供不同剪裁的图片——比如在桌面布局上显示完整的、横向图片，而在手机布局上显示一张剪裁过的、突出重点的纵向图片，可以用 `<picture>` 元素来实现。

分辨率切换：想要为窄屏提供更小的图片时，因为小屏幕不需要像桌面端显示那么大的图片；以及你想为高/低分辨率屏幕提供不同分辨率的图片时，都可以通过 vector graphics (SVG images)、 srcset 以及 sizes 属性来实现。

<hr/>

# CSS

CSS层叠样式表用于设置和布置网页。

CSS 可以用于给文档添加样式 —— 比如改变标题和链接的颜色及大小。它也可用于创建布局 —— 比如将一个单列文本变成包含主要内容区域和存放相关信息的侧边栏区域的布局。它甚至还可以用来做一些特效，比如动画。

CSS是一门基于规则的语言 —— 定义用于页中特定元素样式的一组规则. 

语法由选择器开头，接着是一对{}，大括号内部定义一个或多个形式为 属性(property):值(value); 的声明(declarations)

```css
h1 {
    color: red;
    font-size: 5em;
}

p {
    color: black;
}
```

CSS规则如何在HTML文档中生效

这是一个HTML文档

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>开始学习CSS</title>
</head>
<body>
    <h1>Hello World!</h1>
    <p>This is my first CSS example</p>
</body>
</html>
```

目前有三种方式可以实现让HTML文档遵守给它的CSS规则，这里更倾向于最普遍有用的方式，文档的开头链接CSS，被称为外部样式表

在HTML文档相同目录下创建一个文件，保存命名为style.css，然后在HTML文档中，`<head>`模块中加入link标签，链接引入这个CSS文档，`<link rel="stylesheet" href="styles.css">`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My CSS experiment</title>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <h1>Hello World!</h1>
    <p>This is my first CSS example</p>
  </body>
</html>
```
CSS文件可能是这样的
```css
h1 {
  color: blue;
  background-color: yellow;
  border: 1px solid black;
}

p {
  color: red;
}
```

第二种是内部样式表，内部样式表是指不使用外部CSS文件，而是将CSS放在HTML文件`<head>`标签里的`<style>`标签之中。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My CSS experiment</title>
    <style>
      h1 {
        color: blue;
        background-color: yellow;
        border: 1px solid black;
      }

      p {
        color: red;
      }
    </style>
  </head>
  <body>
    <h1>Hello World!</h1>
    <p>This is my first CSS example</p>
  </body>
</html>
```

第三种是内联样式，内联样式存在于HTML元素的style属性之中。其特点是每个CSS表只影响一个元素

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>My CSS experiment</title>
  </head>
  <body>
    <h1 style="color: blue;background-color: yellow;border: 1px solid black;">Hello World!</h1>
    <p style="color:red;">This is my first CSS example</p>
  </body>
</html>
```

### 选择器

为了样式化某些元素，会通过选择器来选中HTML文档中的这些元素。

每个CSS规则都以一个选择器或一组选择器为开始，去告诉浏览器这些规则应该应用到哪些元素上。

```text
h1
a:link
.manythings
#onething
*
.box p
.box p:first-child
h1, h2, .intro
```
### 规则

当不同的选择器选中相同的HTML元素时，哪个CSS规则会生效呢

CSS语言有规则来控制在发生碰撞时哪条规则将获胜--这些规则称为级联规则和专用规则。

### 属性和值

与值配对的属性称为CSS声明，CSS声明块与选择器配对，以生成CSS规则集(或CSS规则)。

![](https://mdn.mozillademos.org/files/16500/rules.png)

这里包含两个规则，一个用于H1选择器，另一个用于p选择器。h1的规则被突出显示。

### 函数
有一些可能的值以函数的形式出现，比如calc()函数

`<div class="outer"><div class="box">The inner box is 90% - 30px.</div></div>`

```css
.outer {
  border: 5px solid black;
}

.box {
  padding: 10px;
  width: calc(90% - 30px);
  background-color: rebeccapurple;
  color: white;
}
```
这里的calc()示例中，要求此框的宽度为包含块宽度的90%，减去30像素。


### @规则

@rules，这是一些特殊的规则，为CSS提供了一些关于如何表现的指导。
比如将额外的样式表导入主CSS样式表使用@import

    @import `style2.css`

比如使用媒体查询应用CSS，@media

```css
body {
  background-color: pink;
}

@media (min-width: 30em) {
  body {
    background-color: blue;
  }
}
```

### CSS如何工作
当浏览器展示一个文件的时候，它必须兼顾文件的内容和文件的样式信息，下面了解到它处理文件的标准的流程。
这里的步骤是浏览加载网页的简化版本

1. 浏览器载入HTML文件（比如从网络上获取）。

2. 将HTML文件转化成一个DOM（Document Object Model），DOM是文件在计算机内存中的表现形式，下一节将更加详细的解释DOM。

3. 接下来，浏览器会拉取该HTML相关的大部分资源，比如嵌入到页面的图片、视频和CSS样式。JavaScript则会稍后进行处理，简单起见，这里对如何加载JavaScript不会展开叙述。

4. 浏览器拉取到CSS之后会进行解析，根据选择器的不同类型（比如element、class、id等等）把它们分到不同的“桶”中。浏览器基于它找到的不同的选择器，将不同的规则（基于选择器的规则，如元素选择器、类选择器、id选择器等）应用在对应的DOM的节点中，并添加节点依赖的样式（这个中间步骤称为渲染树）。

5. 上述的规则应用于渲染树之后，渲染树会依照应该出现的结构进行布局。

6. 网页展示在屏幕上，被称为着色。


## 层叠与继承

CSS的一些最基本的概念---层叠、优先级和继承

## CSS选择器

标签，类，ID选择器

属性选择器

### 伪类和伪元素

伪类是选择器的一种，它用于选择处于特定状态的元素

伪元素以类似方式表现，不过表现得是像你往标记文本中加入全新的HTML元素一样，而不是向现有的元素上应用类。伪元素开头为双冒号::。

::before和::after伪元素与content属性的共同使用，在CSS中被叫做“生成内容”

关系选择器

## 盒模型

CSS中广泛使用两种盒子，块级盒子 block box 和内联盒子 inline box，在页面流和元素之间的关系方面变现出不同行为

### 块级盒子
* 盒子会在内联的方向上扩展并占据父容器在该方向上的所有可用空间，在绝大数情况下意味着盒子会和父容器一样宽
* 每个盒子都会换行
* width和height属性可以发挥作用
* 内边距（padding）, 外边距（margin） 和 边框（border） 会将其他元素从当前盒子周围“推开”

### 内联盒子

* 盒子不会产生换行。
* width 和 height 属性将不起作用。
* 垂直方向的内边距、外边距以及边框不会把其他处于 inline 状态的盒子推开。
* 水平方向的内边距、外边距以及边框会把其他处于 inline 状态的盒子推开。


通过对盒子display属性的设置，来控制盒子的外部显示类型


### 什么是CSS盒模型

CSS中组成一个块级盒子需要:

* Content box: 这个区域是用来显示内容，大小可以通过设置 width 和 height.
* Padding box: 包围在内容区域外部的空白区域； 大小通过 padding 相关属性设置。
* Border box: 边框盒包裹内容和内边距。大小通过 border 相关属性设置。
* Margin box: 这是最外面的区域，是盒子和其他元素之间的空白区域。大小通过 margin 相关属性设置。

![盒模型](https://mdn.mozillademos.org/files/16558/box-model.png)


### 标准盒模型

在标准模型中，如果给盒设置 width 和 height，实际设置的是 content box。 padding 和 border 再加上设置的宽高一起决定整个盒子的大小。

![标准盒模型](https://mdn.mozillademos.org/files/16559/standard-box-model.png)

### IE盒模型

使用这个模型，所有宽度都是可见宽度，所以内容宽度是该宽度减去边框和填充部分

![IE盒模型](https://mdn.mozillademos.org/files/16557/alternate-box-model.png)


默认浏览器会使用标准模型，如果需要使用替代模型，可以通过为其设置 box-sizing: border-box 来实现。 


### 外边距，内边距，边框

外边距

外边距是盒子周围一圈看不到的空间。它会把其他元素从盒子旁边推开。 外边距属性值可以为正也可以为负。设置负值会导致和其他内容重叠。

外边距折叠

如果有两个外边距相接的元素，这些外边距将合并为一个外边距，即最大的外边距的大小。

边框

边框是在边距和填充框之间绘制的。为边框设置样式时，有大量的属性可以使用——有四个边框，每个边框都有样式、宽度和颜色

内边距

内边距位于边框和内容区域之间，不能有负数量的内边距，所以值必须是0或正的值。应用于元素的任何背景都将显示在内边距后面，内边距通常用于将内容推离边框。

### display: inline-block

display有一个特殊的值，它在内联和块之间提供了一个中间状态。可以实现一个元素不切换到新行，但是可以设定宽度和高度，并避免重叠


## 背景与边框

背景background

背景颜色 background-clock

背景图片 background-image属性允许在元素的背景中显示图像

background-repeat属性用于控制图像的平铺行为
no-repeat — 不重复。
repeat-x —水平重复。
repeat-y —垂直重复。
repeat — 在两个方向重复。

background-size调整背景图像的大小，它可以设置长度或百分比值，来调整图像的大小以适应背景。
或者关键字
* cover —浏览器将使图像足够大，使它完全覆盖了盒子区，同时仍然保持其高宽比。
* contain — 浏览器将使图像的大小适合盒子内。

背景图像定位 background-position 属性允许选择背景图像显示在其应用到的盒子中的位置，它使用的坐标系中，框的左上角是(0,0)，框沿着水平(x)和垂直(y)轴定位。

背景附加
background-attachment控制内容滚动时，背景如何滚动

* scroll: 使元素的背景在页面滚动时滚动。如果滚动了元素内容，则背景不会移动。实际上，背景被固定在页面的相同位置，所以它会随着页面的滚动而滚动。
* fixed: 使元素的背景固定在视图端口上，这样当页面或元素内容滚动时，它就不会滚动。它将始终保持在屏幕上相同的位置。
* local: 局部值将背景固定在设置的元素上，因此当滚动元素时，背景也随之滚动。

边框 border

圆角border-radius
属性和与方框的每个角相关的长边来实现方框的圆角，可以使用两个长度或百分比作为值，第一个值定义水平半径，第二个值定义垂直半径。

## 处理不同方向的文本

CSS在最近几年得到了改进，以更好地支持不同方向的文本，包括从右到左，也包括从上到下的文本（如日文）——这些不同的方向属性被称为书写模式。

CSS中的书写模式是指文本的排列方向是横向还是纵向的。writing-mode 属性使我们从一种模式切换到另一种模式。

writing-mode的三个值分别是：

* horizontal-tb: 块流向从上至下。对应的文本方向是横向的。
* vertical-rl: 块流向从右向左。对应的文本方向是纵向的。
* vertical-lr: 块流向从左向右。对应的文本方向是纵向的。

writing-mode属性实际上设定的是页面上块级元素的显示方向——要么是从上到下，要么是从右到左，要么是从左到右。而这决定了文本的方向。

切换书写模式时，也在改变块和内联文本的方向。horizontal-tb书写模式下块的方向是从上到下的横向的，而 vertical-rl书写模式下块的方向是从右到左的纵向的。因此，块维度指的总是块在页面书写模式下的显示方向。而内联维度指的总是文本方向。 

水平书写模式下的两种维度

![水平书写模式](https://mdn.mozillademos.org/files/17148/horizontal-tb-zh.png)

纵向书写模式下的两种维度

![](https://mdn.mozillademos.org/files/17149/vertical-zh.png)


## 溢出

CSS中万物皆盒，因此我们可以通过给width和height（或者 inline-size block-size）赋值的方式来约束盒子的尺寸。溢出是在往盒子里面塞太多东西的时候发生，所以盒子里面的东西也不会老老实实待着。

CSS尽力减少数据损失，只要有可能，CSS就不会隐藏内容

overflow属性是控制元素溢出的一种方式，它告诉浏览器怎样处理溢出

overflow: visible 默认状态，可以看到溢出内容

overflow: hidden 隐藏溢出内容

overflow：scroll 盒子出现滚动条，滚动显示溢出内容

overflow-x，overflow-y分别控制水平和垂直方向滚动条

overflow: auto 内容超出盒子滚动条才会显示，若是没有则会消失，浏览器决定


表单元素重置

```css
button,
input,
select,
textarea {
  font-family: inherit;
  font-size: 100%;
  box-sizing: border-box;
  padding: 0; margin: 0;
}

textarea {
  overflow: auto;
} 
```

## CSS布局

CSS页面布局技术允许拾取网页中的元素，并且控制它们相对正常布局流、周边元素、父容器、或者主视口/窗口的位置。

### 正常布局流

正常布局流(normal flow)是指在不对页面进行任何布局控制时，浏览器默认的HTML布局方式。

使用css创建一个布局时，就会离开正常布局流，但是对于页面上的多数元素，正常布局流完全可以创建需要的布局。所以一个结构良好的Html文档开始是非常重要，按照默认方式来搭建页面而不用自造车轮。

会覆盖默认的布局行为：
* display属性设置值，创建新的布局方式，CSS Grid 和 Flexbox
* 浮动，应用float值，让块级元素互相并排成一行，不是一个堆叠在另一个上面。
* position属性，允许精准设置盒子中盒子的位置，正常布局流中，默认为static
* 表格布局，表格的布局方式应用在非表格内容上
* 多列布局，让块按列布局

### 弹性盒子Flexbox

弹性盒子被设计用于创建横向或是纵向的一维页面布局。元素可以膨胀以填充额外的空间, 收缩以适应更小的空间。

指定元素display: flex

flex-direction属性，指定主轴的方向（弹性盒子子类放置的地方—它默认值是 row 水平方向，另一个值column 垂直方向

flex-wrap: wrap 子代溢出时，使子代换行

flex-flow: row wrap 是flex-direction和flex-wrap的缩写

动态尺寸 flex: 2 200px 这表示每个flex 项将首先给出200px的可用空间，剩余的可用空间将根据分配的比例共享

align-item控制flex项在交叉轴垂直方向上的位置

* 默认值是stretch，其会使所有 flex 项沿着交叉轴的方向拉伸以填充父容器
* center 值会使这些项保持其原有的高度，但是会在交叉轴居中
* 也可以设置诸如 flex-start 或 flex-end 这样使 flex 项在交叉轴的开始或结束处对齐所有的值。

justify-content控制flex项在主轴上的位置

* 默认值是 flex-start，所有 flex 项都位于主轴的开始处。
*  flex-end 让 flex 项到结尾处。
* center 可以让 flex 项在主轴居中。
* space-around 使所有 flex 项沿着主轴均匀地分布，在任意一端都会留有一点空间。
* space-between，与space-around相似，只是不会在两端留下任何空间。

flex项排序 order
* 所有 flex 项默认的 order 值是 0。
* order 值大的 flex 项比 order 值小的在显示顺序中更靠后。
* 相同 order 值的 flex 项按源顺序显示。假如有四个元素，其 order 值分别是2，1，1和0，那么它们的显示顺序就分别是第四，第二，第三，和第一。


### Grid布局

Grid网格布局被设计用于同时在两个维度上把元素按行和列排列整齐。

一个网格通常具有许多的列（column）与行（row），以及行与行、列与列之间的间隙，这个间隙一般被称为沟槽（gutter）。

![网格](https://mdn.mozillademos.org/files/13899/grid.png)

display: grid 定义网格

grid-tempalte-columns 定义网格列

grid-template-columns: 200px 1fr 2fr 除了长度和百分比，用fr来灵活地定义网格的行与列的大小,这个单位表示了可用空间的一个比例

grid-template-rows 定义网格行

grid-column-gap属性来定义列间隙,grid-row-gap来定义行间隙,grid-gap同时设定两者。

可以使用repeat来重复构建具有某些宽度配置的某些列

    .container {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-gap: 20px;
    }

隐式网格就是为了放显式网格放不下的元素，浏览器根据已经定义的显式网格自动生成的网格部分。
使用grid-auto-rows和grid-auto-columns属性手动设定隐式网格的大小。

    .container {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-auto-rows: 100px;
      grid-gap: 20px;
    }

minmax 函数为一个行/列的尺寸设置了取值范围。设定为 minmax(100px, auto)，那么尺寸就至少为100像素，并且如果内容尺寸大于100像素则会根据内容自动调整。

多列填充

repeat函数中的一个关键字auto-fill来替代确定的重复次数，minmax函数来设定一个行/列的最小值，以及最大值1fr。

    .container {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
      grid-auto-rows: minmax(100px, auto);
      grid-gap: 20px;
    }

基于线的元素放置

网格有许多分隔线，第一条线的起始点与文档书写模式相关。
grid-column，grid-row 同时指定开始与结束的线，使用/符号分开。

```css
header {
  grid-column: 1 / 3;
  grid-row: 1;
}

article {
  grid-column: 2;
  grid-row: 2;
}

aside {
  grid-column: 1;
  grid-row: 2;
}

footer {
  grid-column: 1 / 3;
  grid-row: 3;
}
```

grid-template-areas属性放置元素

```css
.container {
  display: grid;
  grid-template-areas:
      "header header"
      "sidebar content"
      "footer footer";
  grid-template-columns: 1fr 3fr;
  grid-gap: 20px;
}

header {
  grid-area: header;
}

article {
  grid-area: content;
}

aside {
  grid-area: sidebar;
}

footer {
  grid-area: footer;
}
```

### 浮动

把一个元素“浮动”(float)起来，会改变该元素本身和在正常布局流（normal flow）中跟随它的其他元素的行为。这一元素会浮动到左侧或右侧，并且从正常布局流(normal flow)中移除，其他的周围内容就会在被设置浮动(float)的元素周围环绕。

float 属性有四个可能的值：
* left — 将元素浮动到左侧。
* right — 将元素浮动到右侧。
* none — 默认值, 不浮动。
* inherit — 继承父元素的浮动属性。

清楚浮动

* Empty Div 方法，顾名思义，就是一个空的 div。`<div style="clear: both;">`

* 溢出方法依赖于在父元素上设置溢出 CSS 属性。如果此属性在父元素上设置为 auto 或 hidden，则父元素将展开以包含浮动元素，从而有效地为后续元素清除它。

* CSS 伪选择器 ( :after) 来清除浮动
```css
.clearfix:after { 
   content: "."; 
   visibility: hidden; 
   display: block; 
   height: 0; 
   clear: both;
}
```


### 定位

定位(positioning)能够把一个元素从它原本在正常布局流(normal flow)中应该在的位置移动到另一个位置。定位(positioning)并不是做主要页面布局的方式，它更像是管理和微调页面中的一个特殊项的位置。

五种主要的定位类型：

* 静态定位(Static positioning)是每个元素默认的属性——它表示“将元素放在文档布局流的默认位置——没有什么特殊的地方”。

* 相对定位(Relative positioning)允许相对于元素在正常的文档流中的位置移动它——包括将两个元素叠放在页面上。对于微调和精准设计(design pinpointing)非常有用。

* 绝对定位(Absolute positioning)将元素完全从页面的正常布局流(normal layout flow)中移出，类似将它单独放在一个图层中。可以将元素相对于页面的 `<html>` 元素边缘固定，或者相对于该元素的最近被定位祖先元素(nearest positioned ancestor element)。绝对定位在创建复杂布局效果时非常有用，例如通过标签显示和隐藏的内容面板或者通过按钮控制滑动到屏幕中的信息面板。

* 固定定位(Fixed positioning)与绝对定位非常类似，但是它是将一个元素相对浏览器视口固定，而不是相对另外一个元素。 这在创建类似在整个页面滚动过程中总是处于屏幕的某个位置的导航菜单时非常有用。

* 粘性定位(Sticky positioning)是一种新的定位方式，它会让元素先保持和position: static一样的定位，当它的相对视口位置(offset from the viewport)达到某一个预设值时，它就会像position: fixed一样定位。

### 多列布局

多列布局模组给了一种把内容按列排序的方式，就像文本在报纸上排列那样。由于在web内容里用户在一个列上通过上下滚动来阅读两篇相关的文本是一种非常低效的方式，那么把内容排列成多列可能是一种有用的技术。

要把一个块转变成多列容器(multicol container)，可以使用 column-count属性来告诉浏览器多少列，也可以使用column-width来告诉浏览器以至少某个宽度的尽可能多的列来填充容器。

* 使用 column-gap 改变列间间隙。
* 用 column-rule 在列间加入一条分割线。

比如这样：

    .container {
      column-count: 3;
      column-gap: 20px;
      column-rule: 4px dotted rgb(79, 185, 227);
    }

多列内容折断

添加属性break-inside，并设值 avoid,增加旧属性 page-break-inside: avoid 能够获得更好的浏览器支持

```css
.container {
  column-width: 250px;
  column-gap: 20px;
}

.card {
  break-inside: avoid;
  page-break-inside: avoid;
  background-color: rgb(207, 232, 220);
  border: 2px solid rgb(79, 185, 227);
  padding: 10px;
  margin: 0 0 1em 0;
}
```

## 响应式设计

响应式Web设计不是单独的技术，它是描述Web设计的一种方式、或者是一组最佳实践的一个词，它是用来建立可以响应查看内容的设备的样式的一个词。

### 媒体查询

媒体查询允许我们运行一系列测试，例如用户的屏幕是否大于某个宽度或者某个分辨率，并将CSS选择性地适应用户的需要应用在样式化页面上。

下面的媒体查询进行测试，以知晓当前的Web页面是否被展示为屏幕媒体且视口至少有800像素宽。
```css
@media screen and (min-width: 800px) {
  .container {
    margin: 1em 2em;
  }
} 
```

媒体查询语法

    @media media-type and (media-feature-rule) {
      /* CSS rules go here */
    }

它由以下部分组成：

* 一个媒体类型，告诉浏览器这段代码是用在什么类型的媒体上的
* 一个媒体表达式，是一个被包含的CSS生效所需的规则或者测试
* 一组CSS规则，会在测试通过且媒体类型正确的时候应用

媒体类型有：all print screen speech

媒体特征规则： 一般最常探测的特征是视口宽度，可以使用min-width、max-width和width媒体特征，在视口宽度大于或者小于某个大小——或者是恰好处于某个大小——的时候，应用CSS。

朝向特征： orientation  竖放（portrait mode）和横放（landscape mode）模式。

pointer媒体特征。它可取三个值：none、fine和coarse。fine指针是类似于鼠标或者触控板的东西，它让用户可以精确指向一片小区域。coarse指针是你在触摸屏上的手指。none值意味着，用户没有指点设备，

媒体查询中，还可以存在 与 或 非 逻辑

and ， not

```css
/* 与逻辑 */
@media screen and (min-width: 400px) and (orientation: landscape) {
    body {
        color: blue;
    }
}

/* 或逻辑 */
@media screen and (min-width: 400px), screen and (orientation: landscape) {
    body {
        color: blue;
    }
}

/* 非逻辑 */
@media not all and (orientation: landscape) {
    body {
        color: blue;
    }
}
```

文字阴影，渐变，过渡，动画