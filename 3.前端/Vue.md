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
<Child v-slot:default="props">{{ props.name }}</Child>

<!--Child-->
<slot :name="name"/>
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