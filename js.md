## Fetch API
Fetch API提供了一个JavaScript接口，用于访问和操纵HTTP管道的部分，例如请求和响应。提供了一个全局fetch()方法，简单合理来跨网络异步获取资源。
fetch规范与jQuery.ajax()主要有两种方式的不同：
* 当接受到一个代表错误的HTTP状态码时，从fetch()返回的promise不会被标记为reject，及时该HTTP响应的状态码是404或500.相反，它会将promise状态标记为resolve(会将resolve的返回值的ok属性设置为false)，仅当网络故障时，或请求被阻止时，才会标记为reject。
* 默认情况下，fetch不会从服务端发送或接受任何cookies，如果站点依赖于用户session，则会导致卫竟然证的请求(要发送cookies，必须设置credentials选项)
-----
#### 进行fetch请求
一个基本的fetch请求设置起来很简单。
```js
fetch('http://example.com/movies.json')
.then(function(response){
    return response.json();
}).then(function(myJson){
    console.log(myJson);
})
```
这里通过网络获取一个JSON文件并将其打印到控制台。最简单的用法是只提供一个参数用来指明需要fetch()到的资源路径，然后返回一个包含响应结果的promise(一个Response对象)
#### 支持的请求参数
fetch()接受第二个可选参数，一个可以控制不同配置的init对象：
```js
// Example POST method implementation:

postData('http://example.com/answer', {answer: 42})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))

function postData(url, data) {
  // Default options are marked with *
  return fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}
```
#### 发送带凭据的请求
为了让浏览器发送包含凭据的请求(即便是跨域源)，要将`credentials:'include'`添加到传递给fetch方法的init对象
```js
fetch('https://example.com', {
  credentials: 'include'  
})
```
如果只想在请求URL与调用脚本同一起源处时发送凭据，添加`credentials:'same-origin'`
```js
// The calling script is on the origin 'https://example.com'

fetch('https://example.com', {
  credentials: 'same-origin'  
})
```
如果在请求中不包含凭据，使用`credentials:'omit'`
```js
fetch('https://example.com', {
  credentials: 'omit'  
})
```
#### 上传JSON数据
使用fetch POST JSON数据
```js
var url = 'https://example.com/profile';
var data = {username: 'example'};

fetch(url, {
  method: 'POST', // or 'PUT'
  body: JSON.stringify(data), // data can be `string` or {object}!
  headers: new Headers({
    'Content-Type': 'application/json'
  })
}).then(res => res.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```
#### 上传文件
可以通过`HTML<input type="file" />`元素，FormData()和fetch()上传文件
```js
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```
#### 上传多个文件
可以通过`HTML<input type="file" mutiple/>`元素，FormData()和fetch()上传文件
```js
var formData = new FormData();
var photos = document.querySelector("input[type='file'][multiple]");

formData.append('title', 'My Vegas Vacation');
// formData 只接受文件、Blob 或字符串，不能直接传数组，所以必须循环嵌入
for (let i = 0; i < photos.files.length; i++) { 
    formData.append('photo', photos.files[i]); 
}

fetch('https://example.com/posts', {
  method: 'POST',
  body: formData
})
.then(response => response.json())
.then(response => console.log('Success:', JSON.stringify(response)))
.catch(error => console.error('Error:', error));
```
#### 检测请求是否成功
如果遇到网络故障，fetch()promise将会reject，带上一个TypeError对象，虽然这个情况经常是遇到权限问题或类似问题，比如404不是一个网络故障。想要精确的判断fetch()是否成功，需要包含promise resolved的情况，此时再判断Response.ok是不是为true。
```js
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    return response.blob();
  }
  throw new Error('Network response was not ok.');
}).then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL; 
}).catch(function(error) {
  console.log('There has been a problem with your fetch operation: ', error.message);
});
```
#### 自定义请求对象
除了传给fetch()一个资源的地址，还可以通过Reqyest()构造函数来创建一个request对象看，然后再作为参数传给fetch():
```js
var myHeaders = new Headers();

var myInit = { method: 'GET',
               headers: myHeaders,
               mode: 'cors',
               cache: 'default' };

var myRequest = new Request('flowers.jpg', myInit);

fetch(myRequest).then(function(response) {
  return response.blob();
}).then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```

Request()和fetch()接受同样的参数。甚至可以传入一个已存在的request对象来创造一个拷贝`const anotherRequest = new Request(myRequest,myInit)`。因为request和response bodies只能被使用一次(被设计成stream的方式，所以只能被读取一次)。创建一个拷贝就可以再次使用request/response,也可以使用不同的init参数

------
## Headers
使用Headers的接口，可以通过Headers()构造函数来创建一个自己的headers对象
```js
var content = "Hello World";
var myHeaders = new Headers();
myHeaders.append("Content-Type", "text/plain");
myHeaders.append("Content-Length", content.length.toString());
myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```
也可以传一个多维数组或者对象字面量：
```js
myHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
```
headers中的内容也可以被获取
```js
console.log(myHeaders.has("Content-Type")); // true
console.log(myHeaders.has("Set-Cookie")); // false
myHeaders.set("Content-Type", "text/html");
myHeaders.append("X-Custom-Header", "AnotherValue");
 
console.log(myHeaders.get("Content-Length")); // 11
console.log(myHeaders.getAll("X-Custom-Header")); // ["ProcessThisImmediately", "AnotherValue"]
 
myHeaders.delete("X-Custom-Header");
console.log(myHeaders.getAll("X-Custom-Header")); // [ ]
```
如果使用一个不合法的HTTP Header属性名，那么Headers的方法通常都会抛出TypeError异常。如果写入不可写的属性，也会抛出TypeError异常。除此之外，失败并不会抛出异常。
```js
var myResponse = Response.error();
try {
  myResponse.headers.set("Origin", "http://mybank.com");
} catch(e) {
  console.log("Cannot pretend to be a bank!");
}
```
最佳实践是在使用之前检查content type是否正确。
```js
fetch(myRequest).then(function(response) {
  if(response.headers.get("content-type") === "application/json") {
    return response.json().then(function(json) {
      // process your JSON further
    });
  } else {
    console.log("Oops, we haven't got JSON!");
  }
});
```
## Response对象
Response实例是在fetch()处理完promises之后返回的。
最常见的response属性有：
* Response.status----整数(默认值是200)，是response的状态码
* Response.statusText----字符串(默认值为OK)，该值与HTTP状态码消息对应
* Response.ok----该属性是来检查response的状态是否在200-299(包括200,299)这个范围内，该属性返回一个Boolean值。
## Body
不管是请求还是响应都能够包含body对象，body也可以是以下任意类型的实例。
* ArrayBuffer、ArrayBufferView、Blob/File、string、URLSearchParams、FormData
Body类定义了以下方法以获取body内容，这些方法都会返回一个被解析后Promise对象和数据。
* arrayBuffer()、blob()、json()、text()、formData()
这些方法让非文本化的数据使用起来更加简单。请求体可以由传入body参数来进行设置。
```js
var form = new FormData(document.getElementById('login-form'));
fetch("/login", {
  method: "POST",
  body: form
})
```
request和response都会试着自动设置Content-Type，如果没有设置Content-Type值，发送的请求也会自动设值。
>`fecth(input[,init])`
input定义要获取的资源
* 一个USVString字符串，包含要获取资源的URL，一个request对象
init一个配置项对象，包括所有对请求的设置。可选的参数有：
* method：请求使用的方式，如GET、POST
* header：请求的头信息，形式为Headers的对象或包含ByteString值得对象字面量

## 有待补充

---

## webSocket

WebSocket 它可以在用户的浏览器和服务器之间打开交互式通信会话。可以向服务器发送消息并接收事件驱动的响应，而无需通过轮询服务器的方式以获得响应

服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话

WebSockets Diagram

![webSoket](https://images.ctfassets.net/3prze68gbwl1/asset-17suaysk1qa1k1c/3ea3d8f59a4a701e383f01e0157083be/WebSockets-Diagram.png)

HTTP Long Polling Diagram

![HTTP_Long_Polling](https://images.ctfassets.net/3prze68gbwl1/asset-17suaysk1qa1kcz/f4de71b350de32d03fc67be78bcd1def/HTTP-Long-Polling-Diagram.png)

其他特点有：

* 建立在 TCP 协议之上，服务器端的实现比较容易。

* 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

* 数据格式比较轻量，性能开销小，通信高效。

* 可以发送文本，也可以发送二进制数据。

* 没有同源限制，客户端可以与任意服务器通信。

* 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。

### 客户端简单示例

WebSocket 的用法相当简单

```js
const ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
```

### 构造函数 webSocket()

WebSocket构造函数  WebSocket(url[, protocols])
WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例。

    const ws = new WebSocket('ws://localhost:8080');

### 常量
WebSocket.CONNECTING	0
WebSocket.OPEN	1
WebSocket.CLOSING	2
WebSocket.CLOSED	3

### 属性

webSocket.redyState
readyState属性返回实例对象的当前状态，共有四种。

CONNECTING：值为0，表示正在连接。
OPEN：值为1，表示连接成功，可以通信了。
CLOSING：值为2，表示连接正在关闭。
CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

redystate可与常量作比较，当做判断条件

webSocket.onopen  用于指定连接成功后的回调函数。

webSocket.onclose  用于指定连接关闭后的回调函数。

webSocket.onmessage  用于指定收到服务器数据后的回调函数。

WebSocket.onerror 用于指定连接失败后的回调函数。

WebSocket.binaryType 使用二进制的数据类型连接。显式指定 "blob"或"arraybuffer"

WebSocket.bufferedAmount  未发送至服务器的字节数。可以判断发送是否结束

WebSocket.url  WebSocket的绝对路径。

### 方法
WebSocket.close([code[, reason]])  关闭当前链接。

WebSocket.send(data)  对要传输的数据进行排队。

### 事件
使用 addEventListener() 或将一个事件监听器赋值给本接口的 oneventname 属性，来监听下面的事件。

open close message error

```js
// Create WebSocket connection.
const socket = new WebSocket('ws://localhost:8080');
// 属性或事件监听都可
// Connection opened
socket.onopen = function(event) {
  console.log("Connection open ...")
  socket.send('Hello Server!');
};

socket.addEventListener("open", function(event) {
  console.log("Connection open ...")
  socket.send('Hello Server!');
});

// Listen for messages
socket.onmessage = function(event) {
  console.log('Message from server ', event.data);
};

socket.addEventListener("message", function(event) {
  console.log('Message from server ', event.data);
});

// Connection error
socket.onerror = function(event) {
  console.log('Connection error ...');
  socket.send('Server,Connection error ...');
};

socket.addEventListener("error", function(event) {
  console.log('Connection error ...');
  socket.send('Server,Connection error ...');
});

// Connection close
socket.onclose = function(event) {
  console.log('Connection close');
  socket.close(1005,'Server,Connection close ...');
};

socket.addEventListener("close", function(event) {
  console.log('Connection close');
  socket.close(1005,'Server,Connection close ...');
});
```

### 服务端实现
有websocket客户端就必须有WebSocket服务器


## SSE Server-sent Events

使用server-sent 事件，服务器可以在任何时刻向Web 页面推送数据和信息。这些被推送进来的信息可以在这个页面上作为 Events + data 的形式来处理。

如果发送事件的脚本不同源，应该创建一个新的包含URL和options参数的EventSource对象。

    const evtSource = new EventSource("//api.example.com/ssedemo.php", { withCredentials: true } );

一旦你成功初始化了一个事件源,就可以对 message 事件添加一个处理函数开始监听从服务器发出的消息了:

```js
// 监听从服务器发送来的所有没有指定事件类型的消息(没有event字段的消息)
evtSource.onmessage = function(event) {
  const newElement = document.createElement("li");
  const eventList = document.getElementById("list");

  newElement.innerHTML = "message: " + event.data;
  eventList.appendChild(newElement);
}
```

也可以使用addEventListener()方法来监听其他类型的事件:

```js
evtSource.addEventListener("ping", function(event) {
  const newElement = document.createElement("li");
  const time = JSON.parse(event.data).time;
  newElement.innerHTML = "ping at " + time;
  eventList.appendChild(newElement);
});
```
这里只有在服务器发送的消息中包含一个值为"ping"的event字段的时候才会触发对应的处理函数

### 服务器端如何发送事件流

```php
date_default_timezone_set("America/New_York");
header("Cache-Control: no-cache");
header("Content-Type: text/event-stream");

$counter = rand(1, 10);
while (true) {
  // Every second, send a "ping" event.

  echo "event: ping\n";
  $curDate = date(DATE_ISO8601);
  echo 'data: {"time": "' . $curDate . '"}';
  echo "\n\n";

  // Send a simple message at random intervals.

  $counter--;

  if (!$counter) {
    echo 'data: This is a message at time ' . $curDate . "\n\n";
    $counter = rand(1, 10);
  }

  ob_end_flush();
  flush();
  sleep(1);
}
```
### 事件流格式

事件流仅仅是一个简单的文本数据流,文本应该使用 UTF-8 格式的编码.每条消息后面都由一个空行作为分隔符.以冒号开头的行为注释行,会被忽略.

规范中规定了下面这些字段:

* event 事件类型.如果指定了该字段,则在客户端接收到该条消息时,会在当前的EventSource对象上触发一个事件,事件类型就是该字段的字段值,用addEventListener()方法在当前EventSource对象上监听任意类型的命名事件,如果该条消息没有event字段,则会触发onmessage属性上的事件处理函数.

* data 消息的数据字段.如果该条消息包含多个data字段,则客户端会用换行符把它们连接成一个字符串来作为字段值.

* id 事件ID,会成为当前EventSource对象的内部属性"最后一个事件ID"的属性值.

* retry 一个整数值,指定了重新连接的时间(单位为毫秒),如果该字段值不是整数,则会被忽略.


### 构造函数 EventSource()
EventSource 是服务器推送的一个网络事件接口。一个EventSource实例会对HTTP服务开启一个持久化的连接，以text/event-stream 格式发送事件, 会一直保持开启直到被要求关闭。

const evtSource = new EventSource(url, configuration);

url 远程资源的位置
configuration 为配置新连接提供可选项    可选项：withCredentials，默认为 false，指示 CORS 是否应包含凭据( credentials )。

### 属性

EventSource.onerror 是一个 event handler，当发生错误时被调用，并且在此对象上派发 error  事件。

EventSource.onmessage 是一个 event handler，当收到一个 message 事件，即消息来自源头时被调用。

EventSource.onopen 是一个 event handler，当收到一个 open 事件，即连接刚打开时被调用。

EventSource.readyState  只读 一个unsigned short值，代表连接状态。可能值是 CONNECTING (0), OPEN (1), 或者 CLOSED (2)。

EventSource.url  只读一个DOMString，代表事件源的 URL。

### 方法

EventSource.close()
如果存在，则关闭连接，并且设置 readyState 属性为 CLOSED。如果连接已经被关闭，此方法不会再进行任何操作。

### 事件
使用 addEventListener() 或将一个事件监听器赋值给本接口的 oneventname 属性，来监听下面的事件。

error message open

## 轮询

客户端定时向服务器发送Ajax请求，服务器接到请求后马上返回响应信息并关闭连接。

```js
function poll() {
    setTimeout(function() {
        $.get("/path/to/server", function(data, status) {
            console.log(data);
            // 发起下一次请求
            poll();
        });
    }, 10000);
}
```

## 长轮询

客户端向服务器发送Ajax请求，服务器接到请求后hold住连接，直到有新消息才返回响应信息并关闭连接，客户端处理完响应信息后再向服务器发送新的请求。 

轮询可能在以下3种情况时终止。
* 有新数据推送 。当服务器向浏览器推送信息后，应该主动结束程序运行从而让连接断开，这样浏览器才能及时收到数据。
* 没有新数据推送 。应该设定一个最长时限，避免WEB服务器超时（Timeout），若一直没有新信息，服务器应主动向浏览器发送本次轮询无新信息的正常响应，并断开连接，这也被称为“心跳”信息。一般建议在10～20秒左右
* 网络故障或异常 。由于网络故障等因素造成的请求超时或出错也可能导致轮询的意外中断，此时浏览器将收到错误信息。

```js
// 客户端
function longPolling() {
  $.ajax({
    async: true, //异步
    url: "/path/to/server",
    type: "post",
    dataType: "JSON",
    data: {},
    timeout: 30000, //超时时间30s
    error: function(xhr, textStatus, thrownError) {
      longPolling() // 异常错误后再次发起请求
    }，
    success: function(response) {
      message = response.data.message;
      (message != "timeout") && broadcast(); //收到消息后发布消息
      longPolling();
    }
  })
}
```




## WebRTC (Web Real-Time Communications)

WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。WebRTC包含的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。

WebRTC允许在两个设备之间进行实时的对等媒体交换。通过称为信令的发现和协商过程建立连接。

WebRTC是一个完全对等技术，用于实时交换音频、视频和数据，同时提供一个中心警告。必须进行一种发现和媒体格式协商，以使不同网络上的两个设备相互定位的过程。这个过程被称为信令，并涉及两个设备连接到第三个共同商定的服务器。通过这个第三方服务器，这两台设备可以相互定位，并交换协商消息。


## javaScript 类、面向对象、promise

### 类

类是用于创建对象的模板，JS中的类建立在原型上，但是也有某些语法和语义未与ES5类相似语义共享。

### 定义类

类语法有两个组成部分：类表达式和声明。

### 类声明
定义类的一种方法是使用类声明。声明一个类，可以使用带有class关键字的类名

```js
class rectangle {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
}
```
类声明没有提升，必须先声明类，然后才能访问它。

类表达式是定义类的另一种方法。类表达式可以命名或不命名，命名类表达式的名称是该类体的局部名称。

```js
// 未命名/匿名类
let Rectangle = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
console.log(Rectangle.name);
// output: "Rectangle"

// 命名类
let Rectangle = class Rectangle2 {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
console.log(Rectangle.name);
// 输出: "Rectangle2"
```

### 类体和方法定义

一个类体是一对`{}`中的部分，这是定义类成员的位置，如方法或构造函数。类声明和类表达式的主体都执行在严格模式下。

构造函数。constructor方法是一个特殊的方法，用于创建和初始化由class创建的对象。一个类只能拥有一个名为constructor的特殊方法，如果类包含多个constructor方法，则会抛出一个syntaxError。一个构造函数可以使用super关键字来调用一个父类的构造函数。

```js
class rectangle {
    // constructor
    constructor(height, width) {
        this.height = height;
        this.width = widht;
    }
    // Getter
    get area() {
        return this.clacArea()
    }
    // Method
    calcArea() {
        return this.height * this.width;
    }
}
const square = new rectangle(10,10)
```

### 静态方法
static关键字用来定义一个类的一个静态方法。调用静态方法不需要实例化该类，但不能通过一个类实例调用静态方法，静态方法通常用于为一个应用程序创建工具函数。
```js
class Point {
    construct(x,y) {
        this.x = x;
        this.y = y;
    }

    static distance(a,b) {
        const dx = a.x - b.x;
        const dy = a.y - b.y;
        return Math.hypot(dx, dy)
    }
}

const p1 = new Point(5, 5);

p1.distance;
// undefined

console.log(Point.distance(p1, p2));
// 7.0710678118654755
```

### 用原型和静态方法绑定this

当调用静态或原型方法时没有指定this的值，那么方法内的this值将被置为`undefined`。

```js
class Animal {
    speak() {
        return this;
    }
    static eat() {
        return this;
    }
}

let obj = new Animal();
obj.speak(); // Animal{}
let speak = obj.speak;
speak(); // undifined

Animal.eat(); // Animal{}
let eat = Animal.eat;
eat(); // undifined
```

如果上述代码通过传统的基于函数的语法来实现，那么依据初始的this值，在非严格模式下调用会发生自动装箱。若初始值是`undefined`，this值会被设为全局对象。

```js
function Animal() { }

Animal.prototype.speak = function() {
  return this;
}

Animal.eat = function() {
  return this;
}

let obj = new Animal();
let speak = obj.speak;
speak(); // global object

let eat = Animal.eat;
eat(); // global object
```

>类的方法内部如果含有this，它默认指向类的实例。但是，将方法提取出来单独使用，this会指向该方法运行时所在的环境，会因为找不到方法而导致报错。

实例属性必须定义在类的方法里：
```js
class Rectangle {
  constructor(height, width) {    
    this.height = height;
    this.width = width;
  }
}
```
静态的或原型的数据属性必须定义在类定义的外面。
```js
Rectangle.staticWidth = 20;
Rectangle.prototype.prototypeWidth = 25;
```

### 字段声明

公有字段。通过预先声明字段，类定义变得更加自我记录，并且字段始终存在。

```js
class Rectangle {
  height = 0;
  width;
  constructor(height, width) {    
    this.height = height;
    this.width = width;
  }
}
```

私有字段。私有字段只能在类里读取或写入，从外部引用私有字段是错误的。

```js
class Rectangle {
  #height = 0;
  #width;
  constructor(height, width) {    
    this.#height = height;
    this.#width = width;
  }
}
```
### extends 扩展子类
extends 关键字在 类声明或类表达式中用于创建类的子类

```js
class Animal { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name); // 调用超类构造函数并传入name参数
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

const d = new Dog('Mitzie');
d.speak();// 'Mitzie barks.'
```
如果子类中定义了构造函数，那么它必须先调用 super() 才能使用this。
也可以继承传统的基于函数的类:

```js
function Animal (name) {
  this.name = name;  
}
Animal.prototype.speak = function () {
  console.log(this.name + ' makes a noise.');
}

class Dog extends Animal {
  speak() {
    super.speak();
    console.log(this.name + ' barks.');
  }
}

var d = new Dog('Mitzie');
d.speak();//Mitzie makes a noise.  Mitzie barks.
```
注意，类不能继承常规对象(不可构造)。如果要继承常规对象，可以改用`Object.setPrototypeOf()`

```js
var Animal = {
  speak() {
    console.log(this.name + ' makes a noise.');
  }
};

class Dog {
  constructor(name) {
    this.name = name;
  }
}

Object.setPrototypeOf(Dog.prototype, Animal);// 如果不这样做，在调用speak时会返回TypeError

var d = new Dog('Mitzie');
d.speak(); // Mitzie makes a noise.
```
### super调用超类

super关键字用于调用对象的父对象的函数

```js
class Cat { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Lion extends Cat {
  speak() {
    super.speak();
    console.log(this.name + ' roars.');
  }
}
```

### 公有字段

>公有（public）和私有（private）字段声明是一个JavaScript标准委员会TC39提议的试验性功能 （第3阶段）。

静态公有字段和实例公有字段都是可编辑的，可遍历的，可配置的，它们参与原型的继承。

静态公有字段使用关键字 static 声明，在声明一个类的时候，使用Object.defineProperty方法将静态公有字段添加到类的构造函数中，类被声明后，可以从类的构造函数访问静态公有字段。

```js
class ClassWithStaticField {
  static staticField = 'static field';
}

console.log(ClassWithStaticField.staticField);
// 预期输出值: "static field"​


// 没有设定初始化程序的字段将默认被初始化为'undefined'
class ClassWithStaticField {
  static staticField;
}

console.assert(ClassWithStaticField.hasOwnProperty('staticField'));
console.log(ClassWithStaticField.staticField);
// 预期输出值: "undefined"
```

静态公有字段不会在子类里重复初始化，但可以通过原型链访问它们。

```js
class ClassWithStaticField {
  static baseStaticField = 'base field';
}

class SubClassWithStaticField extends ClassWithStaticField {
  static subStaticField = 'sub class field';
}

console.log(SubClassWithStaticField.subStaticField);
// 预期输出值: "sub class field"

console.log(SubClassWithStaticField.baseStaticField);
// 预期输出值: "base field"
```

公有实例字段可以在基类的构造过程中(构造函数主体运行前)使用`Object.defineProperty`添加，也可以在子类构造函数中的super()函数结束后添加

```js
class ClassWithInstanceField {
  instanceField = 'instance field';
}

const instance = new ClassWithInstanceField();
console.log(instance.instanceField);
// 预期输出值: "instance field"
```

当初始化字段时，this指向的是类正在构造中的实例。可以在子类中使用super来访问超类的原型。

```js
class ClassWithInstanceField {
  baseInstanceField = 'base field';
  anotherBaseInstanceField = this.baseInstanceField;
  baseInstanceMethod() { return 'base method output'; }
}

class SubClassWithInstanceField extends ClassWithInstanceField {
  subInstanceField = super.baseInstanceMethod();
}

const base = new ClassWithInstanceField();
const sub = new SubClassWithInstanceField();

console.log(base.anotherBaseInstanceField);
// 预期输出值: "base field"

console.log(sub.subInstanceField);
// 预期输出值: "base method output"
```

### 公共方法
静态公共方法，关键字static将为一个类定义一个静态方法。静态方法不会在实例中被调用，而只会被类本身调用，它们经常是工具函数

```js
class ClassWithStaticMethod {

  static staticProperty = 'someValue';
  static staticMethod() {
    return 'static method has been called.';
  }

}

console.log(ClassWithStaticMethod.staticProperty);
// output: "someValue"
console.log(ClassWithStaticMethod.staticMethod());
// output: "static method has been called."
```

公共实例方法，是在类的赋值阶段用Object.defineProperty方法添加到类中的。

```js
class ClassWithPublicInstanceMethod {
  publicMethod() {
    return 'hello world';
  }
}

const instance = new ClassWithPublicInstanceMethod();
console.log(instance.publicMethod());
// 预期输出值: "hello worl​d"
```

在实例的方法中，this指向的是实例本身，使用super访问到超类的原型，由此调用超类的方法。

```js
class BaseClass {
  msg = 'hello world';
  basePublicMethod() {
    return this.msg;
  }
}

class SubClass extends BaseClass {
  subPublicMethod() {
    return super.basePublicMethod();
  }
}

const instance = new SubClass();
console.log(instance.subPublicMethod());
// 预期输出值: "hello worl​d"
```

getter和setter是和类的属性绑定的特殊方法，分别会在其绑定的属性被取值、赋值时调用。使用get和set句法定义实例的公共getter和setter。

```js
class ClassWithGetSet {
  msg = 'hello world';
  get msg() {
    return this.msg;
  }
  set msg(x) {
    this.msg = `hello ${x}`;
  }
}

const instance = new ClassWithGetSet();
console.log(instance.msg);
// expected output: "hello worl​d"

instance.msg = 'cake';
console.log(instance.msg);
// 预期输出值: "hello cake"
```

## promise

一个promise对象代表一个在promise被创建出时不一定的状态值，它把异步操作最终的成功返回值或者失败原因和相应的处理程序关联起来。使得异步方法可以像同步方法一样返回值：异步方法不会立即返回最终的值，而是会返回一个promise，以便在未来某时把值交给使用者。
一个promise必然处于以下几种状态之一：

* 待定pending：初始状态，既没有被兑现，也没有被拒绝
* 已兑现fulfilled：意味着操作成功完成
* 已拒绝rejected：意味着操作失败

待定状态的promise对象要么会通过一个值被兑现，要么会通过一个原因(错误)被拒绝rejected。当情况发生时，promise的then方法排列的相关处理程序就会被调用。如果promise在相应的处理程序被绑定时就已经被兑现或被拒绝，那么这个处理程序就会被调用，因此在完成异步操作和绑定处理方法之间不会存在竞争状态。

![promise](https://mdn.mozillademos.org/files/8633/promises.png)

### promise链式调用

可以用`promise.then(),promise.catch(),promise.finally()`这些方法将进一步的操作与一个变为已敲定状态的promise关联起来。这些方法还会返回一个新生成的promise对象，这个对象可以被非强制性的用来做链式调用。

```js
const myPromise =
  (new Promise(myExecutorFunc))
  .then(handleFulfilledA)
  .then(handleFulfilledB)
  .then(handleFulfilledC)
  .catch(handleRejectedAny);
```

一个已经处于已敲定settled状态的promise中的操作只有promise链式调用的栈被清空了和一个时间循环过去了之后才会被执行。

### promise构造器

通过new关键字和promise构造器创建它的对象。构造器接受一个名为executor function的函数，此函数接受两个函数参数。当异步任务成功时，第一个函数resolve将被调用，并返回一个值代表成功，当失败时，第二个函数reject将被调用，并返回失败原因(失败原因通常是一个error对象)。

```js
const myPromise = new Promise((resolve, reject) => {
  // do something asynchronous which eventually calls either:
  //
  //   resolve(someValue)        // fulfilled
  // or
  //   reject("failure reason")  // rejected
});

// 提供拥有promise功能的函数，简单返回一个promise即可：

function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest() 
    xhr.open("GET", url) 
    xhr.onload = () => resolve(xhr.responseText) 
    xhr.onerror = () => reject(xhr.statusText) 
    xhr.send() 
  });
}
```

```js
  function imgLoad(url) {
    // Create new promise with the Promise() constructor;
    // This has as its argument a function
    // with two parameters, resolve and reject
    return new Promise(function(resolve, reject) {
      // Standard XHR to load an image
      var request = new XMLHttpRequest();
      request.open('GET', url);
      request.responseType = 'blob';
      // When the request loads, check whether it was successful
      request.onload = function() {
        if (request.status === 200) {
        // If successful, resolve the promise by passing back the request response
          resolve(request.response);
        } else {
        // If it fails, reject the promise with a error message
          reject(Error('Image didn\'t load successfully; error code:' + request.statusText));
        }
      };
      request.onerror = function() {
      // Also deal with the case when the entire request fails to begin with
      // This is probably a network error, so reject the promise with an appropriate message
          reject(Error('There was a network error.'));
      };
      // Send the request
      request.send();
    });
  }
  // Get a reference to the body element, and create a new image object
  var body = document.querySelector('body');
  var myImage = new Image();
  // Call the function with the URL we want to load, but then chain the
  // promise then() method on to the end of it. This contains two callbacks
  imgLoad('myLittleVader.jpg').then(function(response) {
    // The first runs when the promise resolves, with the request.response
    // specified within the resolve() method.
    var imageURL = window.URL.createObjectURL(response);
    myImage.src = imageURL;
    body.appendChild(myImage);
    // The second runs when the promise
    // is rejected, and logs the Error specified with the reject() method.
  }, function(Error) {
    console.log(Error);
  });
  
```

### 静态方法

`Promise.all(iterable)`
这个方法返回一个新的promise对象，这个promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里的promise对象失败则立即触发该promise对象的失败。这个新的promise对象在触发成功状态后，会把一个包含iterable里所有promise返回值的数组作为成功回调的返回值，顺序跟iterable的顺序保持一致，如果这个新的promise对象出发了失败状态，它会把iterable里的第一个触发失败的promise对象的错误信息作为它的失败错误信息。

`Promise.reject(reason)`
返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法

`Promise.resolve(value)`
返回一个状态由给定value决定的Promise对象。

尚在草案实验阶段的方法：

* `Promise.allSettled(iterable)`：等到所有promises都已敲定（settled）（每个promise都已兑现（fulfilled）或已拒绝（rejected））。返回一个promise，该promise在所有promise完成后完成。并带有一个对象数组，每个对象对应每个promise的结果。

* `Promise.any(iterable)`：接收一个Promise对象的集合，当其中的一个 promise 成功，就返回那个成功的promise的值。

* `Promise.race(iteralbe)`：当iterable参数里的任意一个子promise被成功或失败后，父promise马上也会用子promise的成功返回值或失败详情作为参数调用父promise绑定的相应句柄，并返回该promise对象。

## 元编程

从ECMAScript 2015 开始，JavaScript 获得了 Proxy 和 Reflect 对象的支持，允许拦截并定义基本语言操作的自定义行为（例如，属性查找，赋值，枚举，函数调用等）。借助这两个对象，可以在 JavaScript 元级别进行编程。




# JavaScript

## 对象原型

JavaScript 常被描述为一种基于原型的语言 (prototype-based language)——每个对象拥有一个原型对象，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为原型链 (prototype chain)，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。

准确地说，这些属性和方法定义在Object的构造器函数(constructor functions)之上的prototype属性上，而非对象实例本身。

在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个链接（它是__proto__属性，是从构造函数的prototype属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。

some.__proto__访问原型对象，some.prototype定义可被继承的属性或方法

每个实例对象都从原型中继承了一个constructor属性，该属性指向了用于构造此实例对象的构造函数。

## 类

```js
class Person {
  constructor(first, last, age, gender, interests) {
    this.name = {
      first,
      last
    };
    this.age = age;
    this.gender = gender;
    this.interests = interests;
  }

  greeting() {
    console.log(`Hi! I'm ${this.name.first}`);
  };

  farewell() {
    console.log(`${this.name.first} has left the building. Bye for now!`);
  };
}
```

class声明生成一个新的类，块代码定义了类的特性：
* constructor()方法定义代表Person类的构造函数。
* greeting()和farewell()是类方法。在构造函数之后定义与类关联的方法。

### 实例化对象实例 

new运算符
```js
let han = new Person('Han', 'Solo', 25, 'male', ['Smuggling']);
han.greeting();
// Hi! I'm Han

let leia = new Person('Leia', 'Organa', 19, 'female', ['Government']);
leia.farewell();
// Leia has left the building. Bye for now
```

### 继承类

使用extends 关键字告诉 JavaScript 继承类的 基础类，从而创建一个子类

与 new 运算符将 this 初始化为新分配的对象 的老式构造函数不同， this 不会为由 extends 关键字定义的类（即子类）自动初始化。

对于子类，对新分配对象的 this 初始化始终依赖于父类构造函数，要调用父构造函数，必须使用 super() 运算符

```js
class Teacher extends Person {
  constructor(subject, grade) {
    super(); // Now 'this' is initialized by calling the parent constructor.
    this.subject = subject;
    this.grade = grade;
  }
}
```

### 继承父类属性

```js
class Teacher extends Person {
  constructor(first, last, age, gender, interests, subject, grade) {
    super(first, last, age, gender, interests);

    // subject and grade are specific to Teacher
    this.subject = subject;
    this.grade = grade;
  }
}
```
super() 运算符实际上是父类构造函数，因此将父类构造函数的必要参数传递给它就会初始化子类中的父类属性，从而继承它

### getter and setter

如果要更改创建的类中属性的值，使用 getter 和 setter 来处理这种情况

getter 和 setter 成对工作。getter 返回变量的当前值，其相应的 setter 将变量的值更改为它定义的值。

```js
class Teacher extends Person {
  constructor(first, last, age, gender, interests, subject, grade) {
    super(first, last, age, gender, interests);
    // subject and grade are specific to Teacher
    this._subject = subject;
    this.grade = grade;
  }

  get subject() {
    return this._subject;
  }

  set subject(newSubject) {
    this._subject = newSubject;
  }
}

let snape = new Teacher('Severus', 'Snape', 58, 'male', ['Potions'], 'Dark arts', 5);

// Check the default value
console.log(snape.subject) // Returns "Dark arts"

// Change the value
snape.subject = "Balloon animals" // Sets _subject to "Balloon animals"

// Check it again and see if it matches the new value
console.log(snape.subject) // Returns "Balloon animals"
```

## JSON

JSON 是一种按照JavaScript对象语法的数据格式

JSON 是一种纯数据格式，它只包含属性，没有方法。
JSON要求在字符串和属性名称周围使用双引号。 单引号无效。

JSON.parse(): 以文本字符串形式接受JSON对象作为参数，并返回相应的对象。
JSON.stringify(): 接收一个对象作为参数，返回一个对应的JSON字符串。

## 异步

### 相关概念

当浏览器里的一个web应用进行密集运算还没有把控制权返回给浏览器的时候，整个浏览器就像冻僵了一样，这叫做阻塞；这时候浏览器无法继续处理用户的输入并执行其他任务，直到web应用交回处理器的控制。

一个线程是一个基本的处理过程，程序用它来完成任务，每个线程一次只能执行一个任务。

现在的计算机大都有多个内核（core），因此可以同时执行多个任务。支持多线程的编程语言可以使用计算机的多个内核，同时完成多个任务

JavaScript 传统上是单线程的。即使有多个内核，也只能在单一线程上运行多个任务，此线程称为主线程（main thread）。

经过一段时间，JavaScript获得了一些工具来帮助解决这种问题。通过 Web workers 可以把一些任务交给一个名为worker的单独的线程，这样就可以同时运行多个JavaScript代码块。一般来说，用一个worker来运行一个耗时的任务，主线程就可以处理用户的交互（避免了阻塞）

web workers相当有用，但是也有局限。主要的问题是不能访问 DOM — 不能让一个worker直接更新UI。

虽然在worker里面运行的代码不会产生阻塞，但是基本上还是同步的。当一个函数依赖于几个在它之前运行的过程的结果，这就会成为问题。

为了解决这些问题，浏览器允许异步运行某些操作。

### 异步JavaScript

两种异步编程风格：老派callbacks，新派promise。

异步回调是在调用将在后台开始执行代码的函数时指定为参数的函数。 当后台代码完成运行时，它会调用回调函数让您知道工作已完成，或者让您知道发生了一些有趣的事情。 使用回调现在有点过时了，但您仍然会看到它们在许多较旧但仍然常用的 API 中使用。

异步回调的一个例子是 addEventListener() 方法

```js
btn.addEventListener('click', () => {
  alert('You clicked me!');

  let pElem = document.createElement('p');
  pElem.textContent = 'This is a newly-added paragraph.';
  document.body.appendChild(pElem);
});

// 第一个参数是要监听的事件类型，第二个参数是在事件触发时调用的回调函数。
```

另一个通过 XMLHttpRequest API 加载资源的示例

```js
function loadAsset(url, type, callback) {
  let xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.responseType = type;

  xhr.onload = function() {
    callback(xhr.response);
  };

  xhr.send();
}

function displayImage(blob) {
  let objectURL = URL.createObjectURL(blob);

  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
}

loadAsset('coffee.jpg', 'blob', displayImage);
```


创建了一个 displayImage() 函数，它表示作为对象 URL 传递给它的 blob，然后创建一个图像来显示 URL，并将其附加到文档的 `<body>`。随后创建一个 loadAsset() 函数，该函数将回调作为参数，以及要获取的 URL 和内容类型。 它使用 XMLHttpRequest（通常缩写为“XHR”）来获取给定 URL 处的资源，然后将响应传递给回调以执行某些操作。 在这种情况下，回调在将资源传递给回调之前等待 XHR 调用完成下载资源（使用 onload 事件处理程序）。

### Promise
Promise 是现代 Web API 中使用的新型异步代码。 一个很好的例子是 fetch() API，它基本上就像一个现代的、更高效的 XMLHttpRequest 版本。

```js
fetch('products.json').then(function(response) {
  return response.json();
}).then(function(json) {
  let products = json;
  initialize(products);
}).catch(function(err) {
  console.log('Fetch problem: ' + err.message);
});
```

promise 是表示异步操作完成或失败的对象。可以说，它代表了一种中间状态。

像promise这样的异步操作被放入事件队列中，事件队列在主线程完成处理后运行，这样它们就不会阻止后续JavaScript代码的运行。排队操作将尽快完成，然后将结果返回到JavaScript环境。

```js
console.log ('Starting');
let image;

fetch('coffee.jpg').then((response) => {
  console.log('It worked :)')
  return response.blob();
}).then((myBlob) => {
  let objectURL = URL.createObjectURL(myBlob);
  image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
}).catch((error) => {
  console.log('There has been a problem with your fetch operation: ' + error.message);
});

console.log ('All done!');
```

浏览器将会执行代码，看见第一个console.log() 输出(Starting) ，然后创建image 变量。
然后，它将移动到下一行并开始执行fetch()块，但是，因为fetch()是异步执行的，没有阻塞，所以在promise相关代码之后程序继续执行，从而到达最后的console.log()语句(All done!)并将其输出到控制台。

只有当fetch() 块完成运行返回结果给.then() ，我们才最后看到第二个console.log() 消息 (It worked ;))

### 其他的异步JavaScript

setTimeout() 在指定的时间后执行一段代码.

```js
// With a named function
let myGreeting = setTimeout(function sayHi() {
  alert('Hello, Mr. Universe!');
}, 2000)

// With a function defined separately
function sayHi() {
  alert('Hello Mr. Universe!');
}

let myGreeting = setTimeout(sayHi, 2000);
```

清除超时   clearTimeout(myGreeting)


setInterval() 以固定的时间间隔，重复运行一段代码.

```js
function displayTime() {
   let date = new Date();
   let time = date.toLocaleTimeString();
   document.getElementById('demo').textContent = time;
}

const createClock = setInterval(displayTime, 1000);

// 清除intervals
clearInterval(myInterval);
```

requestAnimationFrame() 是一个专门的循环函数，旨在浏览器中高效运行动画。它在浏览器重新加载显示内容之前执行指定的代码块，从而允许动画以适当的帧速率运行，不管其运行的环境如何

```js
let startTime = null;

function draw(timestamp) {
    if(!startTime) {
      startTime = timestamp;
    }

   currentTime = timestamp - startTime;

   // Do something based on current time

   requestAnimationFrame(draw);
}

draw();
// requestAnimationFrame()可用与之对应的cancelAnimationFrame()方法“撤销”
// 该方法以requestAnimationFrame()的返回值为参数，此处我们将该返回值存在变量 rAF 中
cancelAnimationFrame(rAF);
```

## Promise

本质上，Promise 是一个对象，代表操作的中间状态 —— 正如它的单词含义 '承诺' ，它保证在未来可能返回某种结果。虽然 Promise 并不保证操作在何时完成并返回结果，但是它保证当结果可用时，代码能正确处理结果，当结果不可用时，代码同样会被执行，来优雅的处理错误。

设想一个视频聊天应用程序，该程序有一个展示用户的朋友列表的窗口，可以点击朋友旁边的按钮对朋友视频呼叫。

该按钮的处理程序调用 getUserMedia() 来访问用户的摄像头和麦克风。由于 getUserMedia() 必须确保用户具有使用这些设备的权限，并询问用户要使用哪个麦克风和摄像头（或者是否仅进行语音通话，以及其他可能的选项），因此它会产生阻塞，直到用户做出所有的决定，并且摄像头和麦克风都已启用。  getUserMedia() 可能需要很长时间。

```js
function handleCallButton(evt) {
  setStatusMessage("Calling...");
  navigator.mediaDevices.getUserMedia({video: true, audio: true})
    .then(chatStream => {
      selfViewElem.srcObject = chatStream;
      chatStream.getTracks().forEach(track => myPeerConnection.addTrack(track, chatStream));
      setStatusMessage("Connected");
    }).catch(err => {
      setStatusMessage("Failed to connect");
    });
}
```

这个函数在开头调用 setStatusMessage() 来更新状态显示信息"Calling..."， 表示正在尝试通话。接下来调用 getUserMedia()，请求具有视频及音频轨的流，一旦获得这个流，就将其显示在"selfViewElem"的video元素中。接下来将这个流的每个轨道添加到表示与另一个用户的连接的 WebRTC，在这之后，状态显示为"Connected"。

如果getUserMedia()失败，则catch块运行。这使用setStatusMessage()更新状态框以指示发生错误。

这里重要的是getUserMedia()调用几乎立即返回，即使尚未获得相机流。即使handleCallButton()函数向调用它的代码返回结果，当getUserMedia()完成工作时，它也会调用你提供的处理程序。只要应用程序不假设流式传输已经开始，它就可以继续运行。

### promise 基本语法

上一章节中的例子：

```js
console.log ('Starting');
let image;

fetch('coffee.jpg').then((response) => {
  console.log('It worked :)')
  return response.blob();
}).then((myBlob) => {
  let objectURL = URL.createObjectURL(myBlob);
  image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
}).catch((error) => {
  console.log('There has been a problem with your fetch operation: ' + error.message);
});

console.log ('All done!');
```

### promise 术语

1. 创建promise时，它既不是成功也不是失败状态。这个状态叫作pending（待定）。
2. 当promise返回时，称为 resolved（已解决）.
    * 一个成功resolved的promise称为fullfilled（实现）。它返回一个值，可以通过将.then()块链接到promise链的末尾来访问该值。 .then()块中的执行程序函数将包含promise的返回值。

    * 一个不成功resolved的promise被称为rejected（拒绝）了。它返回一个原因（reason），一条错误消息，说明为什么拒绝promise。可以通过将.catch()块链接到promise链的末尾来访问此原因。


### 构建自定义promise

```js
const myPromise = new Promise((resolve, reject) => {
  // do something asynchronous which eventually calls either:
  //
  //   resolve(someValue)        // fulfilled
  // or
  //   reject("failure reason")  // rejected
});
```


## async 和 await

简单来说，async function 和 await关键字是基于promises的语法糖，使异步代码更易于编写和阅读。

首先使用 async 关键字，把它放在函数声明之前，使其成为 async function。使函数返回promise

await放在基于promise的函数之前，它会暂停代码在该行上，直到 promise 完成，然后返回结果值。await 只在异步函数里面才起作用。

简单示例

```js
async function hello() {
  return greeting = await Promise.resolve("Hello");
};

hello().then(alert);
```


async/await 重写promise代码

```js
async function myFetch() {
  let response = await fetch('coffee.jpg');
  let myBlob = await response.blob();

  let objectURL = URL.createObjectURL(myBlob);
  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
}

myFetch()
.catch(e => {
  console.log('There has been a problem with your fetch operation: ' + e.message);
});
```

由于 async 关键字将函数转换为 promise， 上述例子使用 promise 和 await 的混合方式重写，将函数的后半部分抽取到新代码块中。这样可以更灵活：

```js
async function myFetch() {
  let response = await fetch('coffee.jpg');
  return await response.blob();
}

myFetch().then((blob) => {
  let objectURL = URL.createObjectURL(blob);
  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
});
```

### 添加错误处理

```js
async function myFetch() {
  try {
    let response = await fetch('coffee.jpg');
    let myBlob = await response.blob();

    let objectURL = URL.createObjectURL(myBlob);
    let image = document.createElement('img');
    image.src = objectURL;
    document.body.appendChild(image);
  } catch(e) {
    console.log(e);
  }
}

myFetch();

// 混合方式
async function myFetch() {
  let response = await fetch('coffee.jpg');
  return await response.blob();
}

myFetch().then((blob) => {
  let objectURL = URL.createObjectURL(blob);
  let image = document.createElement('img');
  image.src = objectURL;
  document.body.appendChild(image);
})
.catch((e) =>
  console.log(e)
);
```

### async/await 和 Promise.all()

```js
async function fetchAndDecode(url, type) {
  let response = await fetch(url);

  let content;

  if(type === 'blob') {
    content = await response.blob();
  } else if(type === 'text') {
    content = await response.text();
  }

  return content;
}

async function displayContent() {
  let coffee = fetchAndDecode('coffee.jpg', 'blob');
  let tea = fetchAndDecode('tea.jpg', 'blob');
  let description = fetchAndDecode('description.txt', 'text');

  let values = await Promise.all([coffee, tea, description]);

  let objectURL1 = URL.createObjectURL(values[0]);
  let objectURL2 = URL.createObjectURL(values[1]);
  let descText = values[2];

  let image1 = document.createElement('img');
  let image2 = document.createElement('img');
  image1.src = objectURL1;
  image2.src = objectURL2;
  document.body.appendChild(image1);
  document.body.appendChild(image2);

  let para = document.createElement('p');
  para.textContent = descText;
  document.body.appendChild(para);
}

displayContent()
.catch((e) =>
  console.log(e)
);
```

### async/await 和 类方法

还可以在类/对象方法前面添加async，以使它们返回promises，并await它们内部的promises

```js
class Person {
  constructor(first, last, age, gender, interests) {
    this.name = {
      first,
      last
    };
    this.age = age;
    this.gender = gender;
    this.interests = interests;
  }

  async greeting() {
    return await Promise.resolve(`Hi! I'm ${this.name.first}`);
  };

  farewell() {
    console.log(`${this.name.first} has left the building. Bye for now!`);
  };
}

let han = new Person('Han', 'Solo', 25, 'male', ['Smuggling']);

// 实例调用
han.greeting().then(console.log);
```

## Web API



