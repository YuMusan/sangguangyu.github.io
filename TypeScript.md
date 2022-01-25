# TypeScript

## 日常类型

原语 The primitives ：boolean，number，string

string 表示字符串值，例如“Hello, world”
number 用于像 42 这样的数字。JavaScript 没有针对整数的特殊运行时值，因此没有等效于 int 或 float 的值 - 一切都只是数字
boolean 用于两个值 true 和 false

数组 Array

有两种方式定义数组

元素类型后接上 []，表示此类型元素组成的一个数组

    const list: number[] = [1, 2, 3]

使用数组泛型 Array<元素类型>

    const list: Array<number> = [1, 2, 3]

任意值  any
TypeScript 也有一个特殊的类型，any，不希望某个特定的值导致类型检查错误时，可以使用它。

    let notSure: any = 4;
    notSure = "maybe a string instead";
    notSure = false; // okay, definitely a boolean

空值  void
表示没有任何类型，一般用于函数没有返回值时

    function warnUser(): void {
      alert("This is my warning message");
    }

Null 和 Undefined

TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。

**变量的类型注释**

使用 const、var 或 let 声明变量时，可以选择添加类型注释以显式指定变量的类型：

    let myName: string = "Alice"

通常不必给变量添加类型注释，TypeScript 会尽可能地尝试自动推断代码中的类型。

**函数 Function**

函数是在 JavaScript 中传递数据的主要方式。 TypeScript 允许指定函数的输入和输出值的类型。

声明函数时，可以在每个参数后面加上类型注解，声明函数接受哪些类型的参数。 参数类型注释在参数名称之后：

```ts
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}
```
还可以添加返回类型注释。 返回类型注释出现在参数列表之后

```ts
function getFavoriteNumber(): number {
  return 26;
}
```
通常不需要返回类型注释，因为 TypeScript 将根据其返回语句推断函数的返回类型。


匿名函数与函数声明有点不同。 当一个函数出现在 TypeScript 可以确定如何调用它的地方时，该函数的参数会自动被赋予类型

```ts
const names = ["Alice", "Bob", "Eve"];
 
// Contextual typing for function
names.forEach(function (s) {
  console.log(s.toUppercase());
});
 
// Contextual typing also applies to arrow functions
names.forEach((s) => {
  console.log(s.toUppercase());
});
```

尽管参数 s 没有类型注释，TypeScript 还是使用了 forEach 函数的类型以及推断的数组类型来确定 s 将具有的类型。

这个过程称为上下文类型，因为函数发生的上下文告知它应该具有什么类型。

**对象类型**

除了原语，最常见的类型是对象类型。要定义对象类型，只需列出其属性及其类型。

```ts
// 参数的类型注解是对象类型
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```

对象类型还可以指定它们的部分或全部属性是可选的。在属性名称之后添加 ? ：

```ts
function printName(obj: { first: string; last?: string }) {
  // ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```
**联合类型**

联合类型是由两种或多种其他类型组成的类型，表示可能是这些类型中的任何一种的值。

```ts
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
// Error
printId({ myID: 22342 });
```
TypeScript 只有在对联合体的每个成员都有效的情况下才允许操作。 例如，如果联合 string | number，不能使用仅在字符串上可用的方法

解决方法是用代码缩小联合

```ts
function printId(id: number | string) {
  if (typeof id === "string") {
    // In this branch, id is of type 'string'
    console.log(id.toUpperCase());
  } else {
    // Here, id is of type 'number'
    console.log(id);
  }
```

**类型别名 type**

通常希望多次使用同一个类型并用一个名称引用它。

```ts
type Point = {
  x: number;
  y: number;
};
 
// Exactly the same as the earlier example
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });

// 类型别名可以命名联合类型
 type ID = number | string;
```

当使用别名时，已经编写过别名的类型。

**接口 interface**

接口声明是命名对象类型的另一种方式：

```ts
interface Point {
  x: number;
  y: number;
}
 
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

就像使用类型别名时一样，该示例像使用匿名对象类型一样工作。 TypeScript 只关心传递给 printCoord 的值的结构——是否具有预期的属性。 只关心类型的结构和功能是称 TypeScript 为结构类型类型系统的原因。

* 类型别名可能不参与声明合并，但接口可以。
* 接口只能用于声明对象的形状，不能重命名原语。
* 接口名称将始终以其原始形式出现在错误消息中，但仅在按名称使用时才出现。

**类型断言**

有时候会遇到这样的情况，比TypeScript更了解某个值的详细信息。 通常发生在知道一个实体具有比它现有类型更确切的类型。

通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

类型断言有两种形式，其一是'<>'语法

    let someValue: any = "this is a string";
    let strLength: number = (<string>someValue).length;

另一个是 as 语法

    let someValue: any = "this is a string";
    let strLength: number = (someValue as string).length;

不过在TypeScript里使用JSX时，只有as语法断言是被允许的。

非空断言运算符 !

TypeScript 还有一种特殊的语法，可以在不进行任何显式检查的情况下从类型中删除 null 和 undefined。 写作 ！ 在任何表达式实际上是值不是 null 或 undefined 的类型断言之后：

```ts
function liveDangerously(x?: number | null) {
  // No error
  console.log(x!.toFixed());
}
```


## 缩小 Narrowing

函数中，在给定参数有联合类型时，如果没有先明确检查填充参数的类型，会抛出一些错误。

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```
if 检查中，TypeScript 看到 typeof padding === "number" 并将其理解为一种称为类型保护的特殊代码形式。 TypeScript 遵循程序可以采用的可能执行路径来分析给定位置的值的最具体的可能类型。 它着眼于这些特殊检查（称为类型保护）和赋值，将类型精炼为比声明的更具体的类型的过程称为缩小。

**typeof 类型警卫**

typeof 会返回以下值：

"string"
"number"
"bigint"
"boolean"
"symbol"
"undefined"
"object"
"function"

TypeScript 可以理解它来缩小不同分支中的类型。在 TypeScript 中，检查 typeof 返回的值是一种类型保护。

真实性缩小

&&  || !  

值判断 0、NaN、"" (the empty string)、0n (the bigint version of zero)、null、undefined all coerce to false,

平等缩小

===, !==, ==, and !=

运算符 in  用于确定对象是否具有带名称的属性。 TypeScript 将这一点视为缩小潜在类型的一种方式。

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
 
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    return animal.swim();
  }
 
  return animal.fly();
}
```
运算符 instensof 来检查一个值是否是另一个值的“实例”。

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
  } else {
    console.log(x.toUpperCase());
  }
}
```

## 函数类型

函数是任何应用程序的基本构建块，无论它们是本地函数、从另一个模块导入的函数，还是类中的方法。来，学习如何编写描述函数的类型。

描述函数的最简单方法是使用函数类型表达式。 这些类型在语法上类似于箭头函数：

```ts
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}
 
function printToConsole(s: string) {
  console.log(s);
}
 
greeter(printToConsole);
```

语法 (a: string) => void 的意思是“带有一个参数的函数，参数名为 a，类型为字符串，函数没有返回值”。 就像函数声明一样，如果未指定参数类型，则它隐含为 any

也可以使用类型别名来命名函数类型：

```ts
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
  // ...
}
```

**通用方式**

通常会编写一个函数，其中输入的类型与输出的类型相关，或者两个输入的类型以某种方式相关。

在 TypeScript 中，描述两个值之间的对应关系时，会使用泛型。

```ts
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

通过向该函数添加类型参数 Type 并在两个地方使用它，我们在函数的输入（数组）和输出（返回值）之间创建了一个链接。调用它时，会出现一个更具体的类型

```ts
// s is of type 'string'
const s = firstElement(["a", "b", "c"]);
// n is of type 'number'
const n = firstElement([1, 2, 3]);
// u is of type undefined
const u = firstElement([]);
```

上面示例中，type不需要特别指定。 类型由 TypeScript 推断，自动选择的。

**多个类型参数**

```ts
function map<Input, Output>(arr: Input[], func: (arg: Input) => Output): Output[] {
  return arr.map(func);
}
 
// Parameter 'n' is of type 'string'
// 'parsed' is of type 'number[]'
const parsed = map(["1", "2", "3"], (n) => parseInt(n));
```

此示例中，TypeScript 可以根据函数表达式的返回值（数字）推断输入类型参数的类型（从给定的字符串数组）和输出类型参数

**约束**

如果关联两个值，但只能对某个值的子集进行操作，这种情况下，使用约束来限制类型参数可以接受的类型种类。
通过编写 extends 子句将类型参数限制

```ts
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
 
// longerArray is of type 'number[]'
const longerArray = longest([1, 2], [1, 2, 3]);
// longerString is of type 'alice' | 'bob'
const longerString = longest("alice", "bob");
// Error! Numbers don't have a 'length' property
const notOK = longest(10, 100);
```

将 Type 限制为 {length: number }，就可以访问 a 和 b 参数的 .length 属性。 如果没有类型约束，将无法访问这些属性

**指定类型参数**

TypeScript 通常可以在泛型调用中推断出预期的类型参数，但并非总是如此。 假如，编写了一个函数来组合两个数组：

```ts
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2);
}

const arr = combine([1, 2, 3], ["hello"]);
// error: Type 'string' is not assignable to type 'number'.
// 需要手动指定类型
const arr = combine<string | number>([1, 2, 3], ["hello"]);
```

**编写良好泛型函数**

编写泛型函数很有趣，而且很容易被类型参数迷住。 有太多类型参数或在不需要它们的地方使用约束会使推理不太成功，让函数的调用者感到沮丧

下推类型参数

```ts
function firstElement1<Type>(arr: Type[]) {
  return arr[0];
}
 
function firstElement2<Type extends any[]>(arr: Type) {
  return arr[0];
}
 
// a: number (good)
const a = firstElement1([1, 2, 3]);
// b: any (bad)
const b = firstElement2([1, 2, 3]);
```

乍一看，这些似乎相同，但 firstElement1 是编写此函数的更好方法。 它推断的返回类型是 Type，但 firstElement2 的推断返回类型是 any，因为 TypeScript 必须使用约束类型解析 arr[0] 表达式，而不是在调用期间“等待”解析元素。

规则：如果可能，使用类型参数本身而不是约束它

使用更少的类型参数

```ts
function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {
  return arr.filter(func);
}
 
function filter2<Type, Func extends (arg: Type) => boolean>(
  arr: Type[],
  func: Func
): Type[] {
  return arr.filter(func);
}
```

这里创建了不关联两个值的类型参数 Func。 是一个危险信号，因为想要指定类型参数的调用者必须无缘无故地手动指定一个额外的类型参数。 Func 没有做任何事情，只是让函数更难阅读和推理！

规则：始终使用尽可能少的类型参数

类型参数应该出现两次

有时函数可能不需要是泛型的：

```ts
function greet<Str extends string>(s: Str) {
  console.log("Hello, " + s);
}
 
greet("world");
// better func
function greet(s: string) {
  console.log("Hello, " + s);
}
```

请记住，类型参数用于关联多个值的类型。 如果一个类型参数只在函数签名中使用一次，它就没有任何关联。

规则：如果一个类型参数只出现在一个位置，如果你真的需要它，强烈重新考虑

**可选参数**

JavaScript 中的函数通常采用可变数量的参数。 例如， number 的 toFixed 方法采用可选的位数：

```ts
function f(n: number) {
  console.log(n.toFixed()); // 0 arguments
  console.log(n.toFixed(3)); // 1 argument
}
```

可以在 TypeScript 中通过使用 ? 将参数标记为可选来对此进行建模

```ts
function f(x?: number) {
  // ...
}
f(); // OK
f(10); // OK
```

回调中的可选参数

了解过可选参数和函数类型表达式后，在编写调用回调的函数时很容易犯以下错误：

```ts
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i);
  }
}
```

编写 index? 作为一个可选参数，通常是希望这两个调用都是合法的

```ts
myForEach([1, 2, 3], (a) => console.log(a));
myForEach([1, 2, 3], (a, i) => console.log(a, i));
```

为回调编写函数类型时，切勿编写可选参数，除非打算在不传递该参数的情况下调用该函数

**函数重载**

在 TypeScript 中，可以通过编写重载签名来指定一个可以以不同方式调用的函数。 为此，需要编写一些函数签名（通常是两个或更多），然后是函数的主体

```ts
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);
// No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
```
