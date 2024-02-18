### DOCTYPE(文档类型) 的作用是什么？

DOCTYPE 用来声明**文档类型定义**规范，目的是告诉浏览器应该以什么样 (html 或 xhtml) 的文档类型定义来解析文档。

HTML5 的写法是 `<!DOCTYPE html>`

### 对 HTML 语义化的理解

- **含义**：用正确的标签做正确的事情：根据内容选择合适的标签
- **对机器**：利于 SEO；搜索引擎的爬虫也依赖于 HTML 标记来确定上下文和各个关键字的权重。
- **对人**：容易阅读和维护。

#### 常用的语义化标签

- `<hn>`：h1 ~ h6，分级标题， `<hn>` 与 `<title>` 协调有利于搜索引擎优化
- `<ul>`：无序列表
- `<ol>`：有序列表
- `<header>`：页眉通常包括网站标志、主导航、全站链接以及搜索框。
- `<nav>`：标记导航，仅对文档中重要的链接群使用。
- `<main>`：页面主要内容，一个页面只能使用一次。如果是 web 应用，则包围其主要功能。
- `<article>`：定义外部的内容，其中的内容独立于文档的其余部分。
- `<section>`：定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分。
- `<aside>`：定义其所处内容之外的内容。如侧栏、文章的一组链接、广告、友情链接、相关产品列表等。
- `<footer>`：页脚，只有当父级是 body 时，才是整个页面的页脚。
- `<small>`：呈现小号字体效果，指定细则，输入免责声明、注解、署名、版权。
- `<strong>`：和 `em` 标签一样，用于强调文本，但它强调的程度更强一些。
- `<em>`：将其中的文本表示为强调的内容，表现为斜体。
- `<mark>`：使用黄色突出显示部分文本。
- `<figure>`：规定独立的流内容（图像、图表、照片、代码等等）（默认有 40px 左右 margin）。
- `<figcaption>`：定义 `figure` 元素的标题，应该被置于 `figure` 元素的第一个或最后一个子元素的位置。
- `<cite>`：表示所包含的文本对某个参考文献的引用，比如书籍或者杂志的标题。
- `<blockquoto>`：定义块引用，块引用拥有它们自己的空间。
- `<address>`：作者、相关人士或组织的联系信息（电子邮件地址、指向联系信息页的链接）。
- `<del>`：移除的内容。
- `<ins>`：添加的内容。
- `<code>`：标记代码。
- `<progress>`：定义运行中的进度（进程）。

###  script 标签中 defer 和 async 的区别

![[Pasted image 20240116155503.png]]

- 无属性：JS 的脚本加载 (fetch) 且执行 (execution) 会阻塞 DOM 的渲染，因此 JS 一般放到最后
- defer
	- 异步加载
	- 加载完成后等待 dom 解析完成后才执行，但会在事件 `domContentLoaded` 之前
	- 多个脚本按照加载顺序执行
- async
	- 异步加载
	- 加载完成后立即执行，会阻塞 dom 解析
	- 执行顺序不一定

### iframe 有哪些缺点？

- 阻塞主页面的 onload 事件。window 的 onload 事件需要在所有 iframe 加载完毕后（包含里面的元素）才会触发。
- 不利于网页的 SEO。搜索引擎的检索程序无法解读这种页面。
- 影响页面的并行加载。iframe 和主页面共享连接池，而浏览器对相同域的连接有限制。
- 浏览器的后退按钮失效。
- 小型的移动设备无法完全显示框架。

### 介绍一下 drag API

被拖放元素：
- `dragstart` 在开始拖放被拖放元素时触发
- `drag` 在正在拖放被拖放元素时触发
目标元素：
- `dragenter` 在被拖放元素进入某元素时触发。
- `dragover` 在被拖放在某元素内移动时触发。
- `dragleave` 在被拖放元素移出目标元素是触 发。
- `drop` 在目标元素完全接受被拖放元素时触发。
- `dragend` 事件主体是被拖放元素，在整个拖放操作结束时触发。

### addEventListener 第三个参数有哪些值？

`options` 一个指定有关 `listener` 属性的可选参数对象。可用的选项如下：
- capture 监听器会在时间捕获阶段传播到 event.target 时触发。
- passive 监听器不会调用 preventDefault()。
- once 监听器只会执行一次，执行后移除。
- signal 该 `AbortSignal` 的 `abort()` 方法被调用时，监听器会被移除。
`useCapture` 捕获阶段调用监听器

### 什么是事件冒泡和事件捕获？

事件冒泡：事件会从一个具体的元素开始触发，然后不断向上传递最终传递给 document

事件捕获：事件会从不具体的元素传递给具体的元素，相当于是事件最开始是从最外部触发，然后不断向内传递，最终传递给具体的元素

#### 关于事件捕获和冒泡，以下代码输出

```html
<div class="container" id="container">
  <div class="item" id="item">
    <div class="btn" id="btn">
      Click me
    </div>
  </div>
</div>
```

```js
document.addEventListener('click', (e) => {
  console.log('Document click')
}, {
  capture: true
})

container.addEventListener('click', (e) => {
  console.log('Container click')
  // console.log(e.target, e.currentTarget)
  // e.stopPropagation()
}, {
  capture: true
})

item.addEventListener('click', () => {
  console.log('Item click')
})

btn.addEventListener('click', () => {
  console.log('Btn click')
})

btn.addEventListener('click', () => {
  console.log('Btn click When Capture')
}, {
  capture: true
})

```

捕获事件 ==> 冒泡事件 (addEventListener) 或 on 事件
冒泡事件 addEventListener 和 on 事件的执行顺序：按照定义时的顺序执行 (书写代码的先后) 并且，冒泡事件 addEventListener 不会覆盖，有多少个事件就执行多少个，按顺序执行 而，相同的 on 事件会覆盖，后面的覆盖前面的

```
"Document click"
"Container click"
"Btn click When Capture"
"Btn click"
"Item click"
```

#### 什么是事件委托？

如果有很多元素要写事件监听 (也就是事件处理程序)，那么可以给所有元素的共同父元素添加一个事件处理程序, 然后因为事件冒泡流的存在, 在子元素上触发的事件最终会传递给父元素，所以在父元素上也可以监听到在子元素上触发的事件，从而提高页面效率

#### 如何阻止冒泡和捕获？

`e.stopPropagation()` 取消所有后续事件捕获和事件冒泡

#### e.currentTarget 与 e.target 有何区别

`e.currentTarget` 事件绑定的元素
`e.target` 事件触发的元素

### 如何阻止事件默认行为？

- `e.preventDefault()`: 取消事件
- `e.cancelable`: 事件是否可取消

如果 `addEventListener` 第三个参数 `{ passive: true}`，`preventDefault` 将会会无效

### 如何添加、删除、移动、复制 DOM 节点?

**创建**:

- createTextNode() //创建文本节点
- createElement() //创建元素节点
- createDocumentFragment() //创建文档碎片

**操作**:

- appendChild() //增加
- removeChild() //删除
- replaceChild() //替换
- insertBefore() //插入

**查找**:

- getElementById()
- getElementByTagName()
- getElementByName()

### 如何判断在移动端?

```js
function isPc() {
  var userAgentInfo = navigator.userAgent;
  var Agents = new Array(
    "Android",
    "iPhone",
    "SymbianOS",
    "Windows Phone",
    "iPad",
    "iPod"
  );
  var flag = true;
  flag = !Agents.some((ele) => {
    return userAgentInfo.indexOf(ele) > 0;
  });
  return flag;
}
```

### IntersectionObserver 怎么使用的？怎么知道一个 DOM 节点出现在视口内？

要了解某个元素是否进入了 " 视口 "（viewport），即用户能不能看到它，传统的实现方法是，监听到 scroll 事件后，调用目标元素的 getBoundingClientRect() 方法，得到它对应于视口左上角的坐标，再判断是否在视口之内。然后声明一个全局变量，每出现一次就加一，就可以得出在视口出现了几次。这种方法的缺点是，由于 scroll 事件密集发生，计算量很大，容易造成性能问题。

于是便有了 IntersectionObserver API

**IntersectionObserver**

```js
var io = new IntersectionObserver(callback, option);
```

上面代码中，IntersectionObserver 是浏览器原生提供的构造函数，接受两个参数：callback 是可见性变化时的回调函数，option 是配置对象（该参数可选）。

构造函数的返回值是一个观察器实例。实例的 observe 方法可以指定观察哪个 DOM 节点。

```js
// 开始观察
io.observe(document.getElementById("example"));

// 停止观察
io.unobserve(element);

// 关闭观察器
io.disconnect();
```

上面代码中，observe 的参数是一个 DOM 节点对象。如果要观察多个节点，就要多次调用这个方法。

```js
io.observe(elementA);
io.observe(elementB);
```

**Callback 参数**

目标元素的可见性变化时，就会调用观察器的回调函数 callback。 callback 一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）。

```js
var io = new IntersectionObserver((entries) => {
  console.log(entries);
});
```

callback 函数的参数（entries）是一个数组，每个成员都是一个 IntersectionObserverEntry 对象。如果同时有两个被观察的对象的可见性发生变化，entries 数组就会有两个成员。

IntersectionObserverEntry 对象提供目标元素的信息，一共有六个属性。

```js
{
  time: 3893.92, // 可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
  // 根元素的矩形区域的信息，getBoundingClientRect() 方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回 null
  rootBounds: ClientRect { 
    bottom: 920,
    height: 1024,
    left: 0,
    right: 1024,
    top: 0,
    width: 920
  },
  // 目标元素的矩形区域的信息
  boundingClientRect: ClientRect { 
     // ...
  },
  // 目标元素与视口（或根元素）的交叉区域的信息
  intersectionRect: ClientRect {
    // ...
  },
  // 目标元素的可见比例，即 intersectionRect 占 boundingClientRect 的比例，完全可见时为 1，完全不可见时小于等于 0
  intersectionRatio: 0.54,
  target: element // 被观察的目标元素，是一个 DOM 节点对象
}
```

**Option 对象**

IntersectionObserver 构造函数的第二个参数是一个配置对象。它可以设置以下属性：

- threshold 属性
- root 属性、rootMargin 属性

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
