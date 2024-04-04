### Vue 的核心机制？

1. **模板编译**：Vue 使用模板来描述组件的结构和渲染逻辑。在 Vue 的内部，模板会被编译成渲染函数。编译过程将模板解析成抽象语法树（AST），然后将 AST 转换为渲染函数。渲染函数是一个 JavaScript 函数，它返回虚拟 DOM（Virtual DOM）。
2. **响应式系统**：Vue 的响应式系统是实现数据驱动视图更新的核心机制。当 Vue 应用初始化时，Vue 会遍历组件的 data 对象中的属性，并使用 Object.defineProperty 方法将这些属性转换为 getter 和 setter。这样一来，当这些属性被读取或修改时，Vue 能够捕获到并触发相应的更新操作。
3. **虚拟 DOM 和 Diff 算法**：Vue 使用虚拟 DOM 来提高渲染性能。在每次组件更新时，Vue 会生成新的虚拟 DOM 树，并与之前的虚拟 DOM 树进行对比。这个对比过程使用 Diff 算法来找出两个虚拟 DOM 树之间的差异。然后，Vue 将这些差异应用到实际的 DOM 上，实现最小化的 DOM 操作，从而提高性能。

### Vue 的响应式原理？

**双向绑定**

==数据变化更新视图，视图变化更新数据：==
- 输入框内容变化时，`Data` 中的数据同步变化。即 `View => Data` 的变化。（通过事件监听的方式实现）
- `Data` 中的数据变化时，文本节点的内容同步变化。即 `Data => View` 的变化。

**响应式原理**

1. Vue 在实例初始化时对 `data` 中所有属性的读写进行劫持，每个属性都会创建一个独立的订阅器（*依赖收集器*） `Dep`，这个过程发生 `Observe` 中。
2. 同时对模版执行编译，找到其中动态绑定的数据，从 `data` 中获取并初始化视图。
3. 同时为每个动态绑定的数据，定义对应的更新函数和订阅者 `Watcher`，并将 `Watcher` 添加到对应的 `Dep` 中，将来数据变化时 `Watcher` 会调用对应的更新函数。
4. 在 `data` 中的数据更新时，对应的 `Dep` 通知所有 `Watcher` 去执行各自的更新函数去更新视图。

**数据变化更新视图的具体实现**

数据劫持结合发布 - 订阅模式

![[Pasted image 20240208165227.png]]

**（1）监听器 Observer 实现**

==目的：让数据对象变得“可观测”。==即每次数据读或写时，我们能感知到数据被读取了或数据被改写了。Vue 2.0 用 `Object.defineProperty()` 来劫持各个数据属性的 `setter / getter`。

```js
/**
 * 循环遍历数据对象的每个属性
 */
function observable(obj) {
  if (!obj || typeof obj !== "object") {
    return;
  }
  let keys = Object.keys(obj);
  keys.forEach((key) => {
    defineReactive(obj, key, obj[key]);
  });
  return obj;
}

/**
 * 将对象的属性用 Object.defineProperty() 进行设置
 */
function defineReactive(data, key, val) {
  var dep = new Dep(); // 当前属性的订阅器
  Object.defineProperty(data, key, {
    enumerable: true,
    configurable: true,
    get: function getter() {
      if (Dep.target) {
        dep.addSub(Dep.target); // 收集当前订阅者
      }
      return val;
    },
    set: function setter(newVal) {
      if (newVal === val) {
        return;
      }
      val = newVal;
      dep.notify(); // 通知所有订阅者更新
    },
  });
}
```

**（2）订阅器 Dep 实现**

目的：收集所有订阅者，一旦数据发生变化，就统一通知更新。

```js
function Dep() {
  this.subs = [];
}
Dep.prototype = {
  addSub: function (sub) {
    this.subs.push(sub);
  },
  notify: function () {
    this.subs.forEach(function (sub) {
      sub.update();
    });
  },
};
Dep.target = null;
```

**（3）订阅者 Watcher 实现**

目的：初始化时绑定更新函数、data 中的属性名，并将自己添加到订阅器中（*把 this 绑定到全局变量 `Dep.target` ，并触发触发订阅器的 get 函数*）

```js
function Watcher(vm, exp, cb) {
  this.vm = vm; // vue 实例对象
  this.exp = exp; // node 节点的 v-model 等指令的属性值
  this.cb = cb; // Watcher 绑定的更新函数
  this.value = this.get(); // 将自己添加到订阅器的操作
}

Watcher.prototype = {
  update: function () {
    this.run();
  },
  run: function () {
    var value = this.vm.data[this.exp];
    var oldVal = this.value;
    if (value !== oldVal) {
      this.value = value;
      this.cb.call(this.vm, value, oldVal);
    }
  },
  get: function () {
    Dep.target = this; // 全局变量 订阅者 赋值
    let value = this.vm.data[this.exp]; // 强制执行监听器里的get函数
    Dep.target = null; // 全局变量 订阅者 释放
    return value;
  },
};
```

**（4）解析器 Compile 实现**

- 解析模板指令，并替换模板数据，初始化视图；
- 将模板指令对应的节点绑定对应的更新函数，初始化相应的订阅者；

```js
function compileText(node, exp) {
  var self = this;
  var initText = this.vm[exp]; // 获取属性值
  this.updateText(node, initText); // dom 更新节点文本值
  // 将这个指令初始化为一个订阅者，后续 exp 改变时，就会触发这个更新回调，从而更新视图
  new Watcher(this.vm, exp, function (value) {
    self.updateText(node, value);
  });
}
```

### 为什么 Vue3 要使用 Proxy 代替 defineProperty？

#### Object.defineProperty 实现响应式

Vue2 在实例初始化时遍历 data ，使用 `Object.defineProperty()` 为对象的每个属性添加 `getter/setter`

```js
function update() {
  app.innerText = obj.foo;
}

function observe(obj) {
  if (typeof obj !== "object" || obj == null) {
    return;
  }
  Object.keys(obj).forEach((key) => {
    defineReactive(obj, key, obj[key]);
  });
}

function defineReactive(obj, key, val) {
  observe(val); // 递归嵌套对象
  Object.defineProperty(obj, key, {
    get() {
      console.log(`get ${key}:${val}`);
      return val;
    },
    set(newVal) {
      if (newVal !== val) {
        observe(newVal) // 新值是对象时需要递归处理
        val = newVal;
        update(); // 修改属性时触发响应式
      }
    },
  });
}

const obj = {}
defineReactive(obj, 'foo', '')
setTimeout(()=>{
    obj.foo = new Date().toLocaleTimeString()
},1000)
```

**存在的问题：**
1. 无法监听对象属性的添加和删除
2. 无法监听数组的项修改（根据 index）、length 修改（*`Object.defineProperty` 本身是可以监控到数组下标的变化的，只是 Vue2 放弃了这个特性。Vue2 重写了数组的 push()、pop()、shift()、unshift()、splice()、sort() 和 reverse() 方法，手动进行监听和触发更新*）
3. 遍历对象属性、对嵌套对象的深层监听会有性能问题

**解决方法：**

```js
// 1) 使用 $set 和 $delete
this.$set(this.obj, k, v)
this.$delete(this.obj, k)
// 2) 创建新对象后重新赋值
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

**$set 的原理？**

1. 如果目标是数组，使用 vue 实现的变异方法 splice 实现响应式
2. 如果目标是对象，如果属性存在，即为响应式，直接赋值
3. 如果 target 本身就不是响应式数据，直接赋值
4. 如果属性不是响应式，则调用 defineReactive 方法进行响应式处理

#### Proxy 实现响应式

Vue3 使用 Proxy 直接监听目标对象，拦截对象中任意属性的变化，对于嵌套对象进行惰性监听 (*真正访问到深层属性时才会变成响应式*)

```js
// 传入对象应该是一个非 null 的 object
const isObject = obj => typeof obj === 'object' && obj !== null

// vue-next/packages/reactivity/src/baseHandlers.ts
const baseHandler = {
  get(target, key, receiver) {
    // 访问 target 的 key 属性，但是 this 是指向 receiver
    let res = Reflect.get(target, key, receiver)
    // 判断 res 是对象，递归处理它
    res = isObject(res) ? reactive(res) : res
    console.log(`获取${key.toString()}: ${res}`)
    return res
  },
  // 原对象、属性名、属性值、代理后的对象
  set(target, key, value, receiver) {
    const res = Reflect.set(target, key, value, receiver)
    console.log(`设置${key.toString()}: ${value}`)
    return res
  },
  deleteProperty(target, key) {
    const res = Reflect.deleteProperty(target, key)
    console.log(`删除${key.toString()}: ${res}`)
    return res
  }
}

// vue-next/packages/reactivity/src/reactive.ts
function reactive(obj) {
  // reactive() 只接受非 null 的 object
  if (!isObject(obj)) {
    return obj
  }

  const observed = new Proxy(obj, baseHandler)
  return observed
}

// 测试
const state = reactive({ a: '前端' })
// 获取
state.a
// 设置已存在属性
state.a = '后端'
// 设置不存在属性
state.b = '运维'
// 删除属性
delete state.b

// 测试2
const state2 = reactive({
  c: { d: '算法' }
})
// 获取
state2.c.d
// 设置已存在属性
state2.c.d = '产品'
// 设置不存在属性
state2.c.d = '设计'
// 删除属性
delete state2.c.d
```

**如何进行深度监听？**

判断当前 `Reflect.get` 的返回值是否为 `Object`，如果是则再通过 reactive 方法做代理， 这样就实现了深度观测。

**监测数组的时候如何防止触发多次 get/set ？**

- 判断 key 是否为当前被代理对象 target 自身属性（`target.hasOwnProperty(key)`）
- 判断旧值与新值是否相等
- 只有满足以上两个条件之一时，才有可能执行触发。

**为什么使用 Reflect？**

- 语义化，方法都一一对应
- 直接使用 `reciever[key]` 会导致死循环，因为 reciever 是代理对象，重复触发了 get 代理
- 解决 this 指向：在 Proxy 代理的情况下，目标对象内部的 `this` 关键字会指向 Proxy 代理

```js
//Proxy的问题
const obj = {
  a: 10,
  get double() {
    return this.a * 2;
  },
};

const proxyobj = new Proxy(obj, {
  get(target, propKey, receiver) {
    return target[propKey];
  },
});
let obj2 = {
  __proto__: proxyobj,
};
obj2.a = 20;
obj2.double; //期望值为40，实际是20，因为double的getter里的this绑定到了obj

//使用Reflect解决this绑定问题
const obj = {
  a: 10,
  get double() {
    return this.a * 2;
  },
};

const proxyobj = new Proxy(obj, {
  get(target, propKey, receiver) {
    //这里的receiver是obj2
    return Reflect.get(target, propKey, receiver);
  },
});
let obj2 = {
  __proto__: proxyobj,
};
obj2.a = 20;
obj2.double; //40, 通过Refelct的receiver，get double()里的this绑定到了obj2

```

**Proxy 的优点**

- Proxy 直接代理整个对象而非对象属性，这样只需做一层代理就可以监听同级结构下的所有属性变化。
- Proxy 可以监听数组的变化。
- 对于嵌套对象进行惰性监听，性能更好

#### 有了 reactive 为何还需要 ref ？

Proxy 的代理目标必须是引用类型，ref 的作用是给原始类型数据提供一个对象包装，使之可以被 Proxy 代理，这也是 ref 声明的响应式数据必须使用 value 访问的原因。

```js
// 封装一个 ref 函数
function ref(val) {
  // 在 ref 函数内部创建包裹对象
  const wrapper = {
    value: val,
  };
  // 将包裹对象变成响应式数据
  return reactive(wrapper);
}
```

#### 优先使用 ref 的原因？

- reactive 只能声明引用数据类型 (对象/数组)
- reactive 在一些情况下会失去响应（*重新赋值一个对象，将 reactive 对象的属性赋值给另一个变量*）

| `reactive`                          | `ref`                                                    |
| ----------------------------------- | -------------------------------------------------------- |
| ❌只支持对象和数组 (引用数据类型)                  | ✅支持基本数据类型 + 引用数据类型                                       |
| ✅在 `<script>` 和 `<template>` 中无差别使用 | ❌在 `<script>` 和 `<template>` 使用方式不同 (script 中要 `.value`) |
| ❌重新分配一个新对象会丢失响应性                    | ✅重新分配一个新对象**不会**失去响应                                     |
| 能直接访问属性                             | 需要使用 `.value` 访问属性                                       |
| ❌将对象传入函数时,失去响应                      | ✅传入函数时,不会失去响应                                            |
| ❌解构时会丢失响应性,需使用 toRefs               | ❌解构对象时会丢失响应性,需使用 toRefs                                  |

#### toRefs 原理

直接解构响应式数据就会导致响应式丢失（*使用拓展运算符后返回了一个普通对象*），需要使用 toRefs。toRefs 是一种用于破坏响应式对象并将其所有属性转换为 ref 的实用方法。

```js
const state = reactive({ name: 'jack', age: 28 })
const { age } = toRefs(state)
age.value++
```

解决办法是创建一个与响应式对象具有同名属性的对象，而且每个属性的值都是一个对象。该对象有一个**访问器属性** `value`，当读取 `value` 的值时，最终读取的是响应式数据 `obj` 下的同名属性值。

```js
// obj 是响应式数据
const obj = reactive({ name: "jack", age: 28 });

// newObj 对象下具有与 obj 对象同名的属性，并且每个属性值都是一个对象，
// 该对象具有一个访问器属性 value，当读取 value 的值时，其实读取的是 obj 对象下相应的属性值
const newObj = {
  name: {
    get value() {
      return obj.name;
    },
  },
  age: {
    get value() {
      return obj.age;
    },
  },
};
```

#### 在模版中使用 ref 为什么不用加 value？

如果读取的属性是一个 ref，则直接将该 ref 对应的 value 属性值返回。

在编写 Vue.js 组件时，组件中的 `setup` 函数所返回的对象会传递给 `proxyRefs` 函数进行处理，它使用 Proxy 为对象创建一个代理对象，设置对象的 `getter`，通过不可枚举属性 `__v_isRef` 判断。

```js
function proxyRefs(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      const value = Reflect.get(target, key, receiver);
      // 自动脱 ref 实现：如果读取的值是 ref，则返回它的 value 属性值
      return value.__v_isRef ? value.value : value;
    },
  });
}
// 调用 proxyRefs 函数创建代理
const newObj = proxyRefs({ ...toRefs(obj) })
```

### computed 和 watch 实现原理？

**computed**

1. 初始化的过程中, 为每一个声明的 `computed` 创建 `Watcher`, 将声明时传入的函数 (或者对象声明的 `get`) 传递给创建的 `Watcher` 用于被访问时执行,利用 `defineProperty` 将声明的 `computed` 代理到 `vm` 实例上, 从而跟 `data` 一样可以通过 `this` 来访问, **同时用一个函数包装 `computed` 的 `getter`(实现缓存的关键)** , 当 `computed` 被访问时将执行该函数, 判断是否使用缓存值
2. 初始化结束之后会执行 `vm.$mount`, 对视图进行渲染, 渲染过程中会执行 `vm._render` 生成 `vnode` 由于解析到 `{{computed}}` 会触发之前劫持的 `getter`, 从而执行声明 `computed` 时的函数
3. 执行声明时传入的函数时, 由于初始化 `dirty=true`, 因此会去获取最新值, 此时会触发其所引用的 `data` 中数据的 `getter`, 从而触发响应式系统的依赖收集.**由于此时的 `Dep.target` 为该 `computerWatcher`**, 因此会收集该 `computerWatcher` 为依赖项
4. 当 `computed` 依赖的数据被更新时, 会进行消息分发,执行 `watcher.update()`, 若 `watcher` 为 `computedWatcher` 则将 `dirty` 标记为 `true`, 当前订阅的 `computed` 被访问时, 触发之前被函数包装的 `getter`, 函数内部识别到 `dirty===true` 则获取最新值, 获取完之后接着将 `dirty` 置为 `false`. **由于被依赖的数据订阅者中还有用于视图更新的 `renderWatcher`, 因此会接着对视图更新从而渲染最新数据, 这也说明 `computedWatcher` 要在 `renderWatcher` 之前去更新**

注意：计算属性与普通的 data 中的属性不同，计算属性并没有对应的 Dep 实例，也没有对应的依赖收集过程。

**watch**

1. 在初始化阶段，会为每个 `watch` 创建一个 `User Watcher`，如果 `watch` 的 `immediate` 为 `true`，会马上执行一次回调；创建 `User Watcher` 过程中会获取一次被监听属性的值，从而触发被监听属性的 `getter` 方法，将 `User Watcher` 添加到被监听属性的 `Dep` 实例中。
2. 当被监听属性发生改变时，通知 `User Watcher` 更新，如果 `watch` 的 `sync` 为 `true`，会马上执行 `watch` 的回调；否则会将 `User Watcher` 的 `update` 方法通过 `nextTick` 放到缓存队列中，在下一个的事件循环中，会重新获取被监听属性的属性值，并判断新旧值是否相等、是否设置了 `deep` 为 `true`、被监听属性是否是对象类型，如果成立就执行回调。

#### 为什么一个 computed 可以依赖另一个 computed？

假设 A，B 为两个计算属性，C 为 data 中的一个响应式属性，A 依赖 B，B 依赖 C

计算属性在触发依赖的 `getter` 前会将自己压入 `targetStack` 栈，`Dep.target` 赋值为自己，收集完成后出栈，并将 `Dep.target` 赋值为栈中的最后一个 `watcher` 。此时会判断 `Dep.target` 是否有值，有值的话就触发自己所有依赖的收集方法去收集 `Dep.target` 。

此处，B 的 `getter` 被触发，在依赖收集完依赖出栈后，发现 `Dep.target` 还有值，于是触发 B 所有依赖的收集方法，将 A（即 `Dep.target`） 添加到它们的 `dep.subs` 中

### Vue 的渲染过程？

1. **解析模板**：将模板解析为抽象语法树（AST），用于后续的编译和渲染过程。
2. **编译模板**：将抽象语法树（AST）转换为渲染函数。渲染函数是一个 JavaScript 函数，用于描述组件的渲染逻辑。编译过程还会对模板中的指令、表达式等进行静态分析和优化。
3. **创建虚拟 DOM**：在组件实例化时，Vue 会根据渲染函数创建一个虚拟 DOM（Virtual DOM）树。虚拟 DOM 是一个轻量级的 JavaScript 对象树，它描述了组件的当前状态和结构。
4. **更新虚拟 DOM**：当组件的状态发生变化时，Vue 会触发更新过程。在更新过程中，Vue 会比较前后两个虚拟 DOM 树的差异，找出需要更新的部分。
5. **生成补丁**：根据虚拟 DOM 树的差异，Vue 会生成一组补丁（Patch），用于描述如何将前后两个虚拟 DOM 树同步。补丁包含了新增、删除和修改节点等操作。
6. **应用补丁**：在应用补丁阶段，Vue 会将生成的补丁应用到虚拟 DOM 树上，通过更新虚拟 DOM 树来反映组件状态的变化。
7. **渲染到真实 DOM**：在完成虚拟 DOM 的更新后，Vue 会将最终的虚拟 DOM 树渲染到真实的 DOM 上。这个过程会将变化的部分更新到真实 DOM 上，以保持页面的同步。
8. **响应用户交互**：一旦组件渲染到真实 DOM 上，Vue 会监听用户的交互事件，如点击、输入等。当用户触发事件时，Vue 会根据事件处理函数的逻辑进行相应的响应。
9. **组件销毁**：当组件不再需要渲染或被销毁时，Vue 会执行组件的销毁过程。在销毁过程中，Vue 会清理组件的相关资源、解绑事件监听器等，以确保内存的释放和性能的优化。

#### Vue 模板编译原理？

模板编译的目标是要把 template 模板转换成渲染函数，主要分成 3 个步骤，parse -> optimize -> generate 。

1. **parse(解析模板)**：首先会将模板解析为 AST（*抽象语法树*）。在这个过程中，模板会被分解成一系列的节点，包括普通元素节点、文本节点、注释节点等，同时也会解析出这些节点的标签名、属性、指令等信息。
2. **optimize(静态分析)**：静态分析是指分析模板中的所有节点，找出其中的静态节点和静态属性，并将其标记出来。（*静态节点是指节点的内容不会发生变化的节点，例如纯文本节点、含有静态属性的节点等，而静态属性则是指节点上的属性值不会改变的属性，例如 class 和 style 属性。*）标记静态节点和静态属性可以帮助 Vue 在后续的更新中跳过这些节点的比对和更新过程，从而提高应用的性能。
3. **generate(代码生成)**：将 AST 转换为可执行的渲染函数。

#### 什么是 AST 抽象语法树？

抽象语法树（Abstract Syntax Tree，AST）是源代码的结构化表示，它以树状的形式表示代码的语法结构。AST 是编译器和相关工具中常用的数据结构，用于分析、转换和生成代码。

AST 将源代码解析为一系列的节点，每个节点代表代码中的一个语法结构，例如函数、变量声明、表达式等。每个节点都包含与其对应的语法元素相关的信息，例如标识符、操作符、参数等。

AST 的构建过程通常包括以下步骤：

1. 词法分析（Lexical Analysis）：将源代码分解为一个个的词法单元（tokens），例如关键字、标识符、操作符等。
2. 语法分析（Syntax Analysis）：根据词法单元构建抽象语法树。在这个阶段，解析器会根据语法规则将词法单元组织成语法结构，并生成对应的 AST 节点。
3. 语义分析（Semantic Analysis）：对 AST 进行进一步的分析，检查语法的正确性、类型推断等。在这个阶段，可以进行符号表的构建、类型检查、作用域分析等操作。

AST 的应用非常广泛，包括但不限于以下方面：

1. 编译器：编译器使用 AST 进行代码的分析、优化和生成。通过对 AST 进行遍历和转换，可以进行代码优化、死代码消除、内联函数等操作。
2. 代码静态分析：静态分析工具使用 AST 分析代码的结构和潜在问题。例如，代码风格检查工具可以通过分析 AST 来检查代码是否符合指定的代码风格规范。
3. 代码转换和重构：通过对 AST 进行修改和重构，可以实现代码的转换和重构。例如，将 ES5 代码转换为 ES6 代码，或者进行自动化的代码重构操作。
4. 代码生成：将 AST 转换为目标代码。编译器将 AST 转换为中间代码或目标代码，用于执行或部署应用程序。

#### AST 和 VNode 的区别？

**编译流程** template -> AST -> render function -> 执行 render function -> VNode

AST 是在模版编译过程中，为了方便转换成渲染函数所存在的；
VNode 是运行时优化 DOM 操作的，提升 DOM 性能的。

### 什么是虚拟 DOM，有什么作用？

虚拟 DOM 本质上是 JavaScript 对象，这个对象就是更加轻量级的对 DOM 的描述和抽象。

**虚拟 DOM 的作用**：

- 频繁变动 DOM 会造成浏览器的回流（重排）或者重绘，因此我们需要这一层抽象，在 patch 过程中==尽可能地一次性将差异更新到 DOM 中==，这样就尽可能的减少了 DOM 的操作，提高了程序性能
- 数据驱动 DOM 的更新，==省略手动 DOM 操作可以大大提高开发效率==
- 虚拟 DOM 最初的目的是更好的==跨平台==，例如 Node.js 就没有 DOM，如果想实现 SSR(服务端渲染)，那么一个方式就是借助虚拟 DOM

###  Vue diff 算法原理？

**是什么？**

Diff 算法是一种对比算法。对比两者是旧虚拟 DOM 和新虚拟 DOM，对比出哪个虚拟节点更改了，找出这个虚拟节点，并只更新这个虚拟节点所对应的真实节点，而不用更新其它数据没发生变化的节点，实现精准地更新真实 DOM，进而提高效率。这是以一种**打补丁**的形式去修正已经存在的节点。

diff 算法深度优先，只在同层进行对比，时间复杂度 `O(n)

**Vue2 双端 diff 算法**

![[Pasted image 20240209104332.png]]

- 数据改变时，触发 `setter`，并通过 `Dep.notify` 去通知所有订阅者去调用 `patch方法`，给真实 DOM 打补丁，更新相应的视图。
- `patch方法` 使用 `sameVnode方法` 对比当前同层的虚拟节点==是否为同一种类型的标签==（*key 值是否一样 && 标签名是否一样 && 是否都为注释节点 && 是否都定义了 data && 当标签为 input 时 type 是否相同*）：
	- 否，则整个节点直接替换成新虚拟节点；
	- 是，则继续执行 `patchVnode方法` 进行深层比对
- `patchVnode方法`
	- 找到对应的 `真实DOM`，称为 `el`
	- 判断 `newVnode` 和 `oldVnode` ==是否指向同一个对象==，如果是，那么直接 `return`
	- 如果他们都有==文本节点==并且不相等，那么将 `el` 的文本节点设置为 `newVnode` 的文本节点。
	- 如果 `oldVnode` 有==子节点==而 `newVnode` 没有，则删除 `el` 的子节点
	- 如果 `oldVnode` 没有==子节点==而 `newVnode` 有，则将 `newVnode` 的子节点真实化之后添加到 `el`
	- 如果两者都有子节点，则执行 `updateChildren` 函数比较子节点
- `updateChildren方法`
	- 使用==首尾指针法==，新的子节点集合和旧的子节点集合，各有首尾两个指针
	- 新子节点集合的两个指针和旧子节点集合的两个指针两两比较，如果没有匹配到，再把所有旧子节点的 `key` 做一个映射到旧节点下标的 `key -> index` 表，然用新 `vnode` 的 `key` 去找出在旧节点中可以复用的位置。
	- 每次比较后，能匹配到就复用，匹配不到就创建一个新的，然后移动首尾指针进行下一次比较
	- 循环比较过程中首尾指针逐渐向中间收拢，最后根据谁先走完循环再删除或创建节点到真实 dom 上

**vue3 快速 diff 算法**

vue3 使用了 `快速diff算法`，核心方法是 `patchKeyedChildren`，首先是借鉴了纯文本 diff 算法中的预处理思路，处理新旧两个组子节点中相同的前置节点和后置节点。处理完后，如果剩余节点无法简单的通过挂载新节点或者卸载已经不存在的节点来完成更新，则需要根据节点的索引关系，构建出一个最长递增子序列。最长递增子序列所指向的节点即为不需要移动的节点。

核心步骤：

- 使用两个指针对比新旧子节点集合的前置节点，相同则复用，不同则停止
- 使用两个指针对比新旧子节点集合的后置节点，相同则复用，不同则停止
- 如果只有旧子节点集合有剩余，那么删除剩余节点
- 如果只有新子节点集合有剩余，那么新增剩余节点
- 如果两边都有剩余：
	- 遍历两边的剩余节点
	- 构建 source 数组用于存放新的一组子节点每个节点在老的一组中存在的原来位置 (索引)
	- 判断是否需要移动节点
	- 利用最长递增子序列来优化移动逻辑

#### 对 key 的理解

key 是给每一个 vnode 的唯一 id，也是 diff 的一种优化策略，可以根据 key，更准确，更快的找到对应的 vnode 节点

当我们在使用 `v-for` 时，需要给单元加上 `key`

- 如果不用 key，Vue 会采用**就地复用**原则：如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素
- 如果使用了 key，Vue 会根据 keys 的顺序记录 element，曾经拥有了 key 的 element 如果不再出现的话，会被直接 remove 或者 destroyed

用 `+new Date()` 生成的时间戳作为 `key`，手动强制触发重新渲染

- 当拥有新值的 render 作为 key 时，拥有了新 key 的 Comp 出现了，那么旧 key Comp 会被移除，新 key Comp 触发渲染

#### 为什么不能用 index 作为 key？

![[Pasted image 20240209110316.png]]

这种情况，最理想的情况是只插入一个新节点。

但是在进行子节点的 `diff算法` 过程中，会进行旧首节点和新首节点的 `sameNode` 对比，这一步命中了逻辑，因为现在 `新旧两次首部节点` 的 `key` 都是 `0` 了，同理，key 为 1 和 2 的也是命中了逻辑，导致 `相同key的节点` 会去进行 `patchVnode` 更新文本，而原本就有的 `c节点`，却因为之前没有 key 为 4 的节点，而被当做了新节点，所以很搞笑，使用 index 做 key，最后新增的居然是本来就已有的 c 节点。所以前三个都进行 `patchVnode` 更新文本，最后一个进行了 `新增`，那就解释了为什么所有 li 标签都更新了。

#### 虚拟 DOM 一定比操作真实 DOM 快吗？

不一定。这是一个性能 vs. 可维护性的取舍。框架的意义在于为你掩盖底层的 DOM 操作，让你用更声明式的方式来描述你的目的，从而让你的代码更容易维护。框架给你的保证是，你在不需要手动优化的情况下，我依然可以给你提供过得去的性能。

### 对 nextTick 的理解？

如果想要在修改数据后立刻得到更新后的 `DOM` 结构，在组件中可以使用 `this.$nextTick()`，传入一个回调函数。`$nextTick()` 会返回一个 `Promise` 对象，配合 `async/await` 使用语义更清晰。

**原理**

由于 VUE 的数据驱动视图更新是异步的，当数据发生变化，`Vue` 将开启一个异步更新队列，并缓冲在同一事件循环中发生的所有数据变更（*如果同一个 watcher 被多次触发，只会被推入到队列中一次*）然后统一进行更新。即修改数据的当下，视图不会立刻更新，而是等同一事件循环中的所有数据变化完成之后，再统一进行视图更新。在同一事件循环中的数据变化后，DOM 完成更新，立即执行 `nextTick(callback)` 内的回调。

1. Vue 用异步队列的方式来控制 DOM 更新和 nextTick 回调先后执行
2. 优先使用微任务，能确保队列在一次事件循环被执行完毕
3. 考虑兼容问题，vue 做了微任务向宏任务的降级方案

### keep-alive 的原理？

keep-alive 是 Vue 提供的一个内置组件，用于在组件之间缓存不活动的组件实例，从而提高组件的渲染性能。keep-alive 的使用场景主要是那些需要频繁切换的组件，比如 tab 切换、列表滚动等。

当一个组件被包裹在 keep-alive 组件中时，这个组件实例将会被缓存起来。当组件被切换出去时，它的状态会被保存下来，而不是被销毁。当组件被再次渲染时，它的状态将被恢复，从而避免了重新渲染和重新创建组件实例的开销。

**实现原理**

==LRU 缓存算法== “如果数据最近被访问过,那么将来被访问的几率也更高”。在组件内部维护一个缓存对象，它会自动缓存最近使用的组件实例，并根据缓存大小限制自动释放最不常用的组件实例。

1. 获取 `keep-alive` 包裹着的第一个子组件对象及其组件名
2. 根据设定的 include/exclude（如果有）进行条件匹配，决定是否缓存。不匹配，直接返回组件实例
3. 根据组件 name 和 tag 生成缓存 Key，并在缓存对象中查找是否已缓存过该组件实例。如果存在，直接取出缓存值并更新该 key 在 `this.keys` 中的位置 (*更新 key 的位置是实现 LRU 置换策略的关键*)
4. 在 `this.cache` 对象中存储该组件实例并保存 key 值，之后检查缓存的实例数量是否超过 max 的设置值，超过则根据 LRU 置换策略删除最近最久未使用的实例（即是下标为 0 的那个 key）
5. 最后组件实例的 keepAlive 属性设置为 true，这个在渲染和执行被包裹组件的钩子函数会用到

#### 缓存后如何获取数据？

在 `keep-alive` 缓存的组件被激活的时候，都会执行 `actived` 钩子

```js
activated(){
   this.getData() // 获取数据
},
```

每次组件渲染的时候，都会执行 `beforeRouteEnter`

```js
beforeRouteEnter(to, from, next){
    next(vm=>{
        console.log(vm)
        // 每次进入路由执行
        vm.getData()  // 获取数据
    })
},
```
