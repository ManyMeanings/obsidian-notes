### js 有哪些数据类型？✨

原始数据类型有 7 种：`Number`、`String`、`Boolean`、`Undefined`、`Null`、`Symbol`、`BigInt`
引用数据类型：`Object`、`Function`、`Array` ...

#### 它们的存储方式是怎样的？

原始数据类型存储在栈 (stack) 中，占据空间小、大小固定。

引用数据类型存储在堆 (heap) 中，占据空间大、大小不固定。引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。

#### null 和 undefined 的区别?

null 表示一个 " 无 " 的对象，也就是该处不应该有值；而 undefined 表示未定义。 在转换为数字时结果不同，Number(null) 为 0，而 undefined 为 NaN。

使用场景上：

- null：
    - 作为函数的参数，表示该函数的参数不是对象
    - 作为对象原型链的终点
- undefined:
    - 变量被声明了，但没有赋值时，就等于 undefined
    - 调用函数时，应该提供的参数没有提供，该参数等于 undefined
    - 对象没有赋值属性，该属性的值为 undefined
    - 函数没有返回值时，默认返回 undefined

### 判断数据类型有哪些方式？

- `Object.prototype.toString.call()`
	- 可以完美判断类型：`[object Array]`、`[object Number]` ...
	- 原理：返回当前调用者的对象类型（*所有数据类型都是对象的一种类型*）
	- 为什么不直接用 toString：每个对象类都重写了 toString 方法
- `typeof`
	- `object`、`number`、`bigint`、`function`
	- 基本数据类型返回对应值（*Null 返回 object*），引用数据类型返回 object 或 function（*数组返回 object*）
- `instanceof`
	- 只能正确判断引用数据类型
	- 原理：判断在其原型链中能否找到该构造函数的原型
- `constructor`
	- `('').constructor === String // true`
	- 访问对象实例的构造函数
	- 无法用于 null 和 undefined；如果手动改变了对象的原型，结果就不准确了

### `Object.is()` 与比较操作符 `===`、`==` 的区别?

- `==` 如果两边的类型不一致，则会进行类型转换后再进行比较。
- `===` 如果两边的类型不一致，直接返回 false
- `Object.is()` 大部分情况与全等结果相同，不同在于：**正负 0 比较不相等，两个 NaN 比较相等**

#### Object.is 原理

```js
const is = (val1, val2) => {
  if (val1 === val2) {
    // 处理 +0 === -0
    // +Infinity !== -Infinity
    return x !== 0 || 1 / x === 1 / y;
  } else {
    // 处理NaN，利用NaN !== NaN 的特型
    return x !== x && y !== y;
  }
};
```

#### 有哪些假值？

6 个假值：`undefined`、`null`、`NaN`、`0`（包括正负）、`""`（空字符串）和 `false`

### JavaScript 的类型转换机制？✨

#### 显式转换

**Number**

```js
// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324
// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN
// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0

// 对象：通常转换成NaN(除了只包含单个数值的数组)
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5

// parseInt 逐个解析字符，遇到不能转换的字符就停下来
parseInt('32a3') //32
```

**String**

```js
// 数值：转为相应的字符串
String(1) // "1"

//布尔值：true转为字符串"true"，false转为字符串"false"
String(true) // "true"

//undefined：转为字符串"undefined"
String(undefined) // "undefined"

//null：转为字符串"null"
String(null) // "null"

//对象
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```

**Boolean**

6 个假值：undefined，null，(+-)0， NaN， ''， false

```js
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```

#### 隐式转换

可以归纳为两种情况发生隐式转换的场景：

- 比较运算（`==`、`!=`、`>`、`<`）、`if`、`while` 需要布尔值地方
- 算术运算（`+`、`-`、`*`、`/`、`%`）

**`==` 操作符**

- 如果任一操作数是布尔值，则将其转换为数值再比较是否相等
- 如果一个操作数是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等
- 如果一个操作数是对象，另一个操作数不是，则调用对象的 `valueOf()` 方法取得其原始值，再根据前面的规则进行比较
- ==null 和 undefined 相等，与其他比较都是 false==
- 如果有任一操作数是 `NaN` ，则相等操作符返回 `false`
- 两个都为引用类型，则比较它们是否指向同一个对象

**`+` 操作符**

- 当一侧为 `String` 类型，被识别为字符串拼接，并会优先将另一侧转换为字符串类型。
- 当一侧为 `Number` 类型，另一侧为原始类型，则将原始类型转换为 `Number` 类型。
- 当一侧为 `Number` 类型，另一侧为引用类型，将引用类型和 `Number` 类型转换成字符串后拼接。

**`<` 和 `>` 比较符**

- 如果两边都是字符串，则比较字母表顺序
- 其他情况下，转换为数字再比较

 **`if` `while`**

- 转为布尔值

**`-`、`*`、`/` 操作符**

- 都转换为数字

#### 一些违反直觉的比较结果

```js
'' == '0' // false
0 == '' // true
0 == '0' // true

false == 'false' // false
false == '0' // true

false == undefined // false
false == null // false
null == undefined // true

' \t\r\n' == 0 // true
```

### 什么是 JavaScript 中的包装类型?

在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象。

#### 包装类型和基本类型的相互转换

```js
let a = 'abc'
let b = Object(a) // String(a)
let c = b.valueOf() // 'abc'
```

### 对 this 对象的理解？✨

-  **全局环境**
	- 浏览器环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象 `window`
	- node 环境：无论是否在严格模式下，在全局执行环境中（在任何函数体外部），this 都是空对象 `{}`
- **是否是 new 绑定**
	- 构造函数未返回对象的情况下，this 指向实例对象
- **是否使用 apply，call，bind 进行显式绑定**
	- this 指向第一个参数
	- 非严格模式下，如果第一个参数为 undefined 或 null，this 指向全局对象
- **隐式绑定**
	- **方法调用** 哪个对象调用方法，函数里面的 this 就指向哪个对象（*与方法的声明位置无关*）
	- **事件** 绑定函数的形式调用时，this 指向绑定事件的对象；
- **默认绑定**
	- 没有明确的对象调用，非严格模式下 this 指向全局对象，严格模式下为 undefined，通常是独立函数调用，如 setTimeout 等
- **箭头函数**
	- 箭头函数没有自己的 this，继承外层上下文绑定的 this（*this 指向取决于声明的位置，而非取决于调用位置*）

### apply，call，bind 三者的区别？✨

- 三者都可以改变函数的 this 对象指向。
- 三者第一个参数都是 this 要指向的对象，如果如果没有这个参数或参数为 undefined 或 null，则默认指向全局 window。
- 三者都可以传参，但是 apply 是数组，而 call 是参数列表，且 apply 和 call 是一次性传入参数，而 bind 可以分为多次传入。
- bind 是返回绑定 this 之后的函数，便于稍后调用；apply 、call 则是立即执行 。

### 箭头函数与普通函数的区别？

- 箭头函数没有 `arguments` 
- 箭头函数不能用作构造函数，不能用 new 调用
- 箭头函数没有独立的 `this`，无法通过 apply，call，bind 进行显式绑定
- 箭头函数不能作为生成器函数创建
- 不建议使用的场景：对象的方法、对象的原型的方法、监听事件

### 扩展运算符的使用场景？

**对象**

- 取出参数对象中的所有可枚举属性，拷贝到当前对象之中（*浅拷贝*）
- 可用于对象合并、修改对象的部分属性

**数组**

- 将一个数组转为用逗号分隔的参数序列
- 可用于复制数组、合并数组、函数传参、将 Iterator 接口的对象转换为数组

### 什么是原型和原型链？✨

**提纲**：隐式原型、显式原型、两者关系

- 每个对象都有一个 `__proto__` 属性，称为隐式原型，它的作用是找父级
- 每个构造函数除了有隐式原型外，还有一个 `prototype` 属性，称为显式原型
- 显式原型定义了所有构造函数创建的实例所共享的属性和方法
- 两者的关系是：**隐式原型指向创建这个对象的构造函数的显式原型**
- **原型链** 对象的原型也有自己的原型，构成一条原型链，原型链的末尾为 null。当访问一个对象的属性时，如果该对象本身不存在这个属性，那么就会沿着原型链向上查找，直到找到该属性或达到原型链的末尾（*null*）为止

#### 如何获取对象的原型？

```js
// 标准写法
Object.getPrototypeOf(obj)
// 不标准写法，但是浏览器都实现了
obj.__proto__
// 隐式原型指向创建这个对象的构造函数的显式原型
obj.constructor.prototype
// 关系
a.constructor = a.__proto__.constructor = A.prototype.constructor = A
```

### 什么是执行上下文？✨

执行上下文就是当前 JavaScript 代码被解析和执行时所在环境的抽象概念， JavaScript 中运行任何的代码都是在执行上下文中运行。

执行上下文类型分为：

- 全局执行上下文
- 函数执行上下文

执行上下文创建过程中，需要做以下几件事:

1. 创建变量对象：初始化函数的参数 arguments，提升函数声明和变量声明。
2. 创建作用域链
3. 确定 this 的值：this 值在执行中确定

#### 什么是执行栈？

执行栈，也叫调用栈，具有 LIFO（后进先出）结构，**用于存储在代码执行期间创建的所有执行上下文**

一个例子：

![[Pasted image 20240225161838.png]]

- 创建全局上下文请压入执行栈
- `first` 函数被调用，创建函数执行上下文并压入栈
- 执行 `first` 函数过程遇到 `second` 函数，再创建一个函数执行上下文并压入栈
- `second` 函数执行完毕，对应的函数执行上下文被推出执行栈，执行下一个执行上下文 `first` 函数
- `first` 函数执行完毕，对应的函数执行上下文也被推出栈中，然后执行全局上下文
- 所有代码执行完毕，全局上下文也会被推出栈中，程序结束

### 什么是作用域？✨

作用域决定了代码区块中变量和函数的可见性

三种作用域：

- 全局作用域：任何不在函数中或是大括号中声明的变量，可以在程序的任意位置访问
- 函数作用域：在函数中声明的变量，只能在函数内部访问
- 块级作用域：在大括号中使用 `let` 和 `const` 声明的变量，在大括号之外不能访问

#### 什么是作用域链？

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级 (词法层面上的父级) 执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

当代码在一个环境中创建时，会创建变量对象的一个作用域链（scope chain）来保存执行环境有权访问的变量和函数。作用域第一个对象始终是当前执行代码所在环境的变量对象（VO）。如果是函数执行阶段，那么将其 activation object（AO）作为作用域链第一个对象，第二个对象是上级函数的执行上下文 AO，下一个对象依次类推。

#### 什么是 VO 和 AO？

VO 对应的是函数创建阶段，JS 解析引擎进行预解析时，所有的变量和函数的声明，统称为 Variable Object。该变量与执行上下文相关，知道自己的数据存储在哪里，并且知道如何访问。VO 是一个与执行上下文相关的特殊对象，它存储着在上下文中声明的以下内容：

- 变量 (var, 变量声明);
- 函数声明 (FunctionDeclaration, 缩写为 FD);
- 函数的形参

AO 对应的是函数执行阶段，当函数被调用执行时，会建立一个执行上下文，该执行上下文包含了函数所需的所有变量，该变量共同组成了一个新的对象就是 Activetion Object。该对象包含了：

- 函数的所有局部变量
- 函数的所有命名参数
- 函数的参数集合
- 函数的 this 指向

#### 什么是词法作用域？

词法作用域也叫静态作用域，作用域在词法化阶段（通常是编译阶段）确定而非执行阶段确定。使用词法作用域，我们可以仅仅看源代码就可以确定一个变量的作用范围，但如果是动态作用域，代码执行之前我们没法确定变量的作用范围。

#### 什么是词法环境？

词法环境是 Javascript 引擎用来存储变量和对象引用的地方。只有当作用域的代码被执行的时候，引擎才会为作用域创建一个新的词法环境。词法环境还会记录所引用的外部词法环境 (即外部作用域)。

```js
lexicalEnvironment = { 
a: 25, 
obj: <ref. to the object>
outer: <outer lexical environemt> 
}
```

### 什么是变量提升？

- `var` 变量会进行声明提前，在赋值前可以访问到这个变量，值是 `undefined`
- 函数声明也会被提前，而且优先级比 `var` 高
- 块级作用域也有“变量提升”，但是会形成“暂时性死区”，在声明前访问会直接报错

```js
var x = 1;
if(true) {
  // 暂时性死区
  console.log(x);
  
  let x = 2;
}
```

### let 、 const 、var 的区别？✨

- **变量提升**
	- `var` 声明的变量存在变量提升，即变量可以在声明之前调用，值为 `undefined`
	- `let` 和 `const` 不存在变量提升，它们所声明的变量在声明前使用会报错
- **暂时性死区**
	- `var` 不存在暂时性死区
	- `let` 和 `const` 存在暂时性死区，只有等到声明变量的那一行代码出现，才可以获取和使用该变量
- **块级作用域**
	- `var` 不存在块级作用域
	- `let` 和 `const` 存在块级作用域
- **重复声明**
	- `var` 允许重复声明变量
	- `let` 和 `const` 在同一作用域不允许重复声明变量
- **修改声明的变量**
	- `var` 和 `let` 可以
	- `const` 声明一个只读的常量。一旦声明，常量的值就不能改变
- **使用优先级**
	- 能用 `const` 的情况尽量使用 `const`，其他情况下大多数使用 `let`，避免使用 `var`

### 什么是闭包？✨

**什么是闭包**：闭包使函数可以访问其父函数的所有变量，即使在父函数返回之后也是如此。

**为什么要有闭包**：如果没有闭包，函数在执行栈上执行，在栈内存中存储局部变量，执行完后出栈，栈上保存的局部变量就无法访问了。

**闭包的实现**：js 引擎会将函数闭包所需的数据，构成一个 `Closure` 对象存储在堆里，然后函数引用这个对象，再次调用时，函数会去访问 `Closure` 对象里的数据。

#### 闭包的使用场景？

闭包可以保存局部变量，且不影响全局变量（*创建私有变量*）。使用闭包时要注意内存泄漏问题，用完就解绑定相关变量。

- 高阶函数：节流防抖、科里化
- 模拟私有变量
- 自执行函数

### 什么是事件循环？✨

JS 是单线程的，主线程在运行时会产生执行栈，栈中的代码调用某些异步 API 时会在任务队列中添加事件，栈中的代码执行完毕后，就会读取任务队列中的事件，去执行事件对应的回调函数，如此循环往复，形成事件循环机制。

1. JavaScript 中的任务分为**同步任务和异步任务**，异步任务又包括**宏任务和微任务**
2. 同步任务按顺序在执行栈中执行，遇到异步任务就添加到宏任务队列或微任务队列等待执行
3. 同步任务执行完成后，从宏任务队列取出第一个任务执行
4. 执行完成后，按顺序执行所有的微任务（*执行微任务过程中产生的新的微任务在当前的循环中继续执行*）
5. 一次事件循环会处理一个宏任务和所有产生的微任务（*第一次循环中初始化代码就是一次宏任务，因此会先执行完所有的微任务再执行代码中的下一个宏任务*）

#### 什么是宏任务和微任务？

- 宏任务：需要特定的异步线程去执行，有明确的异步任务去执行，有回调；
	- script （主代码块）、`setTimeout` 、`setInterval` 、`setImmediate` 、I/O 、UI rendering
- 微任务：不需要特定的异步线程去执行，没有明确的异步任务去执行，只有回调；
	- Promise、queueMicrotask、MutaionObserver、process.nextTick(Node.js 环境)；

#### Node.js 的事件循环有什么区别？

Node.js 中宏任务分成了几种类型，并且放在了不同的 task queue 里。不同的 task queue 在执行顺序上也有区别，微任务放在了每个 task queue 的末尾：

- `setTimeout/setInterval` 属于 timers 类型；
- `setImmediate` 属于 check 类型；
- socket 的 close 事件属于 close callbacks 类型；
- 其他 MacroTask 都属于 poll 类型。
- `process.nextTick` 本质上属于 MicroTask，但是它先于所有其他 MicroTask 执行；
- 所有 MicroTask 的执行时机在不同类型的 MacroTask 切换后。
- idle/prepare 仅供内部调用，我们可以忽略。
- pending callbacks 不太常见，我们也可以忽略。

```
   ┌───────────────────────┐
┌─>│        timers         │<————— 执行 setTimeout()、setInterval() 的回调
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
│  │     pending callbacks │<————— 执行由上一个 Tick 延迟下来的 I/O 回调（待完善，可忽略）
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
│  │     idle, prepare     │<————— 内部调用（可忽略）
│  └──────────┬────────────┘     
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
|             |                   ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │ - (执行几乎所有的回调，除了 close callbacks、timers、setImmediate)
│  │         poll          │<─────┤  connections, │ 
│  └──────────┬────────────┘      │   data, etc.  │ 
│             |                   |               | 
|             |                   └───────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
|  ┌──────────┴────────────┐      
│  │        check          │<————— setImmediate() 的回调将会在这个阶段执行
│  └──────────┬────────────┘
|             |<-- 执行所有 Next Tick Queue 以及 MicroTask Queue 的回调
│  ┌──────────┴────────────┐
└──┤    close callbacks    │<————— socket.on('close', ...)
   └───────────────────────┘

```

### 讲一下你对 JS 异步的理解？

**callback**

回调函数是最早被广泛使用的一种方式。通过将耗时的操作封装在一个函数中，并将该函数作为参数传递给异步操作，异步操作完成后会调用该函数，从而达到异步编程的效果。但是嵌套层数太多的回调函数会造成回调地狱，不利于代码阅读和异常处理。

**Promise**

Promise 是 ES6 中新增的一种异步编程方式，它可以更好地管理异步操作的状态，并且可以避免回调函数嵌套的问题。Promise 有三种状态：pending（进行中）、fulfilled（已完成）和 rejected（已拒绝），在异步操作完成后，Promise 对象的状态会从 pending 变为 fulfilled 或 rejected，同时可以通过 then() 和 catch() 方法来处理异步操作的结果。

**async/await**

async/await 是 ES8 中新增的异步编程方式，它使用起来更加简洁和直观，可以将异步操作看作同步操作来处理，提高了代码的可读性和可维护性。async/await 基于 Promise 实现，使用 async 关键字声明异步函数，并使用 await 关键字等待异步操作的结果。

### 对 Promise 的理解？✨

**提纲**：执行器函数、三种状态、状态改变方法、then 方法

- Promise 用作构造函数时，接收一个执行器函数作为参数，返回一个 Promise 实例，执行器函数接收两个函数 resolve 和 reject 作为参数
- Promise 实例有三种状态，分别是 pending（等待态）、fulfilled（成功态） 和 rejected（失败态）
- 实例初始化时状态是 pending，可以通过调用 resolve 函数将状态转为 fulfilled，或调用 reject 函数将状态转为 rejected。
- 实例的状态一旦改变就无法再次更改。
- Promise 实例有一个 then 方法，它接收两个函数作为参数，是 fulfilled 和 rejected 状态的回调函数，在状态改变后调用。
- then 方法会返回一个新的 Promise 实例，并且将回调函数返回的结果作为该 Promise resolve 的结果

### Ajax 是什么?

`AJAX` 可以在不重新加载整个网页的情况下，与服务器交换数据，并且更新部分网页。

`Ajax` 的原理简单来说通过 `XMLHttpRequest` 对象来向服务器发异步请求，从服务器获得数据，然后用 `JavaScript` 来操作 `DOM` 而更新页面

### 对 JS 垃圾回收机制的理解？✨

垃圾收集器会定期（周期性）找出那些不再继续使用的变量，然后释放其内存。

主流浏览器采用“标记清除”的方法：

- 垃圾收集器在运行时会给内存中的所有变量都加上一个标记，假设内存中所有对象都是垃圾，全标记为 0
- 从各个根对象（*当前执行函数的局部变量和参数、当前嵌套调用链上的其他函数的局部变量和参数、全局变量等*）开始遍历，把不是垃圾的节点改成 1
- 清理所有标记为 0 的垃圾，销毁并回收它们所占用的内存空间
- 最后，把所有内存中对象标记修改为 0，等待下一轮垃圾回收

优化：

- **分代收集**：对象被分成两组：“新的”和“旧的”，优先跟踪新对象，降低旧对象的检查频率
- **增量收集**：将整个对象集拆分成多个部分，逐一清除
- **闲时收集**：在 CPU 空闲时尝试运行垃圾回收

#### 常见的内存泄漏？

内存泄漏（Memory leak）是指由于疏忽或错误造成程序未能释放已经不再使用的内存。对于持续运行的服务进程，必须及时释放不再用到的内存。否则，内存占用越来越高，轻则影响系统性能，重则导致进程崩溃。

- **意外的全局变量** 当全局变量使用不当，没有及时回收（手动赋值 null），或者拼写错误等将某个变量挂载到全局变量时
- **遗忘的定时器** setTimeout 和 setInterval 是由浏览器专门线程来维护它的生命周期，所以当在某个页面使用了定时器，当该页面销毁时，没有手动去释放清理这些定时器的话，那么这些定时器还是存活着的。如果此时再次打开同个页面，内存中其实是有双份页面数据的，如果多次关闭、打开，那么内存泄漏会越来越严重
- **使用不当的闭包** 避免反复运行引发大量闭包
- **遗漏的 DOM 元素** DOM 元素的生命周期正常是取决于是否挂载在 DOM 树上，当从 DOM 树上移除时，也就可以被销毁回收了。但如果某个 DOM 元素，在 js 中也持有它的引用时，那么它的生命周期就由 js 和是否在 DOM 树上两者决定了，记得移除时，两个地方都需要去清理才能正常回收它
- **遗忘的事件监听器** 活动事件侦听器将防止在其范围内捕获的所有变量被垃圾收集。添加后，事件侦听器将一直有效，直到使用 removeEventListener() 显式删除或者关联的 DOM 元素被移除。
- **缓存** 如果不断地将对象添加到缓存（*Map*）中，而不删除未使用的对象，那么缓存可以无限增长。可以使用 WeakMap 来解决此问题。它是一种具有弱键引用的数据结构，仅接受对象作为键。如果我们使用一个对象作为键，并且它是对该对象的唯一引用——相关变量将从缓存中删除并被垃圾收集。

```js
function foo() {
  bar1 = 'some text'; // 没有声明变量 实际上是全局变量 => window.bar1
  this.bar2 = 'some text' // 全局变量 => window.bar2
}
foo();
```

```js
var serverData = loadData();
setInterval(function() {
  var renderer = document.getElementById('renderer');
  if(renderer) {
    renderer.innerHTML = JSON.stringify(serverData);
  }
}, 5000); // 每 5 秒调用一次
```

```js
var theThing = null;
var replaceThing = function () {
  var originalThing = theThing;
  var unused = function () {
    if (originalThing)
      // 对于 'originalThing'的引用
      console.log("hi");
  };
  theThing = {
    longStr: new Array(1000000).join("*"),
    someMethod: function () {
      console.log("message");
    },
  };
};
setInterval(replaceThing, 1000);

```

这段代码，每次调用 replaceThing 时，theThing 获得了包含一个巨大的数组和一个对于新闭包 someMethod 的对象。同时 unused 是一个引用了 originalThing 的闭包。

这个范例的关键在于，闭包之间是共享作用域的，尽管 unused 可能一直没有被调用，但是 someMethod 可能会被调用，就会导致无法对其内存进行回收。 当这段代码被反复执行时，内存会持续增长。

```js
var elements = {
  image: document.getElementById('image')
};
function doStuff() {
  elements.image.src = 'http://example.com/image_name.png';
}
function removeImage() {
  document.body.removeChild(document.getElementById('image'));
  // 这个时候我们对于 #image 仍然有一个引用, Image 元素, 仍然无法被内存回收.
}
```

#### 如何识别内存泄漏？

![[Pasted image 20240119160858.png]]

1. 打开开发者工具，选择 Timeline 面板
2. 在顶部的 `Capture` 字段里面勾选 Memory
3. 点击左上角的录制按钮。
4. 在页面上进行各种操作，模拟用户的使用情况。
5. 一段时间后，点击对话框的 stop 按钮，面板上就会显示这段时间的内存占用情况。
6. 如果内存占用基本平稳，接近水平，就说明不存在内存泄漏；反之，就是内存泄漏了。

### for...in 和 for...of 的区别

- `for in` 遍历的是数组的索引（或对象的键名）， `for of` 遍历的是数组元素值
- `for in` 会遍历数组/对象的原型上的可枚举属性，可以使用 `hasOwnProperty()` 方法将遍历限制在数组/对象内
- `for of` 的遍历不包括原型
- `for of` 适用遍历数组/字符串/`map`/`set` 等拥有迭代器对象（`iterator`）的集合，（*不能遍历对象，因为没有迭代器对象*）

### Symbol 有哪些使用场景？

- 作为对象属性的键名，避免键名冲突问题
	- Symbol 类型的 key 是不能通过 `Object.keys()` 或者 `for...in` 来枚举，利用该特性，我们可以把一些不需要对外操作和访问的属性使用 Symbol 来定义。
	- 要想访问以 Symbol 方式定义的对象属性，可以用 `Object.getOwnPropertySymbols(obj)` 或 `Reflect.ownKeys(obj)`
- 定义常量，避免命名冲突

### 在 ES5 中如何实现继承 / class extends 的原理？

组合寄生继承：即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。

```js
function inherit(child, parent) {
  // 创建父类原型的一个副本
  let prototype = Object.create(parent.prototype);
  // 将创建副本的 `constructor` 属性指向子类
  prototype.constructor = child;
  // 将新创建的对象（即副本）赋值给子类型的原型
  child.prototype = prototype;
}

function Child(name) {
  // 在子类构造函数中调用父类构造函数，继承父类的属性
  Parent.call(this, name)
}
```

### 数组的常用方法？

**会改变原数组**

- `push()`
- `unshift()`
- `pop()`
- `shift()`
- `splice()` 传入三个参数，分别是开始位置、0（要删除的元素数量）、插入的元素，返回被删除的元素数组
- `sort()`
- `reverse()`

**不会改变原数组**

- `concat()` 合并操作，参数可以是数组或元素
- `slice()` 选取原数组指定范围内的元素返回一个新数组 `[start, end)`
- `join()` 使用指定的分隔符分隔数组元素并返回字符串

**查找元素**

- `indexOf()` 返回要查找的元素在数组中的位置，如果没找到则返回 -1
- `includes()` 在数组中查找元素，找到返回 `true`，否则 `false`
- `find()` 参数为一个函数，返回第一个匹配条件的元素

**迭代方法**

- `some()` 对数组每一项都执行传入的测试函数，如果至少有 1 个元素返回 true ，则这个方法返回 true
- `every()` 对数组每一项都运行传入的测试函数，如果所有元素都返回 true ，则这个方法返回 true
- `forEach()`
- `filter()`
- `map()`
- `reduce()`

### 字符串的常用方法？

字符串的方法一般都不会改变原字符串，而是返回一个新的字符串

- `concat()`
- `slice()`
- `subStr(startIndex, 字符数量)`
- `trim()`、`trimLeft()`、`trimRight()`
- `padStart(len, 填充字符)`、`padEnd(len, 填充字符)`
- `toLowerCase()`、 `toUpperCase()`
- `indexOf()`
- `includes()`
- `startsWith()`
- `split()`
- `match()` 返回匹配字符串的数组
- `search()` 返回匹配字符串的起始索引
- `replace()` 接收两个参数，第一个参数为匹配的内容，第二个参数为替换的元素（可用函数）

### WeakSet、WeakMap 和 Set、Map 区别？

通常，当对象、数组之类的数据结构在内存中时，它们的子元素，如对象的属性、数组的元素都被认为是可达的。例如，如果把一个对象放入到数组中，那么只要这个数组存在，那么这个对象也就存在，即使没有其他对该对象的引用。

如果我们使用对象作为常规 `Map` 的键，那么当 `Map` 存在时，该对象也将存在。它会占用内存，并且不会被（垃圾回收机制）回收。

`WeakMap` 是类似于 `Map` 的集合，它仅允许对象作为键，并且一旦通过其他方式无法访问这些对象，垃圾回收便会将这些对象与其关联值一同删除。

`WeakSet` 是类似于 `Set` 的集合，它仅存储对象，并且一旦通过其他方式无法访问这些对象，垃圾回收便会将这些对象删除。

它们的主要优点是它们对对象是弱引用，所以被它们引用的对象很容易地被垃圾收集器移除。

这是以不支持 `clear`、`size`、`keys`、`values` 等作为代价换来的……

`WeakMap` 和 `WeakSet` 被用作“主要”对象存储之外的“辅助”数据结构。一旦将对象从主存储器中删除，如果该对象仅被用作 `WeakMap` 或 `WeakSet` 的键，那么该对象将被自动清除。

**Set**

- 成员不能重复
- 只有健值，没有健名，有点类似数组。
- 可以遍历，方法有 add，delete，clear，has，values，forEach，entries 等

**WeakSet**

- 成员都是对象
- 成员都是弱引用，随时可以消失。 可以用来保存 DOM 节点，不容易造成内存泄漏
- ==不能遍历==，方法有 add，delete，has，forEach

**Map**

- 本质上是健值对的集合，类似集合
- 可以遍历，方法有 get，set，delete，clear，has，values，forEach，entries 等

**WeakMap**

- 直接受对象作为健名（null 除外），不接受其他类型的值作为健名
- 健名所指向的对象，不计入垃圾回收机制
- ==不能遍历==，方法有 get，set，has，delete

### 简述 Object.defineProperty

`Object.defineProperty` 可更为精确的定义对象属性

```js
const o = {};
Object.defineProperty(o, "a", {
  configurable: false, // 是否可被删除
  enumerable: false, // 是否可被枚举
  writable: true, // 是否可更改该属性值
  value: 3, // 该属性的值
  // 拦截该属性的读写
  get() {
    return this._a;
  },
  set(a) {
    this._a = a * 10;
  },
});
```

### Object.keys 与 Object.getOwnPropertyNames() 有何区别？

- `Object.keys`: 列出对象自身可枚举的属性值
- `Object.getOwnPropertyNames`: 列出对象自身所有属性值 (*包括可枚举与不可枚举*)

### 为何 0.1+0.2 不等于 0.3，应如何做相等比较?

计算机存储双精度浮点数需要先把十进制数转换为二进制的科学记数法的形式，然后计算机以自己的规则{符号位 +(指数位 + 指数偏移量的二进制)+ 小数部分}存储二进制的科学记数法

因为存储时有位数限制（64 位），并且**某些十进制的浮点数在转换为二进制数时会出现无限循环，会造成二进制的舍入操作 (0 舍 1 入)，当再转换为十进制时就造成了计算误差**

**解决**

1. 把小数转成整数后再运算

```js
/**
 * 精确加法
 */
function add(num1, num2) {
  const num1Digits = (num1.toString().split('.')[1] || '').length;
  const num2Digits = (num2.toString().split('.')[1] || '').length;
  const baseNum = Math.pow(10, Math.max(num1Digits, num2Digits));
  return (num1 * baseNum + num2 * baseNum) / baseNum;
}
```

2. 引入一个很小的数值 `Number.EPSILON` 容忍误差，其值为 2^-52。

```js
function equal(a, b) {
  return Math.abs(a - b) < Number.EPSILON;
}
```

### 如何实现函数缓存？

思路：把参数和对应的结果数据存在一个对象中，调用时判断参数对应的数据是否存在，存在就返回对应的结果数据，否则就返回计算结果

- 在当前函数作用域定义了一个空对象，用于缓存运行结果
- 运用柯里化返回一个函数，返回的函数由于闭包特性，可以访问到 `cache`
- 然后判断输入参数是不是在 `cache` 的中。如果已经存在，直接返回 `cache` 的内容，如果没有存在，使用函数 `func` 对输入参数求值，然后把结果存储在 `cache` 中

```js
const memoize = function (func) {
  let cache = Object.create(null)
  return function(...key) {
    if (!cache[key]) {
      cache[key] = func.apply(this, key)
    }
    return cache[key]
  }
}

const calc = memoize(add);
const num1 = calc(100,200)
const num2 = calc(100,200) // 缓存得到的结果
```

### 说说你有什么办法把数组去重？✨

1. filter 过滤配合 indexOf
2. `[...new Set(arr)]` 或 `Array.from(new Set(arr))`
3. 新建数组，遍历原数组，利用 includes 去重
4. 先用 sort 排序，然后每一项与上一项相同就删除

### 可迭代对象有哪些特点？

ES6 规定，默认的 `Iterator` 接口部署在数据结构的 `Symbol.iterator` 属性，换个角度，也可以认为，一个数据结构只要具有 `Symbol.iterator` 属性 (*`Symbol.iterator` 方法对应的是遍历器生成函数，返回的是一个遍历器对象*)，那么就可以其认为是可迭代的。

可迭代对象的特点：

- 具有 Symbol.iterator 属性，Symbol.iterator() 返回的是一个遍历器对象
- 可以使用 for ... of 进行循环
- 通过被 Array.from 转换为数组

原生具有 Iterator 接口的数据结构：

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

### 怎么理解 Generator 函数？

Generator 函数内部使用 `yield` 表达式定义不同的状态，执行 Generator 函数会获得一个可迭代对象，使用 `next` 方法可以依次遍历函数内部的每个状态。

`Generator` 可以将异步操作同步化表达出来，也能用于对象迭代、控制输出、部署 `Interator` 接口等

使用方式：

- 遇到 `yield` 表达式，就暂停执行后面的操作，并将紧跟在 `yield` 后面的那个表达式的值，作为返回的对象的 `value` 属性值。
- 如果没有再遇到新的 `yield` 表达式，就一直运行到函数结束，直到 `return` 语句为止，并将 `return` 语句后面的表达式的值，作为返回的对象的 `value` 属性值。
- `done` 用来判断是否存在下个状态，`value` 对应状态值
- 如果该函数没有 `return` 语句，则返回的对象的 `value` 属性值为 `undefined`
- `yield` 表达式本身没有返回值，或者说总是返回 `undefined`
- 通过调用 `next` 方法可以带一个参数，该参数就会被当作上一个 `yield` 表达式的返回值

```js
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

### 怎么理解 ES6 中 Decorator ？

Decorator，即装饰器，可以在不改变原有代码情况下，扩展类的属性和方法。

Decorator 就是一个普通的函数：
- 当对类本身进行装饰的时候，能够接受一个参数，即类本身
- 当对类属性进行装饰的时候，能够接受三个参数：类的原型对象、需要装饰的属性名、装饰属性名的描述对象

```js
@testable
class MyTestableClass {
  @readonly
  name() {
    return 'my name'
  }
}

function testable(target) {
  target.isTestable = true;
}

MyTestableClass.isTestable // true

function readonly(target, name, descriptor){
  descriptor.writable = false; // 将可写属性设为false
  return descriptor;
}
```

### 平常工作中 ES6+ 主要用到了哪些？

ES6：

- Class
- 模块 import 和 export
- 箭头函数
- 函数默认参数
- `...` 扩展运算符允许展开数组
- 解构
- 字符串模版
- Promise
- let const
- Proxy、Map、Set
- 对象属性同名能简写

ES7：

- includes
- `**` 求幂运算符

ES8：

- async/await
- Object.values() 和 Object.entries()
- padStart() 和 padEnd()
- Object.getOwnPropertyDescriptors()
- 函数参数允许尾部

ES9：

- for...await...of
- `...` 展开符合允许展开对象收集剩余参数
- Promise.finally()
- 正则中的四个新功能

ES10：

- flat()
- flatMap()
- fromEntries()
- trimStart 和 trimEnd
- matchAll
- BigInt
- try/catch 中报错允许没有 err 异常参数
- Symbol.prototype.description
- Function.toString() 调用时呈现原本源码的样子

### 对尾调用和尾递归的理解？

**尾调用**

尾调用就是指函数的最后一步是调用另一个函数（*尾调用不一定出现在函数尾部，只要是最后一步操作即可*）

```js
// m 和 n 都属于尾调用
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

**尾调用优化**

函数调用会在内存形成一个 " 调用记录 "，又称 " 调用帧 "（call frame），保存调用位置和内部变量等信息。如果在函数内部调用函数，所有函数的调用记录会形成一个调用栈。

==尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用记录==，只保留内层函数的调用记录。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用记录只有一项，这将大大节省内存。（*ES6 的尾调用优化只在严格模式下开启，正常模式是无效的*）

**尾递归**

函数调用自身，称为递归。如果尾调用自身，就称为尾递归。对于尾递归来说，由于只存在一个调用记录，所以永远不会发生 " 栈溢出 " 错误。

要把递归函数用尾递归实现，可以**把所有用到的内部变量改写成函数的参数**（使用函数柯里化或参数默认值提高函数调用的可读性）

### js 定时器为什么是不精确的？如何解决？

JavaScript 中的定时器（`setTimeout` 和 `setInterval`）可能不是完全精确的，这是因为 JavaScript 是单线程的，并且在执行 JavaScript 代码时，可能会发生以下情况导致定时器不精确：

1. 事件循环机制
2. 高优先级任务
3. 定时器的最小延迟：定时器最早也会在 4 毫秒后执行
4. 页面不可见性：当页面处于不可见状态，浏览器可能会降低对定时器的处理优先级，导致定时器的回调函数被延迟执行或甚至暂停执行。

**解决方法**

- 使用 `requestAnimationFrame` 代替 `setTimeout` 或 `setInterval`，因为 `requestAnimationFrame` 会在浏览器的下一帧绘制之前执行，通常可以获得更好的时间精度。
- 使用 `Web Workers` 在后台线程中处理定时任务，以避免主线程的阻塞。
- 注意避免在定时器回调函数中执行耗时的操作，以确保定时器的回调函数能够及时执行。

### 如何实现大文件上传？

[如何实现大文件上传？](https://juejin.cn/post/6844904046436843527)

- 前端上传大文件时使用 Blob.prototype.slice 将文件切片，并发上传多个切片，最后发送一个合并的请求通知服务端合并切片
- 服务端接收切片并存储，收到合并请求后使用流将切片合并到最终文件
- 原生 XMLHttpRequest 的 upload.onprogress 对切片上传进度的监听
- 使用 Vue 计算属性根据每个切片的进度算出整个文件的上传进度

**断点续传**

- 使用 spark-md5 根据文件内容算出文件 hash
- 通过 hash （根据文件内容生成）可以判断服务端是否已经上传该文件，从而直接提示用户上传成功（秒传）
- 通过 XMLHttpRequest 的 abort 方法暂停切片的上传
- 上传前服务端返回已经上传的切片名，前端跳过这些切片的上传
