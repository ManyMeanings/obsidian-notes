## Vue

### 对 SPA 单页面的理解？

SPA（ single-page application ）**仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS**。一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

#### SPA 的优点和缺点？

**优点：**

- ==用户体验好==、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
- 基于上面一点，SPA 相对==对服务器压力小==；
- ==前后端职责分离==，架构清晰，前端进行交互逻辑，后端负责数据处理；

**缺点：**

- ==初次加载耗时多==：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
- 前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以==不能使用浏览器的前进后退功能==，所有的页面切换需要自己建立堆栈管理；
- ==SEO 难度较大==：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

#### 与 MPA 多页面应用的区别？

|  | **单页面应用（SPA）** | **多页面应用（MPA）** |
| ---- | ---- | ---- |
| **组成** | 一个主页面和多个页面片段 | 多个主页面 |
| **刷新方式** | 局部刷新 | 整页刷新 |
| **url 模式** | 哈希模式 | 历史模式 |
| **SEO 搜索引擎优化** | 难实现，可使用 SSR 方式改善 | 容易实现 |
| **数据传递** | 容易 | 通过 url、cookie、localStorage 等传递 |
| **页面切换** | 速度快，用户体验良好 | 切换加载资源，速度慢，用户体验差 |
| **维护成本** | 相对容易 | 相对复杂 |

#### 如何优化 SPA 首屏加载时间？

减少首屏渲染时间的方法有很多，总的来讲可以分成两大部分 ：==资源加载优化== 和 ==页面渲染优化==

![[Pasted image 20240209155033.png]]

**（1）减少入口文件体积**

路由懒加载，把不同路由对应的组件分割成不同的代码块，待路由被请求的时候会单独打包路由，使得入口文件变小，加载速度大大增加

在 `vue-router` 配置路由的时候，采用动态加载路由的形式

```js
routes:[ 
    path: 'Blogs',
    name: 'ShowBlogs',
    component: () => import('./components/ShowBlogs.vue')
]
```

以函数的形式加载路由，这样就可以把各自的路由文件分别打包，只有在解析给定的路由时，才会加载路由组件

**（2）静态资源本地缓存**

后端返回资源问题：

- 采用 `HTTP` 缓存，设置 `Cache-Control`，`Last-Modified`，`Etag` 等响应头
- 采用 `Service Worker` 离线缓存

前端合理利用 `localStorage`

**（3）UI 框架按需加载**

在日常使用 `UI` 框架，例如 `element-UI`、或者 `antd`，我们经常性直接引用整个 `UI` 库

```js
import ElementUI from 'element-ui'
Vue.use(ElementUI)
```

但实际上我用到的组件只有按钮，分页，表格，输入与警告 所以我们要按需引用

```js
import { Button, Input, Pagination, Table, TableColumn, MessageBox } from 'element-ui';
Vue.use(Button)
Vue.use(Input)
Vue.use(Pagination)
```

**（4）图片资源压缩**

图片资源虽然不在编码过程中，但它却是对页面性能影响最大的因素

对于所有的图片资源，我们可以进行适当的压缩

对页面上使用到的 `icon`，可以使用在线字体图标，或者雪碧图，将众多小图标合并到同一张图上，用以减轻 `http` 请求压力

**（5）开启 GZip 压缩**

**（6）使用 SSR**

### MVC、MVP 和 MVVM 的区别？

**MVC（Model View Controller）**

![[Pasted image 20240131151935.png]]

View 负责页面的显示逻辑，Model 负责存储页面的业务数据，Controller 负责业务逻辑，三者是单向联系。View 和 Model 之间应用了观察者模式，当 Model 层发生改变的时候它会通知相关 View 层更新页面。

1. View 接受用户交互请求并转交给 Controller 处理
2. Controller 完成业务逻辑后改变 Model 状态
3. Model 将新的数据发送给 View 去更新，使用户得到反馈

**MVP（Model View Presenter）**

![[Pasted image 20240131153414.png]]

MVP 模式通过使用 Presenter 来实现对 View 和 Model 的解耦。

- 各部分之间的通信，都是双向的。
- View 与 Model 不发生联系，都通过 Presenter 传递。
- View 非常薄，不部署任何业务逻辑，称为 " 被动视图 "（Passive View），即没有任何主动性，而 Presenter 非常厚，所有逻辑都部署在那里。

**MVVM（Model View ViewModel）**

![[Pasted image 20240131152856.png]]

MVVM 模式与 MVP 的区别在于 ViewModel 和 View 之间是双向绑定的关系

- ==ViewModel 和 View 之间是双向绑定的关系==：View 的变动，自动反映在 ViewModel，反之亦然。
- View 和 Model 之间无直接联系，而是通过 ViewModel 来进行联系。

### Vue 的生命周期？

- **beforeCreate**
	- 组件实例还未创建，通常用于插件开发中执行一些初始化任务
- **created**
	- 完成数据观测，属性与方法的运算，`watch`、`event` 事件回调的配置
	- 可调用 `methods` 中的方法，访问和修改 data 数据触发响应式渲染 `dom`，可通过 `computed` 和 `watch` 完成数据计算
	- 此时 `vm.$el` 并没有被创建
	- 组件初始化完毕，各种数据可以使用，常用于异步数据获取
- **beforeMount**
	- 在此阶段可获取到 `vm.el`
	- 此阶段 `vm.el` 虽已完成 DOM 初始化，但并未挂载在 `el` 选项上
- **mounted**
	- `vm.el` 已完成 `DOM` 的挂载与渲染，此刻打印 `vm.$el`，发现之前的挂载点及内容已被替换成新的 DOM
	- 可以访问/修改 dom 元素
- **beforeUpdate**
	- 更新的数据必须是被渲染在模板上的（`el`、`template`、`render` 之一）
	- 此时 `view` 层还未更新
	- 若在 `beforeUpdate` 中再次修改数据，不会再次触发更新方法
- **updated**
	- 完成 `view` 层的更新
	- 若在 `updated` 中再次修改数据，会再次触发更新方法（`beforeUpdate`、`updated`）
- **beforeDestroy**（*Vue3 为 beforeUnmount*）
	- 实例被销毁前调用，此时实例属性与方法仍可访问
	- 可用于一些定时器或订阅的取消
- **destroyed**（*Vue3 为 unmounted*）
	- 完全销毁一个实例。可清理它与其它实例的连接，解绑它的全部指令及事件监听器
	- 并不能清除 DOM，仅仅销毁实例
- **errorCaptured** 
	- 当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 `false` 以阻止该错误继续向上传播。
- **deactivated**
	- 被 `keep-alive` 缓存的组件停用时调用。
- **activated**
	- 被 `keep-alive` 缓存的组件激活时调用。
- **setup**
	- 使用组合式 API 时， `setup()` 取代了 `beforeCreate` 和 `created`

#### 父子组件的生命周期关系？

- 创建： 父 created -> 子 created -> 子 mounted -> 父 mounted
- 更新： 父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
- 销毁： 父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

#### 在哪个生命周期发送请求？

`created` 是在组件实例一旦创建完成的时候立刻调用，这时候页面 `dom` 节点并未生成；`mounted` 是在页面 `dom` 节点渲染完毕之后就立刻执行的。触发时机上 `created` 是比 `mounted` 要更早的，两者的相同点：都能拿到实例对象的属性和方法。 讨论这个问题本质就是触发的时机，放在 `mounted` 中的请求有可能导致页面闪动（因为此时页面 `dom` 结构已经生成），但如果在页面加载前完成请求，则不会出现此情况。建议对页面内容的改动放在 `created` 生命周期当中。

### Computed 和 watch 的区别？

**computed**

- 本质是一个具备缓存的 Watcher，只有依赖属性发生变化时才会更新视图，而且结果是在下次使用时获取
- 不支持异步，当 `computed` 内有异步操作时无效，无法监听数据的变化
- 当需要进行数值计算，并且依赖于其它数据时，应该使用 `computed`，因为可以利用 `computed` 的缓存特性，避免每次获取值时都要重新计算

**watch**

- 没有缓存性，更多的是观察的作用，某些数据变化时会执行回调
- watch 支持异步；可以设置异步返回前的中间状态
- 可以在初始化时执行回调
- 可以深度监听对象属性
- 可以设置回调的执行时机，通过设置 `sync` 属性可以在当前队列执行，默认是下一队列
- 通过 `vm.$watch` 注册的监听，会返回一个 `unWatch` 函数，调用该函数可以取消监听

### v-if 和 v-show 的区别

- **控制方式**
	- v-if 根据条件动态渲染或删除 dom 元素
	- v-show 在条件不满足时在 dom 元素上添加 `display:none`
- **编译过程**
	- `v-if` 切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件，触发组件的生命周期。
	- `v-show` 只是简单的基于 css 切换
- **性能消耗
	- v-if 有更高的切换开销
	- v-show 有更高的初始渲染开销
- **使用场景**
	- v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景
	- v-show 适用于需要频繁切换条件的场景

### Vue 的 data 为什么必须是函数？？

**组件是用来复用的**，且 Vue 组件和 Vue 实例之间是通过原型链来继承的。如果 data 是一个对象，那么在组件复用时，多个组件将共享一个 data 对象，这样一个组件的状态改变会影响到其他组件。

将 data 选项定义为函数，因为函数每次调用返回的都是一个全新的对象，从而避免状态影响。

### Vue 的优点？

- 轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十 `kb` ；
- 双向数据绑定：在数据操作方面更为简单；
- 组件化：保留了 `react` 的优点，实现了 `html` 的封装和重用，在构建单页面应用方面有着独特的优势；
- 视图，数据，结构分离：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作；
- 虚拟 DOM：`dom` 操作是非常耗费性能的，不再使用原生的 `dom` 操作节点，极大解放 `dom` 操作，但具体操作的还是 `dom` 不过是换了另一种方式；
- 运行速度更快：相比较于 `react` 而言，同样是操作虚拟 `dom`，就性能而言， `vue` 存在很大的优势。

#### Vue 和 React 的对比？

**与 React 相比，Vue 具有以下优势**

- Vue 更小且更快
- 方便的模板简化了开发过程
- 相比学习 JSX 它有更简单的 JavaScript 语法
- 深受国内企业和开发者喜爱

**与 Vue 相比，React 具有以下优势**

- ReactJS 在大型应用程序开发中提供了更大的灵活性
- 易于测试
- 非常适合创建移动应用程序
- 生态系统规模大，成熟度高

### 组件之间的通信方式有哪些？

**（1）props / $emit 父子组件通信**

- 父组件通过 `props` 向子组件传递数据
- 子组件通过 `$emit` 调用父组件定义的事件并传递参数。

**（2）ref / $refs 父子组件通信**

通过在调用子组件时定义 `ref`，可以在父组件中通过 `$refs` 访问子组件的实例

**（3）$parent / $children 父子组件通信**

- 在子组件中使用 `$parent` 可以访问上一级父组件的实例
- 在父组件中使用 `$children` 可以得到一个包含所有的子组件实例的数组，无序并且访问的数据也不是响应式的。

**（4）provide / inject 隔代组件通信**

- 在父组件中通过 `provide` 来发送数据或方法，进行依赖注入
- 在子组件中通过 `inject` 来接收数据或方法

**（5）$attrs / $listeners 隔代组件通信**

- `$attrs`：继承所有的父组件属性（除了 prop 传递的属性、class 和 style ），一般用在子组件的子元素上
- `$listeners`：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。（相当于子组件继承父组件的事件）

**（6）eventBus 事件总线（`$emit / $on`）父子、隔代、兄弟组件通信**

通过一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信，包括父子、隔代、兄弟组件。

```js
// com1
// 触发事件
EventBus.$emit('addition', { num:this.num++ })
// com2
// 监听事件
EventBus.$on('addition', param => { this.count = this.count + param.num; })
```

**（7）状态管理库（Vuex 或 Pinia）**

**（8）localStorage / sessionStorage**

### 在实际项目中，vue 组件通信有哪些注意点 ？

- **明确通信方式**：根据具体情况选择适合的通信方式。
- **组件解耦**：组件之间应该尽量保持解耦，即一个组件不应该过于依赖其他组件的内部实现细节。通过明确定义 props 和事件接口，组件可以更加独立地工作，提高复用性和可维护性。
- **单向数据流**：在 Vue 中，数据流是单向的，自上而下。父组件通过 props 向子组件传递数据，子组件通过事件向父组件发送消息。遵循这个单向数据流可以使数据流动更加可控和可预测。
- **避免过度通信**：过多的组件通信可能导致代码复杂性增加。在设计组件通信时，要避免过度通信，只传递必要的数据和事件，以保持代码的简洁性和可读性。
- **适当使用事件总线**：事件总线（Event Bus）是一种简单的组件通信方式，可以在组件之间进行事件的发布和订阅。但是，过度使用事件总线可能导致代码难以维护和调试，因此应该谨慎使用，并在必要时考虑其他更适合的通信方式。
- **谨慎使用 provide/inject**：provide/inject 是一种高级的组件通信方式，可以在祖先组件中提供数据，并在后代组件中注入使用。然而，过度使用 provide/inject 可能导致组件之间的依赖关系不明确，难以追踪和理解，因此需要谨慎使用。
- 使用 Vuex 进行状态管理：如果组件之间需要共享状态或进行复杂的通信，考虑使用 Vuex 进行状态管理。Vuex 提供了一个集中式的状态管理方案，可以更好地管理和共享组件之间的状态。

### 有用过 Vue3 吗，和 Vue2 有哪些区别？

[面试官：vue3有了解过吗？能说说跟vue2的区别吗？ | web前端面试 - 面试官系列](https://vue3js.cn/interview/vue/vue3_vue2.html#%E4%B8%80%E3%80%81vue3%E4%BB%8B%E7%BB%8D)

### vue3.0 为什么要引入 Composition API？

1. 更好的代码组织，options api 造成了代码的跳来跳去
2. 逻辑复用更加的方便，虽然 mixin 也能够很好的复用代码，但是当 mixin 多了以后就不知道变量哪里来的了，还会造成命名冲突
3. 没有让人捉摸不透的 this

### v-if 与 v-for 为什么不建议一起使用 ？

1. Vue2 中 v-for 优先级更高，每次先循环再判断，带来性能浪费
2. Vue3 中 v-if 优先级更高，但是也不推荐
3. 如果避免出现这种情况，则在外层嵌套 `template`（页面渲染不生成 `dom` 节点），在这一层进行 v-if 判断，然后在内部进行 v-for 循环

```html
<template v-if="isShow">
    <p v-for="item in items">
</template>
```

3. 如果条件出现在循环内部，可通过计算属性 `computed` 提前过滤掉那些不需要显示的项

```js
computed: {
    items: function() {
      return this.list.filter(function (item) {
        return item.isShow
      })
    }
}
```

### scoped 属性作用？

当一个 style 标签设置 scoped 属性，它的 css 样式只能用于当前 Vue 组件，使==组件的样式不相互污染==。

scoped 属性的实现原理是给每一个 dom 元素添加了一个独一无二的动态属性，给 css 选择器额外添加一个对应的属性选择器，来选择组件中的 dom。

```
.box[data-v-11c6864c]{
    background:red;
}
<template>
    <div class="box" data-v-11c6864c>dom</div>
</template>
```

### vue 初始化页面闪动问题？

使用 vue 开发时，在 vue 初始化之前，由于 div 是不归 vue 管的，所以我们写的代码在还没有解析的情况下会容易出现花屏现象，看到类似于{{message}}的字样，虽然一般情况下这个时间很短暂，但是还是有必要让解决这个问题的。

首先：在 css 里加上以下代码：

```css
[v-cloak] { display: none;}
```

如果没有彻底解决问题，则在根元素加上 `style="display: none;" :style="{display: 'block'}"`

#### scoped 如何进行样式穿透？

scoped 虽然避免了组件间样式污染，但是很多时候我们需要修改组件中的某个样式，但是又不想去除 scoped 属性。

1. 使用 `/deep/` 深度选择器
2. 使用两个 style 标签

### 如何封装一个通用的弹窗组件？

1. 确定弹窗的结构：遮罩层、头部、内容、底部
2. 组件 API 设计：
	1. props：标题、是否展示底部、尺寸、挂载的 dom 元素等
	2. slot：头部、内容和底部的默认插槽
	3. event：打开、关闭、确认、取消等触发的事件
3. 优化：
	1. 支持 API 形式调用，接收 h 函数渲染内容（[Vue3丨TS丨7 个思路封装一个灵活的 Modal 对话框 - 掘金](https://juejin.cn/post/6916362519279829005)）

### 什么是自定义指令，怎么实现？

Vue 自定义指令可以用来**复用代码，封装常用的 DOM 操作或行为**。常见的自定义指令有监听滚动事件、图片懒加载、设置 loading、权限管理等。

一个自定义指令由一个包含类似组件生命周期钩子的对象来定义。钩子函数会接收到指令所绑定元素作为其参数。自定义指令可以全局注册或局部注册，注册后可以在模板中使用 v- 前缀调用。

```js
app.directive('fsize', (el, binding) => {
	// 等于同时设定 mounted 和 updated
	el.style.fontSize = binding.value + (binding.arg || 'px');
});

// template
// <div v-fsize:em="20">test</div>
```

### 什么是 slot？

通过 `slot` 插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用。有三种插槽：默认插槽、具名插槽、作用域插槽

- `v-slot` 属性只能在 `<template>` 上使用，但在只有默认插槽时可以在组件标签上使用
- 默认插槽名为 `default`，可以省略 default 直接写 `v-slot`
- 简写为 `#` 时不能不写参数，写成 `#default`
- 可以通过解构获取 `v-slot={user}`，还可以重命名 `v-slot="{user: newName}"` 和定义默认值 `v-slot="{user = '默认值'}"`

### 常用的修饰符？

**表单修饰符**

- .lazy：光标离开标签的时候，才会将值赋予给 `value`
- .trim：自动过滤用户输入的首尾空格字符
- .number：自动将用户的输入值转为数值类型

**事件修饰符**

- .stop：阻止事件冒泡
- .prevent：阻止默认事件
- .self ：只当在 `event.target` 是当前元素自身时触发处理函数
- .once：事件只触发一次

**v-bind 修饰符**

- .sync：能对 `props` 进行一个双向绑定

### Vue 组件里写的原生 addEventListeners 监听事件，要手动去销毁吗？为什么？

要在 beforeDestroy 手动销毁，否则如果在 mounted 中使用 addEventListeners，可能会多次重复注册导致内存泄漏。

### Vue 中父组件如何监听子组件的生命周期？

**使用 on 和 emit**

```js
// Parent.vue
<Child @mounted="doSomething"/>
// Child.vue
mounted() {
  this.$emit("mounted");
}
```

**使用 hook 钩子函数**

```js
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...
```

### 如何在 Vue 中定义全局方法？

**Vue2**

将方法挂载到 Vue.prototype 上面（缺点：调用这个方法的时候没有提示）

```js
// global.js
const RandomString = (encode = 36, number = -8) => {
  return Math.random() // 生成随机数字, eg: 0.123456
    .toString(encode) // 转化成36进制 : "0.4fzyo82mvyr"
    .slice(number);
},
export default {
	RandomString,
  ...
}
```

```js
// 在项目入口的main.js里配置
import Vue from "vue";
import global from "@/global";
Object.keys(global).forEach((key) => {
  Vue.prototype["$global" + key] = global[key];
});
```

```js
// 挂载之后，在需要引用全局变量的模块处(App.vue)，不需再导入全局变量模块，而是直接用this就可以引用了，如下:
export default {
  mounted() {
    this.$globalRandomString();
  },
};
```

**Vue3**

使用 `app.config.globalProperties`

```js
//在main.js中创建实例
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App);
//挂载方法到实例上
app.config.globalProperties.$axios = axios

//全局调用
import { getCurrentInstance } from "vue";
const { proxy } = getCurrentInstance();
proxy.$axios()
```

### Vue 中组件和插件有什么区别？

组件 `(Component)` 是用来构成你的 `App` 的业务模块，它的目标是 `App.vue`

插件 `(Plugin)` 是用来增强你的技术栈的功能模块，它的目标是 `Vue` 本身

### 你知道哪些 Vue 编码规范？

1. if 和 for 不能用在同一个元素。
2. 对于不会发生变化的数据，用 Object.freeze() 冻结。
3. 给 for 设置合适的 key，尽量不要使用数组的索引作为 key。
4. 组件命名：驼峰命名，首字母大写，组件名应该为多个单词组成。
5. props 要指定类型。
6. 使用策略模式替代满屏的 if/else。
7. 不要写死字符串，多个状态时要使用枚举。

### Vue 项目的目录结构设计

**基本原则**

- 文件夹和文件夹内部文件的语义一致性
- 单一入口/出口
- 就近原则，紧耦合的文件应该放到一起，且应以相对路径引用
- 公共的文件应该以绝对路径的方式从根目录引用
- `/src` 外的文件不应该被引入

```
my-vue-test:.
│  .browserslistrc
│  .env.production
│  .eslintrc.js
│  .gitignore
│  babel.config.js
│  package-lock.json
│  package.json
│  README.md
│  vue.config.js
│  yarn-error.log
│  yarn.lock
│
├─public
│      favicon.ico
│      index.html
│
└─src
    ├─apis //接口文件根据页面或实例模块化
    │      index.js
    │      login.js
    │
    ├─components //全局公共组件
    │  └─header
    │          index.less
    │          index.vue
    │
    ├─config //配置（环境变量配置不同passid等）
    │      env.js
    │      index.js
    │
    ├─contant //常量
    │      index.js
    │
    ├─images //图片
    │      logo.png
    │
    ├─pages //多页面vue项目，不同的实例
    │  ├─index //主实例
    │  │  │  index.js
    │  │  │  index.vue
    │  │  │  main.js
    │  │  │  router.js
    │  │  │  store.js
    │  │  │
    │  │  ├─components //业务组件
    │  │  └─pages //此实例中的各个路由
    │  │      ├─amenu
    │  │      │      index.vue
    │  │      │
    │  │      └─bmenu
    │  │              index.vue
    │  │
    │  └─login //另一个实例
    │          index.js
    │          index.vue
    │          main.js
    │
    ├─scripts //包含各种常用配置，工具函数
    │  │  map.js
    │  │
    │  └─utils
    │          helper.js
    │
    ├─store //vuex仓库
    │  │  index.js
    │  │
    │  ├─index
    │  │      actions.js
    │  │      getters.js
    │  │      index.js
    │  │      mutation-types.js
    │  │      mutations.js
    │  │      state.js
    │  │
    │  └─user
    │          actions.js
    │          getters.js
    │          index.js
    │          mutation-types.js
    │          mutations.js
    │          state.js
    │
    └─styles //样式统一配置
        │  components.less
        │
        ├─animation
        │      index.less
        │      slide.less
        │
        ├─base
        │      index.less
        │      style.less
        │      var.less
        │      widget.less
        │
        └─common
                index.less
                reset.less
                style.less
                transition.less
```

### Vue 项目如何做权限控制？

**（1）接口权限**

接口权限目前一般采用 `jwt` 的形式来验证，没有通过的话一般返回 `401`，跳转到登录页面重新进行登录

登录完拿到 `token`，将 `token` 存起来，通过 `axios` 请求拦截器进行拦截，每次请求的时候头部携带 `token`

```js
axios.interceptors.request.use(config => {
    config.headers['token'] = cookie.get('token')
    return config
})
axios.interceptors.response.use(res=>{},{response}=>{
    if (response.data.code === 40099 || response.data.code === 40098) { //token过期或者错误
        router.push('/login')
    }
})
```

**（2）菜单权限**

菜单与路由分离，菜单由后端返回

前端定义路由信息

```js
{
    name: "login",
    path: "/login",
    component: () => import("@/pages/Login.vue")
}
```

`name` 字段都不为空，需要根据此字段与后端返回菜单做关联，后端返回的菜单信息中必须要有 `name` 对应的字段，并且做唯一性校验

全局路由守卫里做判断

```js
function hasPermission(router, accessMenu) {
  if (whiteList.indexOf(router.path) !== -1) {
    return true;
  }
  let menu = Util.getMenuByName(router.name, accessMenu);
  if (menu.name) {
    return true;
  }
  return false;

}

Router.beforeEach(async (to, from, next) => {
  if (getToken()) {
    let userInfo = store.state.user.userInfo;
    if (!userInfo.name) {
      try {
        await store.dispatch("GetUserInfo")
        await store.dispatch('updateAccessMenu')
        if (to.path === '/login') {
          next({ name: 'home_index' })
        } else {
          //Util.toDefaultPage([...routers], to.name, router, next);
          next({ ...to, replace: true })//菜单权限更新完成,重新进一次当前路由
        }
      }  
      catch (e) {
        if (whiteList.indexOf(to.path) !== -1) { // 在免登录白名单，直接进入
          next()
        } else {
          next('/login')
        }
      }
    } else {
      if (to.path === '/login') {
        next({ name: 'home_index' })
      } else {
        if (hasPermission(to, store.getters.accessMenu)) {
          Util.toDefaultPage(store.getters.accessMenu,to, routes, next);
        } else {
          next({ path: '/403',replace:true })
        }
      }
    }
  } else {
    if (whiteList.indexOf(to.path) !== -1) { // 在免登录白名单，直接进入
      next()
    } else {
      next('/login')
    }
  }
  let menu = Util.getMenuByName(to.name, store.getters.accessMenu);
  Util.title(menu.title);
});

Router.afterEach((to) => {
  window.scrollTo(0, 0);
});
```

每次路由跳转的时候都要判断权限，这里的判断也很简单，因为菜单的 `name` 与路由的 `name` 是一一对应的，而后端返回的菜单就已经是经过权限过滤的

如果根据路由 `name` 找不到对应的菜单，就表示用户有没权限访问

如果路由很多，可以在应用初始化的时候，只挂载不需要权限控制的路由。取得后端返回的菜单后，根据菜单与路由的对应关系，筛选出可访问的路由，通过 `addRoutes` 动态挂载

这种方式的缺点：

- 菜单需要与路由做一一对应，前端添加了新功能，需要通过菜单管理功能添加新的菜单，如果菜单配置的不对会导致应用不能正常使用
- 全局路由守卫里，每次路由跳转都要做判断

**（3）按钮权限**

- 用 `v-if` 判断
- 自定义指令

## Vuex

### Vuex 的基本操作？

- state、getters、mutations 和 actions
- state：定义状态
- getters： 定义状态计算逻辑
- mutations： 修改状态
- actions：异步修改状态

- `store.commit()` 触发 mutations
- `store.dispatch()` 触发 actions
- 组件在计算属性中通过 `this.$store.state` 访问状态，通过 `this.$store.getters` 访问 getters
- 组件通过 `this.$store.commit()` 触发 mutations
- 组件通过 `this.$store.dispatch()` 触发 actions

- `mapState()`、`mapGetters()`、`mapMutations()`、` mapActions() ` 批量声明

### 为什么要用 Vuex？

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式，Vuex 的状态存储是**响应式**的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

当**多个组件拥有同一个状态**的时候，vuex 能够很好的帮我们处理，可以很好的使用 vue **开发者工具调试** vuex 的状态，这些优势是 localStorage 不能够很好的模拟的。

### Vuex 中 action 和 mutation 的区别？

- Mutation 专注于修改 State，理论上是修改 State 的唯一途径；Action 业务代码、异步请求。
- Mutation：必须同步执行；Action：可以异步，但不能直接操作 State。
- 在视图更新时，先触发 actions，actions 再触发 mutation
- mutation 的参数是 state，它包含 store 中的数据；store 的参数是 context，它是 state 的父级，包含 state、getters

### 双向绑定和 vuex 是否冲突？

是有冲突的。`v-model` 会去修改 state 的值，但是 vuex 数据修改又必须经过 mutation，这样就冲突了。

解决方法：
- `<input>` 中绑定 value，然后侦听 `input` 或者 `change` 事件，在事件回调中调用方法触发 mutation
- `v-model` 绑定带有 setter 的双向绑定计算属性：

```js
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}
```

## Vue Router

### Vue-Router 的懒加载如何实现?

非懒加载：

```js
import List from '@/components/list.vue'
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
```

懒加载：箭头函数 import

```js
const List = () => import('@/components/list.vue')
const router = new VueRouter({
  routes: [
    { path: '/list', component: List }
  ]
})
```

### 路由的 hash 和 history 模式的区别？

Vue Router 有两种模式：**hash 模式**和**history 模式**。默认的路由模式是 hash 模式。

**hash 模式**

URL 带着一个#，例如：[www.abc.com/#/vue](https://link.juejin.cn?target=http%3A%2F%2Fwww.abc.com%2F%23%2Fvue "http://www.abc.com/#/vue")，它的 hash 值就是 `#/vue`。

hash 值会出现在 URL 里面，但是不会出现在 HTTP 请求中，对后端完全没有影响。所以改变 hash 值，不会重新加载页面。这种模式的浏览器支持度很好，低版本的 IE 浏览器也支持这种模式。hash 路由被称为是前端路由，已经成为 SPA（单页面应用）的标配。

**history 模式**

history 模式的 URL 中没有#，它使用的是传统的路由分发模式，即用户在输入一个 URL 时，服务器会接收这个请求，并解析这个 URL，然后做出相应的逻辑处理。 但是，history 模式需要后台配置支持。如果后台没有正确配置，访问时会返回 404。

history api 可以分为两大部分，切换历史状态和修改历史状态：

- **修改历史状态**：包括了 HTML5 History Interface 中新增的 `pushState()` 和 `replaceState()` 方法，这两个方法应用于浏览器的历史记录栈，提供了对历史记录进行修改的功能。只是当他们进行修改时，虽然修改了 url，但浏览器不会立即向后端发送请求。==如果要做到改变 url 但又不刷新页面的效果，就需要前端用上这两个 API。==
- **切换历史状态：** 包括 `forward()`、`back()`、`go()` 三个方法，对应浏览器的前进，后退，跳转操作。

**对比**

- pushState() 设置的新 URL 可以是与当前 URL 同源的任意 URL；而 hash 只可修改 # 后面的部分，因此只能设置与当前 URL 同文档的 URL；
- pushState() 设置的新 URL 可以与当前 URL 一模一样，这样也会把记录添加到栈中；而 hash 设置的新值必须与原来不一样才会触发动作将记录添加到栈中；
- pushState() 通过 stateObject 参数可以添加任意类型的数据到记录中；而 hash 只可添加短字符串；
- pushState() 可额外设置 title 属性供后续使用。
- hash 模式下，仅 hash 符号之前的 url 会被包含在请求中，后端如果没有做到对路由的全覆盖，也不会返回 404 错误；history 模式下，前端的 url 必须和实际向后端发起请求的 url 一致，如果没有对用的路由处理，将返回 404 错误。

### `$route 和$router` 的区别？

- $route 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- $router 是“路由实例”对象包括了路由的跳转方法，钩子函数等。

### 如何获取页面的 hash 变化？

**（1）监听 $route 的变化**

```js
// 监听,当路由发生变化的时候执行
watch: {
  $route: {
    handler: function(val, oldVal){
      console.log(val);
    },
    // 深度观察监听
    deep: true
  }
},
```

**（2）window.location.hash 读取#值**

window.location.hash 的值可读可写，读取来判断状态是否改变，写入时可以在不重载网页的前提下，添加一条历史访问记录。

### params 和 query 的区别

**用法**：query 要用 path 来引入，params 要用 name 来引入，接收参数都是类似的，分别是 `this.$route.query.name` 和 `this.$route.params.name` 。

**url 地址显示**：query 更加类似于 ajax 中 get 传参，params 则类似于 post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示

**注意**：query 刷新不会丢失 query 里面的数据 params 刷新会丢失 params 里面的数据。

### Vue-router 导航守卫有哪些？

- 全局前置/钩子：
	- **beforeEach** 全局前置守卫，进入路由之前
	- **beforeResolve** 全局解析守卫，在 beforeRouteEnter 调用之后调用
	- **afterEach** 全局后置钩子，进入路由之后
- 路由独享的守卫：
	- **beforeEnter** 为某些路由单独配置守卫
- 组件内的守卫：
	- **beforeRouteEnter** 进入组件前触发
	- **beforeRouteUpdate** 当前地址改变并且该组件被复用时触发
	- **beforeRouteLeave** 离开组件被调用
