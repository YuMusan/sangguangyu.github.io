## Fetch API
Fetch API提供了一个JavaScript接口，用于访问和操纵HTTP管道的部分，例如请求和响应。提供了一个全局fetch()方法，简单合理来跨网络异步获取资源。
fetch规范与jQuery.ajax()主要有两种方式的不同：
* 当接受到一个代表错误的HTTP状态码时，从fetch()返回的promise不会被标记为reject，及时该HTTP响应的状态码是404或500.相反，它会将promise状态标记为resolve(会将resolve的返回值的ok属性设置为false)，仅当网络故障时，或请求被阻止时，才会标记为reject。
* 默认情况下，fetch不会从服务端发送或接受任何cookies，如果站点依赖于用户session，则会导致卫竟然证的请求(要发送cookies，必须设置credentials选项)
-----
### 进行fetch请求

一个基本的fetch请求设置起来很简单。
```js
fetch('http://example.com/movies.json')
  .then(response => response.json())
  .then(data => console.log(data));
```

### 支持的请求参数

fetch()接受第二个可选参数，一个可以控制不同配置的init对象：

```js
// Example POST method implementation:
async function postData(url = '', data = {}) {
  // Default options are marked with *
  const response = await fetch(url, {
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: 'follow', // manual, *follow, error
    referrerPolicy: 'no-referrer', // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    body: JSON.stringify(data) // body data type must match "Content-Type" header
  });
  return response.json(); // parses JSON response into native JavaScript objects
}

postData('https://example.com/answer', { answer: 42 })
  .then(data => {
    console.log(data); // JSON data parsed by `data.json()` call
  });
```

### 发送带凭据的请求

将`credentials:'include'`传递给fetch方法的init对象，浏览器就可以发送包含凭据的请求(即便是跨域源)，
```js
fetch('https://example.com', {
  credentials: 'include'  
})
```

请求URL与调用脚本同一起源处时发送凭据，添加`credentials:'same-origin'`
```js
// The calling script is on the origin 'https://example.com'

fetch('https://example.com', {
  credentials: 'same-origin'  
})
```

请求中不包含凭据，使用`credentials:'omit'`
```js
fetch('https://example.com', {
  credentials: 'omit'  
})
```
### 上传JSON数据

使用fetch POST JSON数据
```js
const data = { username: 'example' };

fetch('https://example.com/profile', {
  method: 'POST', // or 'PUT'
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(data),
})
.then(response => response.json())
.then(data => {
  console.log('Success:', data);
})
.catch((error) => {
  console.error('Error:', error);
});
```
### 上传文件

可以通过`HTML<input type="file" />`元素，FormData()和fetch()上传文件

```js
const formData = new FormData();
const fileField = document.querySelector('input[type="file"]');

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.then(result => {
  console.log('Success:', result);
})
.catch(error => {
  console.error('Error:', error);
});
```
### 上传多个文件
可以通过`HTML<input type="file" mutiple/>`元素，FormData()和fetch()上传文件
```js
const formData = new FormData();
const photos = document.querySelector('input[type="file"][multiple]');

formData.append('title', 'My Vegas Vacation');
for (let i = 0; i < photos.files.length; i++) {
  formData.append(`photos_${i}`, photos.files[i]);
}

fetch('https://example.com/posts', {
  method: 'POST',
  body: formData,
})
.then(response => response.json())
.then(result => {
  console.log('Success:', result);
})
.catch(error => {
  console.error('Error:', error);
});
```
### 检测请求是否成功
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
### 自定义请求对象
除了传给fetch()一个资源的地址，还可以通过Request()构造函数来创建一个request对象，然后再作为参数传给fetch():
```js
const myHeaders = new Headers();

const myRequest = new Request('flowers.jpg', {
  method: 'GET',
  headers: myHeaders,
  mode: 'cors',
  cache: 'default',
});

fetch(myRequest)
  .then(response => response.blob())
  .then(myBlob => {
    myImage.src = URL.createObjectURL(myBlob);
  });
```

Request()和fetch()接受同样的参数。甚至可以传入一个已存在的request对象来创造一个拷贝

    const anotherRequest = new Request(myRequest,myInit)。

因为request和response bodies只能被使用一次(被设计成stream的方式，所以只能被读取一次)。创建一个拷贝就可以再次使用request/response,也可以使用不同的init参数,创建拷贝必须在读取 body 之前进行，而且读取拷贝的 body 也会将原始请求的 body 标记为已读

------

### Headers
使用Headers的接口，可以通过Headers()构造函数来创建一个自己的headers对象

```js
const content = 'Hello World';
const myHeaders = new Headers();
myHeaders.append('Content-Type', 'text/plain');
myHeaders.append('Content-Length', content.length.toString());
myHeaders.append('X-Custom-Header', 'ProcessThisImmediately');
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
const myResponse = Response.error();
try {
  myResponse.headers.set('Origin', 'http://mybank.com');
} catch (e) {
  console.log('Cannot pretend to be a bank!');
}
```

最佳实践是在使用之前检查content type是否正确。

```js
fetch(myRequest)
  .then(response => {
     const contentType = response.headers.get('content-type');
     if (!contentType || !contentType.includes('application/json')) {
       throw new TypeError("Oops, we haven't got JSON!");
     }
     return response.json();
  })
  .then(data => {
      /* process your data further */
  })
  .catch(error => console.error(error));
```
### Response对象

Response实例是在fetch()处理完promises之后返回的。

最常见的response属性有：

* Response.status----整数(默认值是200)，是response的状态码

* Response.statusText----字符串(默认值为OK)，该值与HTTP状态码消息对应

* Response.ok----该属性是来检查response的状态是否在200-299(包括200,299)这个范围内，该属性返回一个Boolean值。

它的实例也可用通过 JavaScript 来创建，但只有在 ServiceWorkers 中使用 respondWith() 方法并提供了一个自定义的 response 来接受 request 时才真正有用:

```js
const myBody = new Blob();

addEventListener('fetch', event => {
  // ServiceWorker intercepting a fetch
  event.respondWith(
    new Response(myBody, {
      headers: { 'Content-Type': 'text/plain' }
    })
  );
});
```
Response() 构造方法接受两个可选参数—— response 的 body 和一个初始化对象（与Request() 所接受的 init 参数类似）

### Body

不管是请求还是响应都能够包含body对象，body也可以是以下任意类型的实例:

* ArrayBuffer、ArrayBufferView、Blob/File、string、URLSearchParams、FormData

Body类定义了以下方法以获取body内容，这些方法都会返回一个被解析后Promise对象和数据。

* arrayBuffer()、blob()、json()、text()、formData()

这些方法让非文本化的数据使用起来更加简单。请求体可以由传入body参数来进行设置。

```js
const form = new FormData(document.getElementById('login-form'));
fetch('/login', {
  method: 'POST',
  body: form
});
```
request和response都会试着自动设置Content-Type，如果没有设置Content-Type值，发送的请求也会自动设值。

### Fetch基本概念

Fetch 的核心在于对 HTTP 接口的抽象，包括 Request，Response，Headers，Body，以及用于初始化异步请求的 global fetch。

---

## Web Workers API

通过使用Web Workers，Web应用程序可以在独立于主线程的后台线程中，运行一个脚本操作。可以在独立线程中执行费时的处理任务，从而允许主线程（通常是UI线程）不会因此被阻塞/放慢。

一个worker是使用一个构造函数创建的一个对象(e.g. Worker()) 运行一个命名的JavaScript文件

主线程和 worker 线程相互之间使用 postMessage() 方法来发送信息, 并且通过 onmessage 这个 event handler来接收信息（传递的信息包含在 Message 这个事件的data属性内) 。数据的交互方式为传递副本，而不是直接共享数据。

在worker内，不能直接操作DOM节点，也不能使用window对象的默认方法和属性。

除了专用 worker 之外，还有一些其他种类的 worker ：

* Shared Workers 可被不同的窗体的多个脚本运行，例如IFrames等，只要这些workers处于同一主域。
* Service Workers 一般作为web应用程序、浏览器和网络之间的代理服务。旨在创建有效的离线体验，拦截网络请求，以及根据网络是否可用采取合适的行动，更新驻留在服务器上的资源
* Chrome Workers 是一种仅适用于firefox的worker
* 音频 Workers可以在网络worker上下文中直接完成脚本化音频处理.

### 专用 Worker

**生成专用worker**

调用Worker() 构造器，指定一个脚本的URL来执行worker线程（main.js）

    const myWorker = new Worker('woker.js')

**消息的接收和发送**

通过postMessage() 方法和onmessage事件处理函数触发workers的方法。

```js
one.onchange = function() {
  myWorker.postMessage([one.value,other.value]);
  console.log('Message posted to worker');
}

other.onchange = function() {
  myWorker.postMessage([one.value,other.value]);
  console.log('Message posted to worker');
}
```
变量one和other代表2个`<input>`元素；它们当中任意一个的值发生改变时，myWorker.postMessage(one.value,other.value])会将这2个值组成数组发送给worker。

在worker中接收到消息后，可以写这样一个事件处理函数代码作为响应（worker.js）

```js
onmessage = function(e) {
  console.log('Message received from main script');
  const workerResult = 'Result: ' + (e.data[0] * e.data[1]);
  console.log('Posting message back to main script');
  postMessage(workerResult);
}
```

onmessage处理函数允许一旦接收到消息就可以执行一些代码，代码中消息本身作为事件的data属性进行使用。

回到主线程，再次使用onmessage以响应worker回传的消息：

```js
myWorker.onmessage = function(e) {
  result.textContent = e.data;
  console.log('Message received from worker');
}
```
获取消息事件的data，并且将它设置为result的textContent，然后就可以看到运算的结果。

```js
// main.js
if (window.Worker) {
  const myWorker = new Worker("worker.js");

  one.onchange = function() {
    myWorker.postMessage([one.value, other.value]);
    console.log('Message posted to worker');
  }

  other.onchange = function() {
    myWorker.postMessage([one.value, other.value]);
    console.log('Message posted to worker');
  }

  myWorker.onmessage = function(e) {
    result.textContent = e.data;
    console.log('Message received from worker');
  }
} else {
  console.log('Your browser doesn\'t support web workers.');
}
```

```js
// worker.js
onmessage = function(e) {
  console.log('Worker: Message received from main script');
  const result = e.data[0] * e.data[1];
  if (isNaN(result)) {
    postMessage('Please write two numbers');
  } else {
    const workerResult = 'Result: ' + result;
    console.log('Worker: Posting message back to main script');
    postMessage(workerResult);
  }
```

**终止worker**

从主线程中立刻终止一个运行中的worker,调用worker的terminate 方法

    worker.terminate();

worker线程会被立即杀死，不会有任何机会让它完成自己的操作或清理工作。

worker线程中，workers 也可以调用close()方法进行关闭

---

## Service Worker API

Service workers 本质上充当 Web 应用程序、浏览器与网络（可用时）之间的代理服务器。这个 API 旨在创建有效的离线体验，它会拦截网络请求并根据网络是否可用来采取适当的动作、更新来自服务器的的资源。它还提供入口以推送通知和访问后台同步 API。

### 特性
Service worker是一个注册在指定源和路径下的事件驱动worker。它采用JavaScript控制关联的页面或者网站，拦截并修改访问和资源请求，细粒度地缓存资源。

Service worker运行在worker上下文，因此它不能访问DOM。相对于驱动应用的主JavaScript线程，它运行在其他线程中，所以不会造成阻塞。它设计为完全异步，同步API（如XHR和localStorage）不能在service worker中使用。

出于安全考量，Service workers只能由HTTPS承载

为了解决丢失网络连接,以及好的统筹机制对资源缓存和自定义的网络请求进行控制

Service Worker 可以使应用先访问本地缓存资源，所以在离线状态时，在没有通过网络接收到更多的数据前，仍可以提供基本的功能


>Firefox Nightly: 访问 about:config 并设置 dom.serviceWorkers.enabled 的值为 true; 重启浏览器
>Chrome Canary: 访问 chrome://flags并开启experimental-web-platform-features; 重启浏览器 
>Opera: 访问 opera://flags 并开启 ServiceWorker 的支持; 重启浏览器。 

### 基本架构

通常遵循以下基本步骤使用service workers

1. service worker URL 通过 serviceWorkerContainer.register() 来获取和注册。

2. 如果注册成功，service worker 就在 ServiceWorkerGlobalScope 环境中运行。这是一个特殊类型的 worker上下文运行环境，与主运行线程（执行脚本）相独立，同时也没有访问 DOM 的能力。

3. service worker 现在可以处理事件了。

4. 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给 service worker 的事件是安装事件(事件中可以开始进行填充 IndexDB和缓存站点资源)

5. 当 oninstall 事件的处理程序执行完毕后，可以认为service worker 安装完成。

6. 当 service worker 安装完成后，会接收到一个激活事件(activate event)。 onactivate 主要用途是清理先前版本的 service worker 脚本中使用的资源。

7. Service Worker 现在可以控制页面，仅在register()成功后的打开的页面。页面起始于有没有 service worker ，且在页面的接下来生命周期内维持这个状态。所以，页面重新加载以让 service worker 获得完全的控制。

![](https://mdn.mozillademos.org/files/12636/sw-lifecycle.png)

service worker 支持的事件：

![](https://mdn.mozillademos.org/files/12632/sw-events.png)


### Service workers demo

这是一个简单的 Star wars Lego 图片库。采用了基于 promise 的函数从一个 JSON 对象来读取图片内容，在显示图片到页面上之前，采用 Ajax 来加载图片。页面非常简单，而且是静态的，但也注册、安装和激活了 service worker，当浏览器支持的时候，它将缓存所有依赖的文件，它可以在离线的时候访问

![](https://mdn.mozillademos.org/files/8243/demo-screenshot.png)

**注册worker**

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw-test/sw.js', { scope: '/sw-test/' }).then(function(reg) {
    // registration worked
    console.log('Registration succeeded. Scope is ' + reg.scope);
  }).catch(function(error) {
    // registration failed
    console.log('Registration failed with ' + error);
  });
}
```

1. 外面的代码块做了一个特性检查，在注册之前确保 service worker 是支持的。

2. 接着，使用 ServiceWorkerContainer.register() 函数来注册站点的 service worker，service worker 只是app 内的一个 JavaScript 文件

3. scope 参数是选填的，可以被用来指定让 service worker 控制的内容的子目录。这个例子指定 '/sw-test/'，表示 app 的 origin 下的所有内容。留空的话，默认值也是这个。

4. .then() 函数链式调用我们的 promise，当 promise resolve 时，里面的代码就会执行。

5. 最后链一个 .catch() 函数，当 promise rejected 才会执行。


![](https://mdn.mozillademos.org/files/12630/important-notes.png)

**填充缓存**
service worker 注册之后，浏览器会尝试为页面或站点安装并激活它
install 事件会在注册完成之后触发。install 事件一般是被用来填充浏览器的离线缓存能力。使用了 Service Worker 的新的标志性的存储 API — cache — 一个 service worker 上的全局对象，它可以存储网络响应发来的资源，并且根据它们的请求来生成key。

```js
this.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',
        '/sw-test/star-wars-logo.jpg',
        '/sw-test/gallery/',
        '/sw-test/gallery/bountyHunters.jpg',
        '/sw-test/gallery/myLittleVader.jpg',
        '/sw-test/gallery/snowTroopers.jpg'
      ]);
    })
  );
});
```

**自定义请求响应**

站点资源缓存后需要告诉 service worker 让它用这些缓存内容来做点什么。

![](https://mdn.mozillademos.org/files/12634/sw-fetch.png)

每次任何被 service worker 控制的资源被请求到时，都会触发 fetch 事件，这些资源包括了指定的 scope 内的文档，和这些文档内引用的其他任何资源

给 service worker 添加一个 fetch 的事件监听器，接着调用 event 上的 respondWith() 方法来劫持 HTTP 响应

```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
  );
});
```
caches.match(event.request) 允许对网络请求的资源和 cache 里可获取的资源进行匹配，查看是否缓存中有相应的资源。

**处理失败的请求**
service worker cache有匹配的资源时， caches.match(event.request) 是非常棒的。但是如果没有匹配资源呢？如果不提供任何错误处理，promise 就会 reject，同时也会出现一个网络错误。

如果在缓存中找不到匹配项，fetch()发起对该资源的默认网络请求，从网络中获取新资源,并对获取到的新资源进行缓存

```js
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((resp) => {
      return resp || fetch(event.request).then((response) => {
        let responseClone = response.clone();
        caches.open('v1').then((cache) => {
          cache.put(event.request, responseClone);
        });
        return response;
      }).catch(() => {
        return caches.match('./sw-test/gallery/myLittleVader.jpg');
      })
    });
  );
});
```

完整的例子:
```js
// sw.js
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',
        '/sw-test/star-wars-logo.jpg',
        '/sw-test/gallery/bountyHunters.jpg',
        '/sw-test/gallery/myLittleVader.jpg',
        '/sw-test/gallery/snowTroopers.jpg'
      ]);
    })
  );
});

self.addEventListener('fetch', function(event) {
  event.respondWith(caches.match(event.request).then(function(response) {
    // caches.match() always resolves
    // but in case of success response will have value
    if (response !== undefined) {
      return response;
    } else {
      return fetch(event.request).then(function (response) {
        // response may be used only once
        // we need to save clone to put one copy in cache
        // and serve second one
        let responseClone = response.clone();
        
        caches.open('v1').then(function (cache) {
          cache.put(event.request, responseClone);
        });
        return response;
      }).catch(function () {
        return caches.match('/sw-test/gallery/myLittleVader.jpg');
      });
    }
  }));
});
```

```js
// app.js
// register service worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw-test/sw.js', { scope: '/sw-test/' }).then(function(reg) {

    if(reg.installing) {
      console.log('Service worker installing');
    } else if(reg.waiting) {
      console.log('Service worker installed');
    } else if(reg.active) {
      console.log('Service worker active');
    }

  }).catch(function(error) {
    // registration failed
    console.log('Registration failed with ' + error);
  });
}
// function for loading each image via XHR

function imgLoad(imgJSON) {
  // return a promise for an image loading
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    request.open('GET', imgJSON.url);
    request.responseType = 'blob';
    request.onload = function() {
      if (request.status == 200) {
        var arrayResponse = [];
        arrayResponse[0] = request.response;
        arrayResponse[1] = imgJSON;
        resolve(arrayResponse);
      } else {
        reject(Error('Image didn\'t load successfully; error code:' + request.statusText));
      }
    };

    request.onerror = function() {
      reject(Error('There was a network error.'));
    };
    // Send the request
    request.send();
  });
}

var imgSection = document.querySelector('section');

window.onload = function() {
  // load each set of image, alt text, name and caption
  for(var i = 0; i<=Gallery.images.length-1; i++) {
    imgLoad(Gallery.images[i]).then(function(arrayResponse) {

      var myImage = document.createElement('img');
      var myFigure = document.createElement('figure');
      var myCaption = document.createElement('caption');
      var imageURL = window.URL.createObjectURL(arrayResponse[0]);

      myImage.src = imageURL;
      myImage.setAttribute('alt', arrayResponse[1].alt);
      myCaption.innerHTML = '<strong>' + arrayResponse[1].name + '</strong>: Taken by ' + arrayResponse[1].credit;

      imgSection.appendChild(myFigure);
      myFigure.appendChild(myImage);
      myFigure.appendChild(myCaption);

    }, function(Error) {
      console.log(Error);
    });
  }
};
```
```js
// image-list.js
var Path = 'gallery/';
var Gallery = { 'images' : [  
  {
    'name'  : 'Darth Vader',
    'alt' : 'A Black Clad warrior lego toy',
    'url': 'gallery/myLittleVader.jpg',
    'credit': '<a href="https://www.flickr.com/photos/legofenris/">legOfenris</a>, published under a <a href="https://creativecommons.org/licenses/by-nc-nd/2.0/">Attribution-NonCommercial-NoDerivs 2.0 Generic</a> license.'
  },
  {
    'name'  : 'Snow Troopers',
    'alt' : 'Two lego solders in white outfits walking across an icy plain',
    'url': 'gallery/snowTroopers.jpg',
    'credit': '<a href="https://www.flickr.com/photos/legofenris/">legOfenris</a>, published under a <a href="https://creativecommons.org/licenses/by-nc-nd/2.0/">Attribution-NonCommercial-NoDerivs 2.0 Generic</a> license.'
  },
  {
    'name'  : 'Bounty Hunters',
    'alt' : 'A group of bounty hunters meeting, aliens and humans in costumes.',
    'url': 'gallery/bountyHunters.jpg',
    'credit': '<a href="https://www.flickr.com/photos/legofenris/">legOfenris</a>, published under a <a href="https://creativecommons.org/licenses/by-nc-nd/2.0/">Attribution-NonCommercial-NoDerivs 2.0 Generic</a> license.'
  },
]};
```

**删除旧缓存**

```js
self.addEventListener('activate', function(event) {
  const cacheWhitelist = ['v2'];
  event.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (cacheWhitelist.indexOf(key) === -1){
          return caches.delete(key);
        }
      }));
    })
  );
});
```
activate事件

----

## WebRTC (Web Real-Time Communications)

WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。WebRTC包含的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。

WebRTC允许在两个设备之间进行实时的对等媒体交换。通过称为信令的发现和协商过程建立连接。

WebRTC是一个完全对等技术，用于实时交换音频、视频和数据，同时提供一个中心警告。必须进行一种发现和媒体格式协商，以使不同网络上的两个设备相互定位的过程。这个过程被称为信令，并涉及两个设备连接到第三个共同商定的服务器。通过这个第三方服务器，这两台设备可以相互定位，并交换协商消息。

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

---

## SSE Server-sent Events

使用server-sent 事件，服务器可以在任何时刻向Web 页面推送数据和信息。这些被推送进来的信息可以在这个页面上作为 Events + data 的形式来处理。

### 从服务器接受事件

创建一个新的包含URL和options参数的EventSource对象。

    const evtSource = new EventSource("//api.example.com/ssedemo.php", { withCredentials: true } );

一旦初始化一个事件源,就可以对 message 事件添加一个处理函数开始监听从服务器发出的消息了:

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
---

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

---

# Web API

## 什么是API
应用程序接口（API，Application Programming Interface）是基于编程语言构建的结构，使开发人员更容易地创建复杂的功能。它们抽象了复杂的代码，并提供一些简单的接口规则直接使用。

比如说，编程来显示一些3D图形，使用以更高级语言编写的API（例如JavaScript或Python）将会比直接编写直接控制计算机的GPU或其他图形功能的低级代码（比如C或C++）来执行操作要容易得多。

### 客户端JavaScript中的API

客户端JavaScript中有很多可用的API — 它们本身并不是JavaScript语言的一部分，却建立在JavaScript语言核心的顶部，为使用JavaScript代码提供额外的超强能力。通常分为两类：

* 浏览器API内置于Web浏览器中，能从浏览器和电脑周边环境中提取数据，并用来做有用的复杂的事情 。

* 第三方API缺省情况下不会内置于浏览器中，通常必须在Web中的某个地方获取代码和信息。


### 常见浏览器API

* 操作文档的API，内置于浏览器中。最明显的例子是DOM（文档对象模型）API，它操作HTML和CSS — 创建、移除以及修改HTML，动态地将新样式应用到您的页面，等等。

* 从服务器获取数据的API，用于更新网页的一小部分。API包括XMLHttpRequest和Fetch API。
* 用于绘制和操作图形的API，目前已被浏览器广泛支持 — 最流行的是以编程方式更新包含在HTML`<canvas>`元素中的像素数据以创建2D和3D场景的Canvas和WebGL。

* 音频和视频API。例如HTMLMediaElement，Web Audio API和WebRTC使用多媒体来做一些非常有趣的事情，比如创建用于播放音频和视频的自定义UI控件，显示字幕视频，从网络摄像机抓取视频，通过画布操纵（见上），或在网络会议中显示在别人的电脑上，或者添加效果到音轨（如增益，失真，平移等） 。

* 设备API，基本上是以对网络应用程序有用的方式操作和检索现代设备硬件中的数据的API。比如访问设备位置数据的地理定位API，可以在地图上标注位置。还包括通过系统通知（Notifications API）或振动硬件（Vibration API）告诉用户Web应用程序有用的更新可用。

* 客户端存储API，如果想创建一个应用程序来保存页面加载之间的状态，甚至让设备在处于脱机状态时可用，那么在客户端存储数据非常有用。例如使用Web Storage API的简单的键 - 值存储以及使用IndexedDB API的更复杂的表格数据存储

### API 如何工作

**基于对象**

例：Geolocation API  由几个简单的对象组成

* Geolocation, 其中包含三种控制地理数据检索的方法

* Position, 表示在给定的时间的相关设备的位置。 — 它包含一个当前位置Coordinates 对象。还包含了一个时间戳,这个时间戳表示获取到位置的时间。

* Coordinates, 其中包含有关设备位置的大量有用数据，包括经纬度，高度，运动速度和运动方向等。

示例：

```js
navigator.geolocation.getCurrentPosition(function(position) {
  var latlng = new google.maps.LatLng(position.coords.latitude,position.coords.longitude);
  var myOptions = {
    zoom: 8,
    center: latlng,
    mapTypeId: google.maps.MapTypeId.TERRAIN,
    disableDefaultUI: true
  }
  var map = new google.maps.Map(document.querySelector("#map_canvas"), myOptions);
});
```

首先要使用 Geolocation.getCurrentPosition() 方法返回设备的当前位置。浏览器的 Geolocation 对象通过调用 Navigator.geolocation 属性来访问.

Geolocation.getCurrentPosition() 方法只有一个必须的参数，这个参数是一个匿名函数，当设备的当前位置被成功取到时，这个函数会运行。 这个函数本身有一个参数，它包含一个表示当前位置数据的 Position 对象。

将Geolocation API与第三方API（Google Maps API）相结合， — 使用它绘制Google地图上由 getCurrentPosition()返回的位置。

引入第三方API

    <script type="text/javascript" src="https://maps.google.com/maps/API/js?key=AIzaSyDDuGt0E5IEGkcE6ZfrKfUtE9Ko_de66pA"></script>

使用该API 

首先使用google.maps.LatLng()构造函数创建一个LatLng对象实例， 该构造函数需要地理定位 Coordinates.latitude 和 Coordinates.longitude值作为参数

    var latlng = new google.maps.LatLng(position.coords.latitude,position.coords.longitude);

该对象实例被设置为 myOptions对象的center属性的值。然后通过调用google.maps.Map()构造函数创建一个对象实例来表示我们的地图， 并传递它两个参数 — 一个参数要渲染地图的 div元素的引用(ID为 map_canvas), 另一个参数是定义的myOptions对象

```js
var myOptions = {
  zoom: 8,
  center: latlng,
  mapTypeId: google.maps.MapTypeId.TERRAIN,
  disableDefaultUI: true
}

var map = new google.maps.Map(document.querySelector("#map_canvas"), myOptions);
```

地图呈现。

**可识别的入口点**

使用API时，应确保知道API入口点的位置。

Geolocation API中是 Navigator.geolocation 属性, 它返回浏览器的 Geolocation 对象，所有有用的地理定位方法都可用。

文档对象模型 (DOM) API，它往往挂在 Document 对象, 或任何想影响的HTML元素的实例

```js
var em = document.createElement('em'); // create a new em element
var para = document.querySelector('p'); // reference an existing p element
em.textContent = 'Hello there!'; // give em some text content
para.appendChild(em); // embed em inside para
```

具有稍微复杂的入口点de API，通常涉及为要编写的API代码创建特定的上下文。

例如：Canvas API的上下文对象是通过获取要绘制的`<canvas>`元素的引用来创建，然后调用它的HTMLCanvasElement.getContext()方法：

```js
var canvas = document.querySelector('canvas');
var ctx = canvas.getContext('2d');

Ball.prototype.draw = function() {
  ctx.beginPath();
  ctx.fillStyle = this.color;
  ctx.arc(this.x, this.y, this.size, 0, 2 * Math.PI);
  ctx.fill();
};
```

然后通过调用内容对象ctx的属性和方法实现想对画布进行的任何操作

**使用事件处理状态的变化**

例如：XMLHttpRequest 对象，它的实例代表一个到服务器的HTTP请求,来取得某种新的资源。onload事件在成功返回时触发包含请求的资源

```js
var requestURL = 'https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json';
var request = new XMLHttpRequest();
request.open('GET', requestURL);
request.responseType = 'json';
request.send();

request.onload = function() {
  var superHeroes = request.response;
  populateHeader(superHeroes);
  showHeroes(superHeroes);
}
```

指定获取的资源的位置，使用XMLHttpRequest() 构造函数创建请求对象的新实例，打开HTTP 的 GET 请求以取得指定资源，指定响应以JSON格式发送，然后发送请求。

然后 onload 处理函数指定如何处理响应。

**适当的地方有额外的安全机制**

WebAPI功能受到与JavaScript和其他Web技术（例如同源政策）相同的安全考虑限制，但是有的API会有额外的安全机制。

比如权限启用请求，通知许可等

## 操作文档

下图表表示直接出现在web页面视图中的浏览器的主要部分：

![](https://mdn.mozillademos.org/files/14557/document-window-navigator.png)

* window是载入浏览器的标签，在JavaScript中用Window对象来表示，使用这个对象可以返回窗口的大小（Window.innerWidth和Window.innerHeight），操作载入窗口的文档，存储客户端上文档的特殊数据（例如使用本地数据库或其他存储设备），为当前窗口绑定event handler，等等。
* navigator表示浏览器存在于web上的状态和标识（即用户代理）。在JavaScript中，用Navigator来表示,可以用这个对象获取一些信息，比如来自用户摄像头的地理信息、用户偏爱的语言、多媒体流等等。
* document（浏览器中用DOM表示）是载入窗口的实际页面，在JavaScript中用Document 对象表示，可以用这个对象来返回和操作文档中HTML和CSS上的信息。

### 文档对象模型 DOM

在浏览器标签中当前载入的文档用文档对象模型来表示。是一个由浏览器生成的“树结构”，使编程语言可以很容易的访问HTML结构

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Simple DOM example</title>
  </head>
  <body>
      <section>
        <img src="dinosaur.png" alt="A red Tyrannosaurus Rex: A two legged dinosaur standing upright like a human, with small arms, and a large head with lots of sharp teeth.">
        <p>Here we will add a link to the <a href="https://www.mozilla.org/">Mozilla homepage</a></p>
      </section>
  </body>
</html>
```

DOM 树

![DOM树](https://mdn.mozillademos.org/files/14559/dom-screenshot.png)

元素节点: 一个元素，存在于DOM中。
根节点: 树中顶层节点，在HTML的情况下，总是一个HTML节点
子节点: 直接位于另一个节点内的节点
后代节点: 位于另一个节点内任意位置的节点。
父节点: 里面有另一个节点的节点。
兄弟节点: DOM树中位于同一等级的节点。
文本节点: 包含文字串的节点

***引用选择节点**

Document.querySelector()
Document.querySelectorAll()
Document.getElementById()
Document.getElementsByTagName()

**创建节点**

Document.createElement() 元素节点
Document.createTextNode() 文本节点

**添加、删除节点**

node.appendChild(otherNode) 添加节点

node.removeChild(otherNode) 删除节点
或者 otherNode.parentNode.removeChild(otherNode) 

**操作样式**

HTMLElement.style 属性，包含文档中每个元素的内联样式信息。可以设置这个对象的属性直接修改元素样式。

```js
para.style.color = 'white';
para.style.backgroundColor = 'black';
para.style.padding = '10px';
para.style.width = '250px';
para.style.textAlign = 'center';
```

或者使用Element.setAttribute()设置属性
它有两个参数，想在元素上设置的属性，为它设置的值。

    para.setAttribute('class', 'highlight');


## 从服务器获取数据

### Ajax  
Asynchronous JavaScript and XML，允许网页直接处理对服务器上可用的特定资源的 HTTP 请求，并在显示之前根据需要对结果数据进行格式化。早期，它倾向于使用XMLHttpRequest 来请求XML数据，现在，使用 XMLHttpRequest 或 Fetch 来请求JSON, 结果是一样的，术语“Ajax”仍然常用于描述这种技术。

![Ajax](https://mdn.mozillademos.org/files/6477/moderne-web-site-architechture@2x.png)

Ajax模型涉及使用 Web API 作为代理来更智能地请求数据，而不仅仅是让浏览器重新加载整个页面。

为了进一步加快速度，一些站点还在用户第一次请求时将资产和数据存储在用户的计算机上，意味着在随后的访问中，使用本地版本而不是每次首次加载页面时下载新副本。内容仅在更新后才从服务器重新加载。

![](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data/web-app-architecture@2x.png)

简单示例：

XMLHttpRequest

```js
let request = new XMLHttpRequest();
request.open('GET', url);
request.responseType = 'text';

request.onload = function() {
  poemDisplay.textContent = request.response;
};

request.send();
```

Fetch

```js
fetch(url).then(function(response) {
  if(response.ok) {
    response.blob().then(function(blob) {
      objectURL = URL.createObjectURL(blob);
      showProduct(objectURL, product);
    });
  } else {
    console.log('Network request for "' + product.name + '" image failed with response ' + response.status + ': ' + response.statusText);
  }
});
```

## 客户端存储

大多数现代的web站点是动态的— 它们在服务端使用各种类型的数据库来存储数据(服务端存储), 之后通过运行服务端（ server-side） 代码来重新获取需要的数据，把其数据插入到静态页面的模板中，并且生成出HTML渲染到用户浏览上。

客户端存储以相同的原理工作，但是在使用上有一些不同。它是由 JavaScript APIs 组成，允许在客户端存储数据 (比如在用户的机器上)，而且可以在需要的时候重新取得需要的数据。

### 传统方法：cookies

它过时、存在各种安全问题，而且无法存储复杂数据

```js
// 设置cookie
const docCookies = document.cookie
// 写入cookie
docCookies.setItem(name, value[, end[, path[, domain[, secure]]]])
// 得到cookie
docCookies.getItem(name)
// 移除cookie
docCookies.removeItem(name[, path],domain)
// 检测cookie
docCookies.hasItem(name)
// 得到cookie列表
docCookies.keys()
```

### 新流派：Web Storage 和 IndexedDB

* Web Storage API 提供了一种非常简单的语法，用于存储和检索较小的、由名称和相应值组成的数据项。当您只需要存储一些简单的数据时，比如用户的名字，用户是否登录，屏幕背景使用了什么颜色等等，这是非常有用的。

* IndexedDB API 为浏览器提供了一个完整的数据库系统来存储复杂的数据。这可以用于存储从完整的用户记录到甚至是复杂的数据类型，如音频或视频文件。

### 未来：Cache API

这个API是为存储特定HTTP请求的响应文件而设计的，它对于像存储离线网站文件这样的事情非常有用，这样网站就可以在没有网络连接的情况下使用。缓存通常与 Service Worker API 组合使用

### 存储简单数据 — web storage

Web Storage API 非常容易使用 — 只需存储简单的 键名/键值对数据 (限制为字符串、数字等类型) 并在需要的时候检索其值。

web storage 数据都包含在浏览器内两个类似于对象的结构中： sessionStorage 和 localStorage。 

* sessionStorage 为每一个给定的源（given origin）维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
* localStorage 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。

Storage.setItem() 存储数据项，两个参数：数据项的名字及其值

Storage.getItem() 获取数据项，一个参数——要检索的数据项的名称

Storage.removeItem() 删除数据项，一个参数——要删除的数据项名称

Storage.clear() 不接受参数，只是简单地清空域名对应的整个存储对象。

```js
localStorage.setItem('name','Chris');

const myName = localStorage.getItem('name');

localStorage.removeItem('name');

localStorage.clear()
```

### 存储复杂数据 — IndexedDB

IndexedDB 是一种底层 API，用于在客户端存储大量的结构化数据（也包括文件/二进制大型对象（blobs））。该 API 使用索引实现对数据的高性能搜索。

IndexedDB 是一个事务型数据库系统,是一个基于 JavaScript 的面向对象数据库。允许存储和检索用键索引的对象；可以存储结构化克隆算法支持的任何对象。
使用：指定数据库模式，打开与数据库的连接，然后检索和更新一系列事务。

**基本模式**

1. 打开数据库。
2. 在数据库中创建一个对象仓库（object store）。
3. 启动一个事务，并发送一个请求来执行一些数据库操作，像增加或提取数据等。
4. 通过监听正确类型的 DOM 事件以等待操作完成。
5. 在操作结果上进行一些操作（可以在 request 对象中找到）

检测是否支持indexedDB

    if (!window.indexedDB) {
        console.log("Your browser doesn't support a stable version of IndexedDB. Such and such feature will not be available.")
}

**打开数据库**

    const request = window.indexedDB.open("MyTestDatabase");

open 请求不会立即打开数据库或者开始一个事务。 对 open() 函数的调用会返回一个可以作为事件来处理的包含 result（成功的话）或者错误值的 IDBOpenDBRequest对象

open 方法接受第二个参数，数据库的版本号。数据库的版本决定了数据库架构，即数据库的对象仓库（object store）和结构。如果数据库不存在，open 操作会创建该数据库，然后 onupgradeneeded 事件被触发，需要在该事件的处理函数中创建数据库模式。如果数据库已经存在，但指定一个更高的数据库版本，会直接触发 onupgradeneeded 事件，允许在处理函数中更新数据库模式。

**生成处理函数**

```js
request.onerror = function(event) {
  // Do something with request.errorCode!
};
request.onsuccess = function(event) {
  // Do something with request.result!
};
```

onsuccess() 和 onerror() 这两个函数哪个被调用呢？如果一切顺利，success 事件会被触发，request 会作为它的 target。 一旦被触发，相关 request 的 onsuccess() 处理函数就会被触发，使用 success 事件作为它的参数。 否则，error 事件会在 request 上被触发。这将会触发使用 error 事件作为参数的 onerror()方法。

现在，假设用户已经允许要创建一个数据库的请求，同时收到触发 success 回调的 success 事件；然后request 是通过调用 indexedDB.open() 产生的， 所以 request.result 是一个 IDBDatabase 的实例，而且希望把它保存下来以供后面使用。

```js
var db;
var request = indexedDB.open("MyTestDatabase");
request.onerror = function(event) {
  alert("Why didn't you allow my web app to use IndexedDB?!");
};
request.onsuccess = function(event) {
  db = event.target.result;
};
```

**错误处理**

错误事件遵循冒泡机制。错误事件都是针对产生这些错误的请求的，然后事件冒泡到事务，然后最终到达数据库对象。避免为所有的请求都增加错误处理程序，可以替代性仅对数据库对象添加一个错误处理程序

```js
db.onerror = function(event) {
  // Generic error handler for all errors targeted at this database's
  // requests!
  console.log("Database error: " + event.target.errorCode);
};
```

打开数据库时常见的可能出现的错误之一是 VER_ERR。这表明存储在磁盘上的数据库的版本高于试图打开的版本。

**创建和更新数据库版本号**

创建一个新的数据库或者增加已存在的数据库的版本号，onupgradeneeded 事件会被触发，IDBVersionChangeEvent 对象会作为参数传递给绑定在 request.result 上的 onversionchange事件处理函数，然后创建该版本需要的对象仓库（object store）。

```js
// 该事件仅在较新的浏览器中实现了
request.onupgradeneeded = function(event) {
  // 保存 IDBDataBase 接口
  const db = event.target.result;

  // 为该数据库创建一个对象仓库
  const objectStore = db.createObjectStore("name", { keyPath: "myKey" });
};
```

**构建数据库**

IndexedDB 使用对象存仓库而不是表，并且一个单独的数据库可以包含任意数量的对象存储空间。每当一个值被存储进一个对象存储空间时，它会被和一个键相关联。

键的提供可以有几种不同的方法，这取决于对象存储空间是使用 key path键路径 还是 key generator键生成器。

还可以创建索引使用被存储的对象的属性的值来查找存储在对象存储空间的值，而不是用对象的键来查找。

简单的例子：

```js
// 客户数据
const customerData = [
  { ssn: "444-44-4444", name: "Bill", age: 35, email: "bill@company.com" },
  { ssn: "555-55-5555", name: "Donna", age: 32, email: "donna@home.org" }
];


// indexedDB存储数据
const dbName = "the_name";

var request = indexedDB.open(dbName, 2);

request.onerror = function(event) {
  // 错误处理
};
request.onupgradeneeded = function(event) {
  var db = event.target.result;

  // 建立一个对象仓库来存储客户的相关信息，选择 ssn 作为键路径（key path）
  // 因为 ssn 可以保证是不重复的
  var objectStore = db.createObjectStore("customers", { keyPath: "ssn" });

  // 建立一个索引来通过姓名来搜索客户。名字可能会重复，所以不能使用 unique 索引
  objectStore.createIndex("name", "name", { unique: false });

  // 使用邮箱建立索引，客户的邮箱不会重复，所以使用 unique 索引
  objectStore.createIndex("email", "email", { unique: true });

  // 使用事务的 oncomplete 事件确保在插入数据前对象仓库已经创建完毕
  objectStore.transaction.oncomplete = function(event) {
    // 将数据保存到新创建的对象仓库
    var customerObjectStore = db.transaction("customers", "readwrite").objectStore("customers");
    customerData.forEach(function(customer) {
      customerObjectStore.add(customer);
    });
  };
};
```

**使用键生成器**

在创建对象仓库时设置 autoIncrement 标记会为该仓库开启键生成器。

```js
// 打开 indexedDB.
var request = indexedDB.open(dbName, 3);

request.onupgradeneeded = function (event) {

    var db = event.target.result;

    // 设置 autoIncrement 标志为 true 来创建一个名为 names 的对象仓库
    var objStore = db.createObjectStore("names", { autoIncrement : true });

    // 因为 names 对象仓库拥有键生成器，所以它的键会自动生成。
    // 被插入的数据可以表示如下：
    // key : 1 => value : "Bill"
    // key : 2 => value : "Donna"
    customerData.forEach(function(customer) {
        objStore.add(customer.name);
    });
};
```

**增加、读取和删除数据**

开启一个事务才能对创建的数据库进行操作。事务来自于数据库对象，而且必须指定这个事务跨越哪些对象仓库。处于一个事务中，就可以对目标对象仓库发出请求。事务提供了三种模式：readonly、readwrite 和 versionchange。

想要修改数据库模式或结构——包括新建或删除对象仓库或索引，只能在 versionchange 事务中才能实现。该事务由一个指定了 version 的 IDBFactory.open 方法启动。

readonly 或 readwrite 模式都可以从已存在的对象仓库里读取记录。但只有在 readwrite 事务中才能修改对象仓库。使用 IDBDatabase.transaction启动一个事务。该方法接受两个参数：storeNames (作用域，想访问的对象仓库的数组），事务模式 mode（readonly 或 readwrite）。该方法返回一个包含 IDBIndex.objectStore方法的事务对象，使用 IDBIndex.objectStore可以访问你的对象仓库。

```js
var objectStore = db.transaction(["customers"], "readwrite").objectStore("customers");
// 增删改查
objectStore.add(item)
objectStore.delete(item)
objectStore.put(item)
objectStore.get(item)
```

**游标、索引**
使用游标遍历对象存储空间中的所有值
使用游标的常见模式是提取出在一个对象存储空间中的所有对象然后把它们添加到一个数组中

```js
var customers = [];

objectStore.openCursor().onsuccess = function(event) {
  var cursor = event.target.result;
  if (cursor) {
    customers.push(cursor.value);
    cursor.continue();
  }
  else {
    alert("以获取所有客户信息: " + customers);
  }
};
```

```js
// 首先，确定已经在 request.onupgradeneeded 中创建了索引:
// objectStore.createIndex("name", "name");

var index = objectStore.index("name");

index.get("Donna").onsuccess = function(event) {
  alert("Donna's SSN is " + event.target.result.ssn);
};
```

## javaScript promise 、类

### promise

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


根据promise规范，简易实现promise对象

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
function Promise(executor) {
    var _this = this
    this.state = PENDING; //状态
    this.value = undefined; //成功结果
    this.reason = undefined; //失败原因

    this.onFulfilled = [];//成功的回调
    this.onRejected = []; //失败的回调

    function resolve(value) {
        if(_this.state === PENDING){
            _this.state = FULFILLED
            _this.value = value
            _this.onFulfilled.forEach(fn => fn(value))
        }
    }
    function reject(reason) {
        if(_this.state === PENDING){
            _this.state = REJECTED
            _this.reason = reason
            _this.onRejected.forEach(fn => fn(reason))
        }
    }
    try {
        executor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}

Promise.prototype.then = function (onFulfilled, onRejected) {
    if(this.state === FULFILLED){
        typeof onFulfilled === 'function' && onFulfilled(this.value)
    }
    if(this.state === REJECTED){
        typeof onRejected === 'function' && onRejected(this.reason)
    }
    if(this.state === PENDING){
        typeof onFulfilled === 'function' && this.onFulfilled.push(onFulfilled)
        typeof onRejected === 'function' && this.onRejected.push(onRejected)
    }
};

// 增强版

Promise.prototype.then = function (onFulfilled, onRejected) {
    var _this = this
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }

    var promise2 = new Promise((resolve, reject)=>{
      if(_this.state === FULFILLED){
        setTimeout(()=>{
            try {
                let x = onFulfilled(_this.value)
                resolvePromise(promise2, x, resolve, reject)
            } catch (error) {
                reject(error)
            }
        })
    } else if(_this.state === REJECTED){
        setTimeout(()=>{
            try {                    
                let x = onRejected(_this.reason)
                resolvePromise(promise2, x ,resolve, reject)
            } catch (error) {
                reject(error)
            }
        })
    } else if(_this.state === PENDING){
        _this.onFulfilled.push(()=>{
            setTimeout(()=>{
                try {                        
                    let x = onFulfilled(_this.value)
                    resolvePromise(promise2, x, resolve, reject)
                } catch (error) {
                    reject(error)
                }
            })
        })
        _this.onRejected.push(()=>{
            setTimeout(()=>{
                try {                        
                    let x = onRejected(_this.reason)
                    resolvePromise(promise2, x ,resolve, reject)
                } catch (error) {
                    reject(error)
                }
            })
        })
    }
    })

    function resolvePromise(promise2, x, resolve, reject){
      if(promise2 === x){
          reject(new TypeError('Chaining cycle'))
      }
      if(x && typeof x === 'object' || typeof x === 'function'){
          let used;
          try {
              let then = x.then
              if(typeof then === 'function'){
                  then.call(x, (y)=>{
                      if (used) return;
                      used = true
                      resolvePromise(promise2, y, resolve, reject)
                  }, (r) =>{
                      if (used) return;
                      used = true
                      reject(r)
                  })
              } else {
                  if (used) return;
                  used = true
                  resolve(x)
              }
          } catch(e){
              if (used) return;
              used = true
              reject(e)
          }
      } else {
          resolve(x)
      }
    } 

    return promise2
}



```

### 类

### 元编程

从ECMAScript 2015 开始，JavaScript 获得了 Proxy 和 Reflect 对象的支持，允许拦截并定义基本语言操作的自定义行为（例如，属性查找，赋值，枚举，函数调用等）。借助这两个对象，可以在 JavaScript 元级别进行编程。

---

# JavaScript

## 语法和数据类型

区分大小写的，并使用 Unicode 字符集
指令被称为语句 （Statement），并用分号（;）进行分隔。
### 注释
```js
// 单行注释

/* 这是一个更长的,
   多行注释
*/
```

### 变量声明

在应用程序中，使用变量来作为值的符号名。变量的名字又叫做标识符，其需要遵守一定的规则。

声明方式：
var 声明一个变量，可选初始化一个值。
let 声明一个块作用域的局部变量，可选初始化一个值。
const 声明一个块作用域的只读常量。

作用域：全局变量，局部变量

变量提升

函数提升

### 常量
关键字 const 创建一个只读的常量
常量不可以通过重新赋值改变其值，也不可以在代码运行时重新声明。它必须被初始化为某个值。

常量的作用域规则与 let 块级作用域变量相同。

### 数据类型

最新的 ECMAScript 标准定义了8种数据类型：

* 7种基本数据类型:
  - Boolean，布尔值，分别是：true 和 false.
  - null，表明 null 值的特殊关键字。
  - undefined，表示变量未赋值时的属性的特殊关键字。
  - Number，数字：整数或浮点数
  - BigInt，任意精度的整数
  - String，字符串，表示文本值的字符序列。
  - Symbol。

* 对象（Object）。

### 字面量

字面量是由语法表达式定义的常量,通过字面量创建相对应数据类型的数据

数组字面量(Array literals)    const a = []

对象字面量(Object literals)   const a = {}

RegExp literals              const regExp = /ab+c/  正则

字符串字面量(String literals)   const a = '晕' 

模板字面量（template literals） const b = `我${a}了`

## 流程控制与错误处理

### 条件判断

if...else语句
当一个逻辑条件为真，if语句执行一个语句。当这个条件为假，else从句执行语句。

```text
if (condition) {
  statement_1;
} else {
  statement_2;
}
```
false、undefined、null、0、NaN、空字符串（""）这些值计算为 false

switch语句

switch 语句允许一个程序求一个表达式的值并且尝试去匹配表达式的值到一个 case 标签。如果匹配成功，这个程序执行相关的语句。

```text
switch (expression) {
   case label_1:
      statements_1
      [break;]
   case label_2:
      statements_2
      [break;]
   ...
   default:
      statements_def
      [break;]
}
```

### 异常处理语句

throw 语句抛出一个异常并且用 try...catch 语句捕获处理它。

## 循环与迭代

for 语句
do...while 语句
while 语句
labeled 语句
break 语句
continue 语句
for...in 语句
for...of 语句

for循环会一直重复执行，直到指定的循环条件为 false

    for ([initialExpression]; [condition]; [incrementExpression])
      statement

do...while 语句一直重复直到指定的条件求值得到假值（false）

    do
      statement
    while (condition);

while 语句只要指定的条件求值为真（true）就会一直执行它的语句块

    while (condition)
      statement

label 提供在程序中其他位置引用它的标识符

    label :
       statement

break 语句来终止循环，switch，或者是链接到 label 语句

continue 语句可以用来继续执行（跳过代码块的剩余部分并进入下一循环）一个 while、do-while、for，或者 label 语句。

for...in 语句循环一个指定的变量来循环一个对象所有可枚举的属性。

for...of 语句在可迭代对象（包括Array、Map、Set、arguments 等等）上创建了一个循环，对值的每一个独特属性调用一次迭代。

## 函数

JavaScript中，函数是执行任务或计算值的语句

### 函数定义

函数定义（也称为函数声明，或函数语句）由一系列的function关键字组成，依次为：

函数的名称。
函数参数列表，包围在括号中并由逗号分隔。
定义函数的 JavaScript 语句，用大括号{}括起来。

```js
function square(number) {
  return number * number;
}
```
定义了函数仅仅是赋予函数以名称并明确函数被调用时该做些什么。调用函数才会以给定的参数真正执行这些动作。

### 函数作用域

函数内定义的变量不能在函数之外的任何地方访问

调用自身的函数称之为递归函数。

JavaScript 允许函数嵌套，并且内部函数可以访问定义在外部函数中的所有变量和函数，以及外部函数能访问的所有变量和函数。

### 闭包

当内部函数以某一种方式被任何一个外部函数作用域访问时，一个闭包就产生了

```js
var pet = function(name) {          //外部函数定义了一个变量"name"
  var getName = function() {
    //内部函数可以访问 外部函数定义的"name"
    return name;
  }
  //返回这个内部函数，从而将其暴露在外部函数作用域
  return getName;
};
myPet = pet("Vivie");

myPet();                            // 返回结果 "Vivie"
```

函数的实际参数会被保存在一个类似数组的arguments对象中

从ECMAScript 6开始，有两个新的类型的参数：默认参数，剩余参数。

默认参数给定参数默认值
剩余参数允许将不确定数量的参数表示为数组

```js
function multiply(multiplier = 2, ...theArgs) {
  return theArgs.map(x => multiplier * x);
}

var arr = multiply(, 1, 2, 3);
console.log(arr); // [2, 4, 6]
```

箭头函数 () => {}  this指向上一级作用域

## 表达式和运算符


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

本质上，Promise 是一个对象，代表操作的中间状态 。虽然 Promise 并不保证操作在何时完成并返回结果，但是它保证当结果可用时，代码能正确处理结果，当结果不可用时，代码同样会被执行，来优雅的处理错误。

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

