---
- ""
---
- | ---- | ---- |
| 页面中 | 不存在 | 存在 | 存在 |
| 重排 | 会 | 不会 | 不会 |
| 重绘 | 会 | 会 | 不一定 |
| 自身绑定事件 | 不触发 | 不触发 | 可触发 |
| transition | 不支持 | 支持 | 支持 |
| 子元素可复原 | 不能 | 能 | 不能 |
| 被遮挡的元素可触发事件 | 能 | 能 | 不能 |

### 元素居中布局的方法有哪些？✨

#### 内联元素居中布局

水平居中

- 行内元素可设置：`text-align: center`
- flex 布局设置父元素：`display: flex; justify-content: center`

垂直居中

- 单行文本父元素确认高度：`height === line-height`
- 多行文本父元素确认高度：`display: table-cell; vertical-align: middle`

#### 块级元素居中布局

水平居中

- 定宽: `margin: 0 auto`
- 绝对定位 `left:50%; margin: 负自身一半`

垂直居中

- `position: absolute` 设置 `left: 50%;top: 50%;transform: translate(-50%, -50%)`
- flex 布局设置 `justify-content: center; align-items: center`
- grid 布局设置 `justify-content: center; align-items: center

### 两栏布局和三栏布局实现？✨

#### 两栏布局

- float 左浮动左边栏，主体设置 margin-left，父元素设置 BFC
- 父元素设置 flex，左边栏设置宽度，主体设置 flex:1
- 父元素设置 grid，grid-template-columns: 200px 1fr

#### 三栏布局

- 两边使用 float，中间使用 margin
- 两边使用 absolute，中间使用 margin
- 父元素设置 flex，中间设置 flex:1，两边设置宽度
- 父元素设置 grid，grid-template-columns: 200px 1fr 200px

### padding 的百分比值？

当 `padding-top`、`padding-bottom`、`margin-top`、`margin-bottom` 属性设置为百分比时，参考对象都是父级元素的宽度

###  flexbox（弹性盒子布局）的常用属性？✨

容器元素：

- `display: flex`
- `flex-direction: row | row-reverse | column | column-reverse`
	- **主轴方向**：从左到右、从右到左、从上到下、从下到上
- `flex-wrap: nowrap | wrap | wrap-reverse`
	- **换行方式**：不换行、换行、换行后第一行在下方
- `justify-content: flex-start | flex-end | center | space-between | space-around`
	- **项目在主轴上的对齐方式**：左对齐、右对齐、居中对齐、两端对齐、每个项目两侧的间隔相等（*项目之间的间隔比项目与边框的间隔大一倍*）
- `align-items: flex-start | flex-end | center | baseline | stretch`
	- **项目在交叉轴上的对齐方式**：起点对齐、终点对齐、中点对齐、文字的基线对齐、如果项目未设置高度或设为 auto，将占满整个容器的高度（*默认值*）
- `align-content: flex-start | flex-end | center | space-between | space-around | stretch`
	- **有多根轴钱时项目整体在交叉轴上的对齐方式**
- `gap: 5px 5px`
	- **行间距和项目间距**

项目元素：

- `order: <integer>` 排列顺序，数值越小，排列越靠前，默认为 0
- `flex-grow: <integer>` 存在剩余空间时项目的放大比例，默认为 0
- `flex-shrink: <integer>` 如果空间不足，项目的缩小比例，默认为 1
- `flex-basis: <length> | auto` 项目占据的主轴长度，默认为 auto，即项目的本来大小。
- `flex` 前三项的简写，默认值为 `0 1 auto`，后两个可选，有两个快捷值：`auto` (`1 1 auto`) 和 `none` (`0 0 auto`)。
- `align-self: auto | flex-start | flex-end | center | baseline | stretch` 单个项目的对齐方式

### grid（网格布局）常用属性？✨

容器元素：

- `display: grid`
- `grid-template-columns` 定义列宽
- `grid-template-rows` 定义行高
- `grid-template-areas` 定义区域
- `gap` 行间距和列间距
- `justify-items: start | end | center | stretch`
	- 设置单元格内容的水平位置（左中右）
- `align-items` 属性设置单元格内容的垂直位置（上中下）
- `place-items` 属性是 `align-items` 属性和 `justify-items` 属性的合并简写形式。
- `justify-content` 属性是整个内容区域在容器里面的水平位置（左中右）
- `align-content` 属性是整个内容区域的垂直位置（上中下）
- `place-content` 属性是 `align-content` 属性和 `justify-content` 属性的合并简写形式

项目元素：

- `grid-column: <start-line> / <end-line>`
	- 是 `grid-column-start` 和 `grid-column-end` 的合并简写形式
- `grid-row: <start-line> / <end-line>`
	- 是 `grid-row-start` 属性和 `grid-row-end` 的合并简写形式
- `justify-self` 属性设置单元格内容的水平位置（左中右）
- `align-self` 属性设置单元格内容的垂直位置（上中下）
- `place-self` 属性是 `align-self` 属性和 `justify-self` 属性的合并简写形式

### 怎么做响应式设计？

- 通过媒体查询检测不同的设备屏幕尺寸做处理（*将媒体查询放到 css 文件末尾确保高优先级*）
- 移动端需要设置 viewport：`<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no”>`
- 布局使用 flex 或者 grid
- 使用相对单位 rem： `html` 中设置 `font-size: 62.5%`，使得 `1rem=10px`

```css
/* 最大宽度为 1200px 的设备 */
@media screen and (max-width: 1024px) {
  .text {
    font-size: 16px;
  }
}
/* 最大宽度为 480px 的设备 */
@media screen and (max-width: 480px) {
  .text {
    font-size: 16px;
  }
}
```

### 什么是 CSS 工程化？

1. 预编译器：less、sass、stylus 等。
2. CSS 模块化方案：常见的像 Vue 里的 scoped 方案以及 React 里的 CSS-in-JS 方案。
3. CSS 自动化工具：比如 PostCSS，就提供很多有用的插件，让我们可以做一些 CSS 的自动化工作，比如自动添加前缀、自动格式化代码、移动端适配。
4. 代码检测工具：如 CSSLint、Stylelint 等，可以自动化检测 CSS 代码的错误和潜在问题，提高代码质量和稳定性。
5. 样式指南：主要是指 CSS 的规范，包括设计原则、排版规则、颜色使用、图标规范、命名规范、代码格式化等各方面的规范。

### CSS 如何避免命名样式冲突？

- BEM 式：将 CSS 类名分为==块、元素和修饰符==三个部分。
	-  `block__list-item block__list-item--highlighted`
- CSS Scoped
	- `scoped css` 会对当前组件 (scope) 下所有元素生成唯一的属性或类名，对所有 CSS 规则将携带唯一属性实现作用域的命名保护
- CSS Module
	- `module css` 会对类名进行 hash 化

### CSS 变量是什么？使用场景？

```css
:root {
  --color: red;
}
h1 {
  var(--color)
}
```

CSS 变量带有前缀 `--` 的属性名，比如 `--example--name`，表示的是带有值的自定义属性，可以通过 `var()` 函数在全文档范围内复用。

减少样式的重复定义，方便在 js 中使用，可以实现主题色修改

```js
// 设置变量
document.getElementById("box").style.setProperty("--color", "pink");
// 读取变量
doucment.getElementById("box").style.getPropertyValue("--color").trim();
// 删除变量
document.getElementById("box").style.removeProperty("--color");
```

### width: auto 和 width: 100% 的区别?

`width: 100%` 会使元素 box 的宽度等于父元素的 contentbox 的宽度，并且随着父级的宽度自动变化，增加子元素的 `padding` 和 `margin` 之后，它的 contentbox 还是不变的。

`width:auto` 会使元素撑满整个父元素，`margin`、`border`、`padding`、`content` 区域会自动分配水平空间。

一般 `width:auto` 使用的多，因为这样灵活，而 `width:100%` 使用比较少，因为在增加 `padding` 或者 `margin` 的时候，容易使其突破父级框，破环布局。

### 页面导入样式时，使用 link 和 @import 有什么区别？

- **从属关系**：link 是 HTML 的标签，不仅可以加载 CSS 文件，还可以定义 rel 连接属性，引入网站图标等；@import 是 CSS 提供的语法规则，只能导入样式表。
- **加载顺序**：link 引入的 CSS 同时加载，@import 引入的 CSS 在页面加载完毕后加载。
- **兼容性**： link 无兼容性问题，@import 只适用于 IE5+ 的浏览器。
- **DOM**：可以用 JS 插入 link 标签改变样式，无法使用 @import 的方式。

### 移动端 1px 边框问题怎么解决？

`CSS` 像素为 `1px` 宽的直线，对应的物理像素是不同的，可能是 `2px` 或者 `3px`,而设计师想要的 `1px` 宽的直线,其实就是 `1` 物理像素宽

**伪元素先放大后缩小**

在目标元素的后面追加一个 `::after` 伪元素，让这个元素布局为 absolute 之后、整个伸展开铺在目标元素上，然后把它的宽和高都设置为目标元素的两倍，border 值设为 1px。接着借助 CSS 动画特效中的放缩能力，把整个伪元素缩小为原来的 50%。此时，伪元素的宽高刚好可以和原有的目标元素对齐，而 border 也缩小为了 1px 的二分之一，间接地实现了 0.5px 的效果。

```css
.border-1px:before{
    content: '';
    position: absolute;
    top: 0;
    height: 1px;
    width: 100%;
    background-color: #999;
    transform-origin: 50% 0%; /*变换原点设置在元素的水平中心线上，垂直顶部边缘*/
}
@media only screen and (-webkit-min-device-pixel-ratio:2){
    .border-1px:before{
        transform: scaleY(0.5);
    }
}
@media only screen and (-webkit-min-device-pixel-ratio:3){
    .border-1px:before{
        transform: scaleY(0.33);
    }
}
```

**viewport + rem 实现**

不适合老项目改造

```js
const scale = 1 / window.devicePixelRatio;
const viewport = document.querySelector('meta[name="viewport"]');
if (!viewport) {
    viewport = document.createElement('meta');
    viewport.setAttribute('name', 'viewport');
    window.document.head.appendChild(viewport);
}

viewport.setAttribute('content', 'width=device-width,user-scalable=no,initial-scale=' + scale + ',maximum-scale=' + scale + ',minimum-scale=' + scale);

// 设置根字体大小
var docEl = document.documentElement; 
var fontsize = 10 * (docEl.clientWidth / 320) + 'px'; 
docEl.style.fontSize = fontsize;

// 在CSS中用rem单位就行了
```

### 对 CSS 动画有了解吗，CSS 实现动画有哪几种方式？

CSS 动画主要包括 transition 动画、和 animation 动画。

1. transition 动画：主要用来实现过渡效果，包括颜色、透明度、大小、位置，从一个状态向另一个状态过渡。配合 transform 可以实现元素的变形和转换，如旋转、缩放、平移和倾斜等

```css
.element {
  transition: property duration timing-function delay;
}

.element {
  transform: rotate(45deg);
  transition: transform duration timing-function delay;
}
```

2. animation 动画：使用 `@keyframes` 规则定义一组关键帧，描述元素在动画过程中的样式变化。然后通过 animation 属性将关键帧应用到元素上：

```css
@keyframes animationName {
  0% { /* 初始样式 */ }
  50% { /* 中间样式 */ }
  100% { /* 结束样式 */ }
}

.element {
  animation: animationName duration timing-function delay iteration-count direction;
}
```

### 怎么实现换肤？

使用 CSS 变量，可以定义一个 `theme-color` 变量来存储主题色：

```css
:root {
  --theme-color: #007bff; /* 定义主题色 */
}
```

然后在需要使用主题色的地方使用 var() 函数来引用这个变量：

```css
.button {
  background-color: var(--theme-color); /* 使用主题色 */
}
```

再通过 JS 选中根元素，使用 `style.setProperty` 动态更改这个 CSS 变量的值，从而实现换肤的效果。

```js
document.documentElement.style.setProperty("--theme-color", "green");
```

**Vue 中 css 绑定响应式数据**

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
