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

## javaScript 类、面向对象、promise

### 类

>类是用于创建对象的模板，JS中的类建立在原型上，但是也有某些语法和语义未与ES5类相似语义共享。

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

一个类体是一对`{}`中的部分，这是定义类成员的位置，如方法或构造函数。

类声明和类表达式的主体都执行在严格模式下。

构造函数。constructor方法是一个特殊的方法，用于创建和初始化由class创建的对象。一个类只能拥有一个名为constructor的特殊方法，如果类包含多个constructor方法，则会抛出一个syntaxError。一个构造函数可以使用super关键字来调用一个父类的构造函数。

#### 原型方法

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

#### 静态方法
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

p1.displayName;
// undefined
p1.distance;
// undefined

console.log(Point.displayName);
// "Point"
console.log(Point.distance(p1, p2));
// 7.0710678118654755
```

#### 用原型和静态方法绑定this
当调用静态或原型方法时没有指定this的值，那么方法内的this值将被置为`undefined`。即使未设置`"use strict"`,class体内部的代码总是在严格模式下执行。
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
#### super调用超类

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

#### 公共方法
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

# promise

一个promise对象代表一个在promise被创建出时不一定的状态值，它把异步操作最终的成功返回值或者失败原因和相应的处理程序关联起来。使得异步方法可以像同步方法一样返回值：异步方法不会立即返回最终的值，而是会返回一个promise，以便在未来某时把值交给使用者。
一个promise必然处于以下几种状态之一：

* 待定pending：初始状态，既没有被兑现，也没有被拒绝
* 已兑现fulfilled：意味着操作成功完成
* 已拒绝rejected：意味着操作失败

待定状态的promise对象要么会通过一个值被兑现，要么会通过一个原因(错误)被拒绝rejected。当情况发生时，promise的then方法排列的相关处理程序就会被调用。如果promise在相应的处理程序被绑定时就已经被兑现或被拒绝，那么这个处理程序就会被调用，因此在完成异步操作和绑定处理方法之间不会存在竞争状态。

![promise](https://mdn.mozillademos.org/files/8633/promises.png)

## promise的链式调用

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

## promise构造器

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
