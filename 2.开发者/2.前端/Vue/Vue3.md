### 计算属性 vs 方法

- 计算属性适合做一些简单的操作
- **计算属性会缓存数据**
- 计算属性可以直接在 HTML 中，像 Data 中的属性一样使用 

- **方法适合做复杂的操作**
- 方法通常作为**事件监听**或者**公共业务逻辑**
- 方法可以当作普通的 js 函数使用

### 计算属性 vs 监听器

| computed                                                   | watch                         |
| ---------------------------------------------------------- | ----------------------------- |
| **简单的业务逻辑计算**                                     | **耗时的操作和远程 API 加载** |
| 可以直接在 HTML 模版中使用                                 | 不能在 HTML 模版中使用        |
| 响应 data 数据变化                                         | 响应数据变化                  |
| 有返回值/getter                                            | 没有返回值                    |
| 也可以使用 setter 修改 data 中的数据（产生副作用，不推荐） | 可以修改 data 中的数据        |

### 生命周期

- **beforeCreate** 会在实例初始化完成、props 解析之后、`data()` 和 `computed` 等选项处理之前立即调用。
- **created** 在组件实例处理完所有与状态相关的选项后调用，此时挂载阶段还未开始。
- **beforeMount** 在组件被挂载之前调用，此时页面呈现的是未经Vue编译的DOM结构
- **mounted** 在组件被挂载之后调用。
- **beforeUpdate** 在组件即将因为一个响应式状态变更而更新其 DOM 树之前调用。
- **updated** 在组件即将因为一个响应式状态变更而更新其 DOM 树之前调用（不能在这时更改组件状态，可能会导致无限循环）。
- **beforeUnmount** 在一个组件实例被卸载之前调用，此时组件实例还保有全部的功能。
- **unmounted** 在一个组件实例被卸载之后调用。

### 子组件在插槽中向父组件暴露内部属性

```html
<!--Parent-->
<Child v-slot:child="props">{{ props.text }}</Child>

<!--Child-->
<slot name="child" :text="showMsg"/>
```

### CSS 中绑定响应数据

实际的值会被编译成哈希化的 CSS 自定义属性，因此 CSS 本身仍然是静态的。自定义属性会通过内联样式的方式应用到组件的根元素上，并且在源值变更的时候响应式地更新。

```css
/*script*/
data() {
	return {
		color: 'red'
	}
}

/*style */
p {
	color: v-bind(color)
}
```

### 自定义 `v-model`

```html
<!--Parent-->
<Child v-model="props" />

<!--Child-->
<!--template-->
<input
	type="text"
	:value="modelValue"
	@input="$emit('update:modelValue', $event.target.value)" />

<!--script-->
props: ['modelValue'],
emits: ['update:modelValue'],
```

### 自定义指令

```js
app.directive('fsize', (el, binding) => {
	// 等于同时设定 mounted 和 updated
	el.style.fontSize = binding.value + (binding.arg || 'px');
});

// template
// <div v-fsize:em="20">test</div>
```

### `Teleport`组件

```html
<!--将消息组件直接挂载到body元素下-->
<Teleport to="body">
	<Message />
</Teleport>
```

### 异步导入组件

```js
import { defineAsyncComponent } from 'vue';

// 该组件会在第一次挂载的时候加载代码
const Component = defineAsyncComponent( () => {
	return import('./components/Child.vue');
})
```

### 全局错误处理

```js
app.config.errorHandler = (err, vm, info) => {
	err // 错误信息
	vm // 实例
	info // 位置
}
```

### 组件错误处理（错误边界）

```js
// 生命周期钩子
// 捕获所有从子组件传播上来的错误
errorCaptured(err, instance, info) {
	return false; // 停止错误向上传播
}
```

## 组合式 API

### 响应性变量

```js
import { ref, reactive, computed } from "vue";

export default {
  setup() {
    const commonVariable = "123"; // 普通变量

    // ---用 ref 定义---
    const num = ref(0); // 数字
    const string = ref("string"); // 字符串
    const arr = ref([1, 2, 3]); // 数组
    // 对象的嵌套属性具会变成响应性
    const obj = ref({ a: 1, b: 2 }); // 对象

    // ---用 reactive 定义响应式对象---
    // 支持转入对象类型
    const option = reactive({
      num: 1,
      string: "234",
    });

    // ---定义计算属性---
    const stringLen = computed(() => {
      return string.value.length;
    });

    // ---访问---
    console.log(num.value, string.value, arr.value, obj.value);

    // ** value 值不等于原始数据 **
    // string.value === 'string'; // false

	// reactive 定义的变量无需通过 value 访问
    console.log(option.num, option.string); 

	// 计算属性也要通过 value 访问
    console.log(stringLen.value);

    // ---导出---
    // 导出的数据在 template 中可以直接访问
    return { commonVariable, num, string, arr, obj, option, stringLen }; 
  },
};
```

### 侦听器

```js
import { watch, watchEffect } from "vue";

export default {
  setup() {
    const x = ref(0);
    const y = ref(0);

	// ---侦听数据源类型---
    // 单个 ref
    watch(x, (newX) => {
      console.log(`x is ${newX}`);
    });
    // getter 函数
    watch(
      () => x.value + y.value,
      (sum) => {
        console.log(`sum of x + y is: ${sum}`);
      }
    );
    // 多个来源组成的数组
    watch([x, () => y.value], ([newX, newY]) => {
      console.log(`x is ${newX} and y is ${newY}`);
    });
	
	// ---深度侦听---
	// 直接给 `watch()` 传入一个响应式对象，会隐式地创建一个深层侦听器
	const obj = reactive({ count: 0 });
	watch(obj, (newValue, oldValue) => {
	  // 在嵌套的属性变更时触发
	  // 注意：`newValue` 此处和 `oldValue` 是相等的
	  // 因为它们是同一个对象！
	});
	// 显式指定深度侦听
	watch(
	  () => obj,
	  (newValue, oldValue) => {
	    // 注意：`newValue` 此处和 `oldValue` 是相等的
	    // *除非* obj 被整个替换了
	  },
	  { deep: true }
	);
	// 获取 oldValue 并且深度侦听
	watch(
	  () => JSON.parse(JSON.stringify(obj)),
	  (newValue, oldValue) => {
	    // 注意：`newValue` 此处和 `oldValue` 是相等的
	    // *除非* obj 被整个替换了
	  },
	  { deep: true }
	);
	
	// ---即时回调的侦听器---
	watch(
	  source,
	  (newValue, oldValue) => {
		// 立即执行，且当 `source` 改变时再次执行
	  },
	  { immediate: true }
	)
	
	// ---自动跟踪回调的响应式依赖---
	// 回调会立即执行
	watchEffect(async () => {
	  const response = await fetch(
	    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
	  )
	  data.value = await response.json()
	})
  },
};

```

### 依赖注入

```js
// proent
const msg = ref({ text: 'msg from parent' });
// 使用 toRef 保持 text 的响应性
provide('msg', toRef(msg.value, 'text'));

// child
const msg = inject('msg');
```

### composable

- composable 是一个普通的 js 函数，命名通常以 use 开头；
- setup() 中的代码全部可以在 composable 中编写；
- composable 的逻辑越独立越好
- 能减少组件文件的代码，增强复用

### `<script setup>`

- 给 script 标签添加 setup 属性 
- 可以直接当作在 setup 函数中编写代码
- 不用手动返回变量，可以直接在模板中使用 
- import 导入的变量也可直接在模板使用
- 定义 props 使用 defineProps，定义事件使用 defineEmits，编译器宏，无需导入

### `Transition`

- 由 `v-if` 所触发的切换
- 由 `v-show` 所触发的切换
- 由特殊元素 `<component>` 切换的动态组件
- 改变特殊的 `key` 属性

![[Pasted image 20231227142814.png]]

```html
<button @click="show = !show">Toggle</button>
<Transition name="fade">
  <p v-if="show">hello</p>
</Transition>
```

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```
