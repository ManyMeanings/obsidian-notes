## Vue

### 对 SPA 单页面的理解？✨

SPA（ single-page application ）**仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS**。一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是**利用路由机制**实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

#### SPA 的优点和缺点？

**优点：**

- ==用户体验好==、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
- 基于上面一点，SPA 相对==对服务器压力小==；
- ==前后端职责分离==，架构清晰，前端进行交互逻辑，后端负责数据处理；

**缺点：**

- ==初次加载耗时多==：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
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

### MVC、MVP 和 MVVM 的区别？✨

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

### Vue 的生命周期？✨

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
- **beforeDestroy**（*Vue3 为 onBeforeUnmount*）
	- 实例被销毁前调用，此时实例属性与方法仍可访问
	- 可用于一些定时器或订阅的取消
- **destroyed**（*Vue3 为 onUnmounted*）
	- 完全销毁一个实例。可清理它与其它实例的连接，解绑它的全部指令及事件监听器
	- 并不能清除 DOM，仅仅销毁实例
- **errorCaptured** 
	- 当捕获一个来自子孙组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 `false` 以阻止该错误继续向上传播。
- **deactivated**
	- 被 `keep-alive` 缓存的组件停用时调用。
- **activated**
	- 被 `keep-alive` 缓存的组件激活时调用。
- **setup**
	- 使用组合式 API 时， `setup()` 取代了 `beforeCreate` 和 `created`，vue3 的生命周期就是在

#### 父子组件的生命周期关系？

- 创建： 父 created -> 子 created -> 子 mounted -> 父 mounted
- 更新： 父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
- 销毁： 父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

#### 在哪个生命周期发送请求？

`created` 是在组件实例一旦创建完成的时候立刻调用，这时候页面 `dom` 节点并未生成；`mounted` 是在页面 `dom` 节点渲染完毕之后就立刻执行的。触发时机上 `created` 是比 `mounted` 要更早的，两者的相同点：都能拿到实例对象的属性和方法。 讨论这个问题本质就是触发的时机，放在 `mounted` 中的请求有可能导致页面闪动（因为此时页面 `dom` 结构已经生成），但如果在页面加载前完成请求，则不会出现此情况。建议对页面内容的改动放在 `created` 生命周期当中。

#### `beforeCreated` 和 `created` 中间都做了什么？

初始化 `data`、`props`、`computed`、`watcher`、`provide`。

### Computed 和 watch 的区别？✨

**computed**

- 本质是一个具备缓存的 Watcher，只有依赖属性发生变化时才会更新视图，而且是在下次调用时重新计算结果的
- 不支持异步，当 `computed` 内有异步操作时无效，无法监听数据的变化
- 当需要进行数值计算，并且依赖于其它数据时，应该使用 `computed`，因为可以利用 `computed` 的缓存特性，避免每次获取值时都要重新计算

**watch**

- 没有缓存性，更多的是观察的作用，某些数据变化时会执行回调
- watch 支持异步；可以设置异步返回前的中间状态
- 可以在初始化时执行回调
- 可以深度监听对象属性
- 可以设置回调的执行时机，通过设置 `sync` 属性可以在当前队列执行，默认是下一队列
- 通过 `vm.$watch` 注册的监听，会返回一个 `unWatch` 函数，调用该函数可以取消监听

### v-if 和 v-show 的区别✨

- **控制方式**
	- v-if 根据条件动态渲染或删除 dom 元素
	- v-show 在条件不满足时在 dom 元素上添加 `display:none`
- **编译过程**
	- `v-if` 切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件，触发组件的生命周期
	- `v-show` 只是简单的基于 css 切换
- **性能消耗
	- v-if 有更高的切换开销
	- v-show 有更高的初始渲染开销
- **使用场景**
	- v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景
	- v-show 适用于需要频繁切换条件的场景

### Vue 的 data 为什么必须是函数？✨

组件可能会被用来创建多个实例，如果 data 是一个对象，所有实例的 data 都会指向同一个引用地址，使得实例的状态会互相影响。

使用函数时，每次创建实例时调用函数都会返回一个新的对象，从而避免状态影响。

根实例对象 `data` 可以是对象也可以是函数（根实例是单例），不会产生数据污染情况

### Vue 的优点？

- 轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十 `kb` ；
- 双向数据绑定：在数据操作方面更为简单；
- 组件化：保留了 `react` 的优点，实现了 `html` 的封装和重用，在构建单页面应用方面有着独特的优势；
- 视图，数据，结构分离：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作；
- 虚拟 DOM：`dom` 操作是非常耗费性能的，不再使用原生的 `dom` 操作节点，极大解放 `dom` 操作，但具体操作的还是 `dom` 不过是换了另一种方式；

### Vue 和 React 的对比？✨

- 数据流向的不同。`react` 从诞生开始就推崇单向数据流，而 `Vue` 是双向数据流
- 数据变化的实现原理不同。`react` 使用的是不可变数据（*当值发生改变时，重新开辟一个新的空间储存新的值，原有的值仍储存在之前的内存空间中不被改变。可以更加简单的判断数据是否发生变化*），而 `Vue` 使用的是可变的数据
- 描述 UI 的写法不同：`React` 使用 `JSX`，`Vue` 使用 `template`

#### 如何进行技术选型？

- 团队经验
- 项目需求：快速开发用 Vue，复杂的项目用 React
- 生态系统

### 组件之间的通信方式有哪些？✨

- 父子关系的组件数据传递选择 `props`  与 `$emit` 进行传递，也可选择 `ref`，`$parent`
- 兄弟关系的组件数据传递可选择 `$bus`
- 祖先与后代组件数据传递可选择 `$attrs` 与 `$listeners` 或者 `Provide` 与 `Inject`
- 复杂关系的组件数据传递可以通过 `vuex` ，`localStorage` ， `sessionStorage` 存放共享的变量

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

```js
// 祖先组件
provide(){  
    return {  
        foo:'foo'  
    }  
}  
```

```js
// 后代组件
inject:['foo'] // 获取到祖先组件传递过来的值  
```

**（5）$attrs / $listeners 隔代组件通信**

- `$attrs`：继承所有的父组件属性（除了 prop 传递的属性、class 和 style ），一般用在子组件的子元素上
- `$listeners`：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。（相当于子组件继承父组件的事件）

```html
// 给Grandson隔代传值，communication/index.vue  
<Child2 msg="lalala" @some-event="onSomeEvent"></Child2>  
  
// Child2做展开  
<Grandson v-bind="$attrs" v-on="$listeners"></Grandson>  
  
// Grandson使⽤  
<div @click="$emit('some-event', 'msg from grandson')">  
{{msg}}  
</div>  
```

**（6）eventBus 事件总线（`$emit / $on`）父子、隔代、兄弟组件通信**

创建并 import 一个空的 Vue 实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信，包括父子、隔代、兄弟组件。

```js
// event-bus.js 
import { Vue } from "vue";
export const EventBus = new Vue();
```

```js
import { EventBus } from "./event-bus.js";
// com1
// 触发事件
EventBus.$emit('addition', { num:this.num++ })
// com2
// 监听事件
EventBus.$on('addition', param => { this.count = this.count + param.num; })
```

**（7）状态管理库（Vuex 或 Pinia）**

- `state` 用来存放共享变量的地方
- `getter`，可以增加一个 `getter` 派生状态，(相当于 `store` 中的计算属性），用来获得共享变量的值
- `mutations` 用来存放修改 `state` 的方法。
- `actions` 也是用来存放修改 state 的方法，不过 `action` 是在 `mutations` 的基础上进行。常用来做一些异步操作

**（8）localStorage / sessionStorage**

#### 在实际项目中，vue 组件通信有哪些注意点 ？

- **明确通信方式**：根据具体情况选择适合的通信方式。
- **组件解耦**：组件之间应该尽量保持解耦，即一个组件不应该过于依赖其他组件的内部实现细节。通过明确定义 props 和事件接口，组件可以更加独立地工作，提高复用性和可维护性。
- **单向数据流**：在 Vue 中，数据流是单向的，自上而下。父组件通过 props 向子组件传递数据，子组件通过事件向父组件发送消息。遵循这个单向数据流可以使数据流动更加可控和可预测。
- **避免过度通信**：过多的组件通信可能导致代码复杂性增加。在设计组件通信时，要避免过度通信，只传递必要的数据和事件，以保持代码的简洁性和可读性。
- **适当使用事件总线**：事件总线（Event Bus）是一种简单的组件通信方式，可以在组件之间进行事件的发布和订阅。但是，过度使用事件总线可能导致代码难以维护和调试，因此应该谨慎使用，并在必要时考虑其他更适合的通信方式。
- **谨慎使用 provide/inject**：provide/inject 是一种高级的组件通信方式，可以在祖先组件中提供数据，并在后代组件中注入使用。然而，过度使用 provide/inject 可能导致组件之间的依赖关系不明确，难以追踪和理解，因此需要谨慎使用。
- **使用 Vuex 进行状态管理**：如果组件之间需要共享状态或进行复杂的通信，考虑使用 Vuex 进行状态管理。Vuex 提供了一个集中式的状态管理方案，可以更好地管理和共享组件之间的状态。

### 有用过 Vue3 吗，和 Vue2 有哪些区别？✨

[Vue 3 迁移指南](https://v3-migration.vuejs.org/zh/)

**速度更快**

- 重写了虚拟 `Dom` 实现
- 编译模板的优化
- 更高效的组件初始化

**体积更小**

通过 `webpack` 的 `tree-shaking` 功能，可以将无用模块“剪辑”，仅打包需要的

能够 `tree-shaking`，有两大好处：

- 对开发人员，能够对 `vue` 实现更多其他的功能，而不必担忧整体体积过大
- 对使用者，打包出来的包体积变小了

**更易维护**

- compositon API
- 更好的 Typescript 支持

**新增特性**

- `framents` 组件现在支持有多个根节点
- `Teleport` 可以在组件的逻辑位置写模板代码，然后在 `Vue` 应用范围之外渲染它
- `composition API` 将相同功能的变量进行一个集中式的管理
- `createRenderer` 构建自定义渲染器

**非兼容变更**

- 组件上 `v-model` 用法已更改
- `<template v-for>` 和 非 `v-for` 节点上 `key` 用法已更改
- 在同一元素上使用的 `v-if` 和 `v-for` 优先级已更改
- `v-for` 中的 `ref` 不再注册 `ref` 数组
- 只能使用普通函数创建功能组件
- 异步组件现在需要 `defineAsyncComponent` 方法来创建
- 自定义指令 API 已更改为与组件生命周期一致

### vue3.0 为什么要引入 Composition API？

- 在逻辑组织和逻辑复用方面，`Composition API` 是优于 `Options API`
- 因为 `Composition API` 几乎是函数，会有更好的类型推断。
- `Composition API` 对 `tree-shaking` 友好，代码也更容易压缩
- `Composition API` 中见不到 `this` 的使用，减少了 `this` 指向不明的情况
- 如果是小型组件，可以继续使用 `Options API`，也是十分友好的

### 说说 Vue 3.0 中 Tree-shaking 特性？举例说明一下？

`Tree shaking` 是一种通过清除多余代码方式来优化项目打包体积的技术，简单来讲，就是在保持代码运行结果不变的前提下，去除无用的代码。

**原理**

`Tree shaking` 是基于 `ES6` 模板语法（`import` 与 `exports`），主要是借助 `ES6` 模块的静态编译思想，在编译时就能确定模块的依赖关系，以及输入和输出的变量

`Tree shaking` 无非就是做了两件事：

- 编译阶段利用 `ES6 Module` 判断哪些模块已经加载
- 判断那些模块和变量未被使用或者引用，进而删除对应代码

### v-if 与 v-for 为什么不建议一起使用 ？

1. Vue2 中 v-for 优先级更高，每次先循环再判断，带来性能浪费
2. Vue3 中 v-if 优先级更高，因为 v-if 中如果用了 v-for 定义的项目或索引，会报错
3. 要避免出现这种情况，则在外层嵌套 `template`（页面渲染不生成 `dom` 节点），在这一层进行 v-if 判断，然后在内部进行 v-for 循环

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

**原理**

- 每个 Vue 文件都将对应一个唯一的 id，该 id 根据文件路径名和内容 hash 生成，通过组合形成 scopeId。
- 编译 template 标签时，会为每个标签添加了当前组件的 scopeId：data-v-xxx
- 编译 style 标签时，会根据当前组件的 scopeId 通过属性选择器和组合选择器输出样式

```
.box[data-v-11c6864c]{
    background:red;
}
<template>
    <div class="box" data-v-11c6864c>dom</div>
</template>
```

具体来说，它是 **通过 vue-loader 实现** 的，实现过程大致分 3 步：

1. 每个 Vue 文件都将对应一个唯一的 id，该 id 根据文件路径名和内容 hash 生成，通过组合形成 scopeId。
2. 首先 vue-loader 会解析 .vue 组件，提取出 template、script、style 对应的代码块；
3. 然后构造组件实例，在组件实例的选项上绑定 ScopedId；
4. 最后对 style 的 CSS 代码进行编译转化，应用 ScopedId 生成选择器的属性；

#### scoped 如何修改第三方组件库的样式？

需要样式穿透，用到深度选择器 `/deep/`

```html
<style lang="scss" scoped>
.box-card {
  /deep/.el-card__body {
    padding: 10px;
  }
}
</style>

```

**原理**

scoped 后选择器最后默认会加上当前组件的一个标识，比如 `[data-v-49729759]`
用了样式穿透后，在 deep 之后的选择器最后就不会加上标识。

### vue 初始化页面闪动问题？

使用 vue 开发时，在 vue 初始化之前，由于 div 是不归 vue 管的，所以我们写的代码在还没有解析的情况下会容易出现花屏现象，看到类似于{{message}}的字样，虽然一般情况下这个时间很短暂，但是还是有必要让解决这个问题的。

首先：在 css 里加上以下代码：

```css
[v-cloak] { display: none;}
```

如果没有彻底解决问题，则在根元素加上 `style="display: none;" :style="{display: 'block'}"`

**原理**

先通过样式隐藏挂载内容，等到数据渲染完成后，v-cloak 属性会自动去除，页面会显示最终效果。

### 什么是自定义指令，怎么实现？

Vue 自定义指令可以用来**复用代码，封装常用的 DOM 操作或行为**。常见的自定义指令有监听滚动事件、图片懒加载、设置 loading、权限管理等。

一个自定义指令由一个包含类似组件生命周期钩子的对象来定义。钩子函数会接收到指令所绑定元素作为其参数。自定义指令可以全局注册或局部注册，注册后可以在模板中使用 v- 前缀调用。

**钩子函数**

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)
- `update`：所在组件的 `VNode` 更新时调用，但是可能发生在其子 `VNode` 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新
- `componentUpdated`：指令所在组件的 `VNode` 及其子 `VNode` 全部更新后调用
- `unbind`：只调用一次，指令与元素解绑时调用

**全局注册**

```js
app.directive('fsize', (el, binding) => {
	// 等于同时设定 mounted 和 updated
	el.style.fontSize = binding.value + (binding.arg || 'px');
});

// template
// <div v-fsize:em="20">test</div>
```

**局部注册**

```js
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus() // 页面加载完成之后自动让输入框获取到焦点的小功能
    }
  }
}
```

### 什么是 slot？

通过 `slot` 插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用。有三种插槽：默认插槽、具名插槽、作用域插槽

- `v-slot` 属性只能在 `<template>` 上使用，但在只有默认插槽时可以在组件标签上使用
- 默认插槽名为 `default`，可以省略 default 直接写 `v-slot`
- 简写为 `#` 时不能不写参数，写成 `#default`
- 可以通过解构获取 `v-slot={user}`，还可以重命名 `v-slot="{user: newName}"` 和定义默认值 `v-slot="{user = '默认值'}"`

```html
<template> 
  <slot name="footer" testProps="子组件的值">
          <h3>没传footer插槽</h3>
    </slot>
</template>
```

```html
<child> 
    <!-- 把v-slot的值指定为作⽤域上下⽂对象 -->
    <template v-slot:default="slotProps">
      来⾃⼦组件数据：{{slotProps.testProps}}
    </template>
    <template #default="slotProps">
      来⾃⼦组件数据：{{slotProps.testProps}}
    </template>
</child>
```

### 说说你对 vue 的 mixin 的理解，有什么应用场景？

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

本质其实就是一个 `js` 对象，它可以包含我们组件中任意功能选项，如 `data`、`components`、`methods`、`created`、`computed` 等等我们只要将共用的功能以对象的方式传入 `mixins` 选项中，当组件使用 `mixins` 对象时所有 `mixins` 对象的选项都将被混入该组件本身的选项中来

- 当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。
- 数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。
- 同名钩子函数将合并为一个数组，因此都将被调用。混入对象的钩子将在组件自身钩子**之前**调用。
- 值为对象的选项，例如 `methods`、`components` 和 `directives`，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。

```js
// 全局混入
Vue.mixin({
  created: function () {
      console.log("全局混入")
    }
})

// 局部混入
Vue.component('componentA',{
  mixins: [myMixin]
})
```

### Vue 中的过滤器了解吗？

过滤器（`filter`）不改变原始数据，可以理解为一个纯函数，它接收一个值作为参数，进行加工处理后返回。

`vue` 中的过滤器可以用在两个地方：双花括号插值和 `v-bind` 表达式，过滤器应该被添加在 `JavaScript` 表达式的尾部，由“管道”符号指示：

```html
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

**定义 filter**

```js
// 局部
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}

// 全局
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})
```

注意: `Vue3` 中已废弃 `filter`

### 常用的修饰符？✨

**表单修饰符**

修饰用得最多的是 `v-model`

- `.lazy`：光标离开标签的时候，才会将值赋予给 `value`（*在 `change` 事件之后再进行信息同步*）
- `.trim`：自动过滤用户输入的首尾空格字符
- `.number`：自动将用户的输入值转为数值类型（*使用 `parseFloat` 解析，解析不了则会返回原来的值*）

**事件修饰符**

- `.stop`：阻止事件冒泡
- `.prevent`：阻止默认事件
- `.self` ：只当在 `event.target` 是当前元素自身时触发处理函数
- `.once`：事件只触发一次
- `.native`：只能用在组件上，可以让父组件接收到原生事件，否则只能接收自定义事件（*$emit*）

**v-bind 修饰符**

- `.sync`：能对 `props` 进行一个双向绑定

```js
//父组件
<comp :myMessage.sync="bar"></comp> 

//子组件
this.$emit('update:myMessage',params);
```

**鼠标按钮修饰符**

- `.left` 左键点击
- `.right` 右键点击
- `.middle` 中键点击

**键盘修饰符**

修饰键盘事件（`onkeyup`，`onkeydown`）

`keyCode` 存在很多，但 `vue` 为我们提供了别名，分为以下两种：

- 普通键（enter、tab、delete、space、esc、up...）
- 系统修饰键（ctrl、alt、meta、shift...）

```html
<input type="text" @keyup.keyCode="shout()">
```

### 什么是异步组件？

在单页应用中，如果没有用懒加载，运用 webpack 打包后的文件将会异常的大，造成进入首页时，需要加载的内容过多，延时过长，不利于用户体验，而运用懒加载则可以将页面进行划分，需要的时候加载页面，可以有效的分担首页所承担的加载压力，减少首页加载用时。

**Vue 允许将组件定义为一个异步解析（加载）组件定义的工厂函数，即 Vue 只在实际需要渲染组件时，才会触发调用工厂函数，并且将结果缓存起来，用于将来再次渲染**

- ==异步加载组件==：用不到的组件不会加载，因此网页打开速度会很快，当你用到这个组件的时候，才会通过异步请求进行加载；
- ==组件缓存==：通过异步加载的组件会缓存起来，当你下一次再用到这个组件时，丝毫不会有任何的迟疑，组件很快会从缓存中加载出来。

**Vue2 异步组件**

```js
//1 全局：
Vue.component('component-name',
 （）=> import('./my-async-componnet') 
  // 这个 `import` 函数会返回一个 `Promise` 对象
 )
//2 局部：
 new Vue({
   components: {
       'component-name': () =>  import('./my-async-componnet')
       //这个 `import` 函数会返回一个 `Promise` 对象。
   }
 })
```

**Vue3 异步组件**

```js
import { defineAsyncComponent } from 'vue'

// 全局注册
app.component('MyComponent', defineAsyncComponent(() => import('./components/MyComponent.vue') ))

// 局部注册
const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)

// 高级异步组件
const AsyncComp = defineAsyncComponent({
  // 加载函数
  loader: () => import('./Foo.vue'),

  // 加载异步组件时使用的组件
  loadingComponent: LoadingComponent,
  // 展示加载组件前的延迟时间，默认为 200ms
  delay: 200,

  // 加载失败后展示的组件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 时间限制，并超时了
  // 也会显示这里配置的报错组件，默认值是：Infinity
  timeout: 3000
})
```

**一个完整的例子**

动态切换组件的场景，引入并定义异步组件，使用 `keep-alive` 缓存状态

```html
<template>
	<div id="app">
		<button @click="fn">xx</button>
		<keep-alive>
			<components :is="com"></components>
		</keep-alive>
	</div>
 </template>
 
 <script>
 	import HelloWorld from './components/HelloWorld.vue'
 	export default {
 		name: 'app',
 		data() {
 			return {
 				flag: true,
 				com: "HelloWorld",
 			}
 		},
 		components: {
 			HelloWorld,
 			// HelloWorld2,
 			// "HelloWorld2":() =>  import('./components/HelloWorld2.vue')
 			"HelloWorld2": () => ({
 				// 需要加载的组件 (是一个 `Promise` 对象)
 				component: import('./components/HelloWorld2.vue'),
 				// 异步组件加载时使用的组件
 				loading: import('./components/Palcebox.vue'),
 				// 加载失败时使用的组件
 				error: import('./components/Palcebox.vue'),
 				// 展示加载时组件的延时时间。默认值是 200 (毫秒)
 				delay: 200,
 				// 如果提供了超时时间且组件加载也超时了，
 				// 则使用加载失败时使用的组件。默认值是：`Infinity`
 				timeout: 3000
 			})
 		},
 		methods: {
 			fn() {
 				this.flag = !this.flag
 				this.com = this.flag ? "HelloWorld" : "HelloWorld2"
 			}
 		}
 	}
 </script>
```

### Vue 组件里写的原生 addEventListeners 监听事件，要手动去销毁吗？

要在 beforeDestroy 手动销毁，否则如果在 mounted 中使用 addEventListeners，可能会多次重复注册导致内存泄漏。

### Vue 中父组件如何监听子组件的生命周期？

**使用 on 和 emit**

父组件定义自定义事件，子组件在生命周期钩子函数中使用 `$emit` 触发监听

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

### 如何自定义 v-model

**Vue2**

允许一个自定义组件在使用 `v-model` 时定制 prop 和 event。默认情况下，一个组件上的 `v-model` 会把 `value` 用作 prop 且把 `input` 用作 event，但是一些输入类型比如单选框和复选框按钮可能想使用 `value` prop 来达到不同的目的。使用 `model` 选项可以回避这些情况产生的冲突。

```vue
<input type="text"
  :value="msg1"
  @input="$emit('change1', $event.target.value)"
>

<script>
export default {
  model: {
    prop: 'msg1', // 对应 props msg，默认是 value
    event: 'change1' // 默认是 input
  },
  props: {
    msg1: {
      type: String,
      default: ''
    }
  }
}
</script>
```

**Vue3**

```vue
<!-- Parent.vue --> 
<Child v-model="count" />

<!-- Child.vue -->
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>parent bound v-model is: {{ model }}</div>
</template>
```

```vue
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>
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
const { proxy } = getCurrentInstance(); // 相当于 this
proxy.$axios()
```

### Vue 中组件和插件有什么区别？

组件 `(Component)` 是用来构成你的 `App` 的业务模块，它的目标是 `App.vue`

插件 `(Plugin)` 是用来增强你的技术栈的功能模块，通常用来为 `Vue` 添加全局功能。

**插件编写**

`vue` 插件的实现应该暴露一个 `install` 方法。这个方法的第一个参数是 `Vue` 构造器，第二个参数是一个可选的选项对象

```js
// Vue2 编写插件
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或 property
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }
  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })
  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })
  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}

// 注册插件
Vue.use(MyPlugin,{ /* ... */} )
```

```js
// Vue3 编写插件
// src\plugins\i18n.js
export default {
  // 参数app：由Vue的creatApp生成的app
  // 参数options：用户传入的选项
  install: (app, options) => {
    console.log(options); // 全局对象
    app.config.globalProperties.$translate = (key) => {
      return key.split(`.`).reduce((o, i) => {
        if (o) return o[i];
      }, options);
    };
    // 全局注入​
    app.provide(`i18n`, options); // 全局自定义指令
    app.directive("my-directive", {
      bind(el, binding, vnode) {},
      inserted(el, binding, vnode) {},
      update(el, binding, vnode, oldVnode) {},
      componentUpdated(el, binding, vnode) {},
      unbind(el, binding, vnode) {},
    });
  },
};
// 使用插件
app.use(i18nPlugin, i18nStrings); 
app.mount('#app');
```

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

### 如何封装一个通用的弹窗组件？

1. 确定弹窗的结构：遮罩层、头部、内容、底部按钮
2. 组件 API 设计：
	1. props：标题、是否展示底部、尺寸、挂载的 dom 元素等
	2. slot：头部、内容和底部的默认插槽
	3. event：打开、关闭、确认、取消等触发的事件
3. 实现思路：
	1. 最外层通过  `Teleport` 内置组件进行包裹，把弹窗直接挂载到 `body` 上
	2.  判断传入的 content 类型，使用 slot 插槽或者 h 函数渲染
	3. 实现 API 形式调用，将组件实例挂载到全局
		1. `Vue2` 使用 `Vue.extend` 的方式获得组件实例，然后挂载到 `body` 上
		2. `Vue3` 使用 `createVNode` 和 `render` 创建节点，然后挂载到 `body` 上
	4. 事件处理：按钮的点击事件触发的时候使用 `emit` 触发父组件的监听

### Vue 项目如何做权限控制？

**接口权限**

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

**菜单权限**

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

**按钮权限**

- 用 `v-if` 判断
- 自定义指令

### Vue 项目如何做前端埋点？

埋点按照数据获取的方式一般分为一下 3 种:

- 页面埋点：统计用户进入或离开页面的各种维度信息，如页面浏览次数（PV）、浏览页面人数（UV）、页面停留时间、浏览器信息等。
- 点击埋点：统计用户在应用内的每一次点击事件，如新闻的浏览次数、文件下载的次数、推荐商品的命中次数等。
- 曝光埋点：统计具体区域是否被用户浏览到，如活动的引流入口的显示、投放广告的显示等。

**页面埋点**

将需要埋点的页面放到单独文件里导出，使用 Vue Router 全局导航守卫 `beforeResolve`，在进入页面后根据路由和 query 是否匹配条件进行上报。

```js
// track.js
export const PVInfoMap = {
  '/share-doc': {
    id: 'share_doc_page.pv',
    query: {},
    data: {}
  }
}
```

```js
import router from '@/router'
import { trackPage } from '@/utils/track.js'
import { PVInfoMap } from '@/plugins/config/track.js'
...
router.beforeResolve((to, from, next) => {
  const { id, query, data } = PVInfoMap[to.path] || {}
  if(id) {
    if(query && Object.keys(query).length) {
      const isMatch = (route) => {
        if(!route) return false
        return Object.keys(query).every(key => {
          return query[key] == route.query[key]
        })
      }
      if(isMatch(to) && !isMatch(from)) {
        trackPage(id,data || {}) // 上报数据
      }
    } else {
      if(from.path !== to.path) {
        trackPage(id, data || {}) // 上报数据
      }
    }
  }
  next()
})
```

**点击埋点&曝光埋点**

使用自定义指令实现，曝光使用 IntersectionObserver 观察元素是否在视窗内，并且曝光上报只上报一次，上报之后移除观察。设定每 2 秒进行一次上报。 如何解决曝光的漏报（定时器 2 秒之内的用户退出）和多报： a. 漏报：保存 localStorage，下次进入之后如果有数据则上报，如果用户再不进入，对漏报的几条数据可忽略 b. 多报：IntersectionObserver 监听曝光，上报时候移除元素的监听

```js
import Vue from 'vue'
import Exposure from './exposure'
import Click from './click'

// 实例化
const exp = new Exposure()
const cli = new Click()

Vue.directive('track', {
  bind(el, binding) {
    const { arg } = binding
    arg.split('|').forEach(item => {
      // 点击
      if (item === 'click') {
        cli.add({ el })
      } else if (item === 'exposure') {
        exp.add({ el })
      }
    })
  }
})
```

```js
import 'intersection-observer'
import { track } from './sendData'

// 节流时间调整，默认100ms
IntersectionObserver.prototype['THROTTLE_TIMEOUT'] = 300

export default class Exposure {
  constructor(maxNum = 20) {
    this.cacheDataArr = []
    this.maxNum = maxNum
    this._timer = 0
    this._observer = null
    this.init()
  }

  /**
   * 初始化
   */
  init() {
    const self = this
    // 边界处理
    this.trackFromLocalStorage()
    this.beforeLeaveWebview()

    // 实例化监听
    this._observer = new IntersectionObserver(function(entries, observer) {
      entries.forEach((entry) => {
        // 出现在视窗中
        if (entry.isIntersecting) {
          // 清除当前定时器
          clearInterval(this._timer)

          // 获取参数
          const tp = entry.target.attributes['track-params'].value
          // 收集参数统一上报，减少网络请求
          self.cacheDataArr.push(tp)
          // 曝光之后取消观察
          self._observer.unobserve(entry.target)

          if (self.cacheDataArr.length >= self.maxNum) {
            self.track()
          } else {
            self.storeIntoLocalStorage(self.cacheDataArr)
            if (self.cacheDataArr.length > 0) {
              // 2秒上报一次
              self._timer = setInterval(function() {
                self.track()
              }, 2000)
            }
          }
        }
      })
    },
    {
      root: null,
      rootMargin: '0px',
      threshold: 0.5
    })
  }

  /**
   * 给元素添加监听
   * @param {Element} entry 
   */
  add(entry) {
    this._observer && this._observer.observe(entry.el)
  }

  /**
   * 埋点上报
   */
  track() {
    const trackData = this.cacheDataArr.splice(0, this.maxNum)
    track(trackData)
    // 更新localStoragee
    this.storeIntoLocalStorage(this.cacheDataArr)
  }

  /**
   * 存储到localstorage, 防止在设定上报时间内用户退出
   * @param { Arrary } data 
   */
  storeIntoLocalStorage(data) {
    window.localStorage.setItem('cacheTrackData', data)
  }

  /**
   * 首次进入先获取localStorage中的数据，也就是用户上次退出未上报的数据
   */
  trackFromLocalStorage() {
    const cacheData = window.localStorage.getItem('cacheTrackData')
    if (cacheData) {
      track(cacheData)
    }
  }

  /**
   * 用户退出系统时调用方法，需要和客户端同学协商注册事件
   */
  beforeLeaveWebview() {
    // 客户端自定义事件监听上报
  }
}
```

### 怎么处理 vue 项目中的错误?

**后端接口错误**

通过 `axios` 的 `interceptor` 实现网络请求的 `response` 先进行一层拦截

```js
apiClient.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    if (error.response.status == 401) {
      router.push({ name: "Login" });
    } else {
      message.error("出错了");
      return Promise.reject(error);
    }
  }
);
```

**代码逻辑错误**

```js
// 全局错误处理
Vue.config.errorHandler = function (err, vm, info) {
  // handle error
  // `info` 是 Vue 特定的错误信息，比如错误所在的生命周期钩子
  // 只在 2.2.0+ 可用
}
```

```js
// 生命周期钩子
errorCaptured(err,vm,info) {
  console.log(`cat EC: ${err.toString()}\ninfo: ${info}`); 
  return false;
}
```

## Vuex

### Vuex 的基本操作？✨

- `state`：定义状态
- `getters`： 定义状态计算逻辑
- `mutations`： 修改状态
- `actions`：异步修改状态

- `store.commit()` 触发 mutations
- `store.dispatch()` 触发 actions
- 组件在计算属性中通过 `this.$store.state` 访问状态，通过 `this.$store.getters` 访问 `getters`
- 组件通过 `this.$store.commit()` 触发 `mutations`
- 组件通过 `this.$store.dispatch()` 触发 `actions`

- `mapState()`、`mapGetters()`、`mapMutations()`、` mapActions() ` 批量声明

### 为什么要用 Vuex？

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式，Vuex 的状态存储是**响应式**的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

当**多个组件拥有同一个状态**的时候，vuex 能够很好的帮我们处理，可以很好的使用 vue **开发者工具调试** vuex 的状态，这些优势是 localStorage 不能够很好的模拟的。

### Vuex 中 action 和 mutation 的区别？

- Mutation 专注于修改 State，理论上是修改 State 的唯一途径；Action 业务代码、异步请求。
- Mutation：必须同步执行；Action：可以异步，但不能直接操作 State。
- 在视图更新时，先触发 actions，actions 再触发 mutation
- mutation 的参数是 state，它包含 store 中的数据；action 的参数是 context，它是 state 的父级，包含 state、getters

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

监听事件 `hashchange` 加载相应页面

```js
// 定义 Router
class Router {
  constructor() {
    this.routes = {}; // 存放路由path及callback
    this.currentUrl = "";

    // 监听路由change调用相对应的路由回调
    window.addEventListener("load", this.refresh, false);
    window.addEventListener("hashchange", this.refresh, false);
  }

  route(path, callback) {
    this.routes[path] = callback;
  }

  push(path) {
    this.routes[path] && this.routes[path]();
  }
}

// 使用 router
window.miniRouter = new Router();
miniRouter.route("/", () => console.log("page1"));
miniRouter.route("/page2", () => console.log("page2"));

miniRouter.push("/"); // page1
miniRouter.push("/page2"); // page2
```

**history 模式**

history 模式使用传统的路由分发模式，即用户在输入一个 URL 时，服务器会接收这个请求，并解析这个 URL，然后做出相应的逻辑处理。 history 模式需要后台配置支持，如果后台没有正确配置，用户刷新页面时会返回 404（*对于所有路径都返回 `index.html` 文件*）

history api 不会触发页面刷新，只是导致 History 对象发生变化，地址栏会有变化。

- `history.pushState` 浏览器历史纪录添加记录
- `history.replaceState` 修改浏览器历史纪录中当前纪录
- `history.popState` 当 `history` 发生变化时触发，用来监听用户点击浏览器倒退按钮和前进按钮

**history 模式**

`history` 模式核心借用 `HTML5 history api`，`api` 提供了丰富的 `router` 相关属性

- `history.pushState` 浏览器历史纪录添加记录
- `history.replaceState` 修改浏览器历史纪录中当前纪录
- `history.popState` 当 `history` 发生变化时触发

```js
// 定义 Router
class Router {
  constructor() {
    this.routes = {};
    this.listerPopState();
  }

  init(path) {
    history.replaceState({ path: path }, null, path);
    this.routes[path] && this.routes[path]();
  }

  route(path, callback) {
    this.routes[path] = callback;
  }

  push(path) {
    history.pushState({ path: path }, null, path);
    this.routes[path] && this.routes[path]();
  }

  listerPopState() {
    window.addEventListener("popstate", (e) => {
      const path = e.state && e.state.path;
      this.routers[path] && this.routers[path]();
    });
  }
}

// 使用 Router

window.miniRouter = new Router();
miniRouter.route("/", () => console.log("page1"));
miniRouter.route("/page2", () => console.log("page2"));

// 跳转
miniRouter.push("/page2"); // page2
```

### `$route` 和 `$router` 的区别？

- `$route` 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数
- `$router` 是“路由实例对象“包括了路由的跳转方法，钩子函数等。

### Vue Router 基本用法？

```html
<!--App.vue-->
<nav>
<!--使用 router-link 组件进行导航 --> 
<!--通过传递 `to` 来指定链接 --> 
<!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
  <router-link to="/">首页</router-link>
  <router-link :to="{ name: 'post', params: { postId: 1 }, query: {} }">{{ postName }}</router-link>
</nav>

<!-- 路由出口 --> 
<!-- 路由匹配到的组件将渲染在这里 -->
<router-view></router-view>
```

```js
// push，向 history 栈添加一个新的记录
this.$router.push("/main");
this.$router.push({ name: "main" });
this.$router.push({ path: "/main" });

// replace，不会向 history 栈添加一个新的记录，而是替换当前路由
this.$router.replace('/main')

// 前进或后退
this.$router.go(-1)

// 路由传参
// params, 在url不可见
this.$router.push("/main/${id}"); // id会默认塞到params对象的 id属性值里面
this.$router.push({ name: "main", params: { id: "mockid" } });
this.$route.params.id

// query，url中可见
this.$router.push({ name: 'main', query:{ id: 'mockid' } })
this.$route.query.id
```

### 如何获取页面的 hash 变化？

**（1）监听 $route 的变化**

```js
// 监听,当路由发生变化的时候执行
watch: {
  $route: {
    handler: function(val, oldVal){
      console.log(this.$route.params.id) 
      console.log(this.$route.query.id)
    },
    // 深度观察监听
    deep: true,
    immediate: true // 初始化也会触发这个handler
  }
}
```

**beforeRouteUpdate**

使用组件路由守卫 `beforeRouteUpdate`，不能监听初始化的值

```js
beforeRouteUpdate(to, from, next){
	console.log(this.$route.params.id)
	console.log(this.$route.query.id)
	next()
}
```

### params 和 query 的区别？

**用法**：query 要用 path 来引入，params 要用 name 来引入，接收参数都是类似的，分别是 `this.$route.query.name` 和 `this.$route.params.name`

**url 地址显示**：query 更加类似于 ajax 中 get 传参，params 则类似于 post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示

**注意**：query 刷新不会丢失 query 里面的数据 params 刷新会丢失 params 里面的数据。

### Vue Router 导航守卫有哪些？

**全局导航守卫**

- `beforeEach(to, from)` 全局前置守卫，进入路由之前
- `beforeResolve(to, from)` 全局解析守卫，在 beforeRouteEnter 调用之后调用
- `afterEach(to, from)` 全局后置钩子，进入路由之后

**路由独享的守卫**

- `beforeEnter(to, from)` 在路由配置上定义，为某些路由单独配置守卫

**组件内的守卫**

- `beforeRouteEnter(to, from, next)` 当路由进入的时候，会触发这个方法，此时组件实例还没被创建
- `beforeRouteLeave(to, from, next)` 当路由离开的时候，会触发这个方法，可以访问组件实例 `this`
- `beforeRouteUpdate(to, from, next)` 当路由参数发生改变，但是还是在当前路由，会触发这个方法，可以访问组件实例

**守卫的返回值**

- `false`: 取消当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
- 一个路由地址: 通过一个路由地址重定向到一个不同的地址，如同调用 `router.push()`，且可以传入诸如 `replace: true` 或 `name: 'home'` 之类的选项。它会中断当前的导航，同时用相同的 `from` 创建一个新导航。

#### 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。
