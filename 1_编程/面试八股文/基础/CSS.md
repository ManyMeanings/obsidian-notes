### 简述 CSS 的盒模型✨

一个盒子由四个部分组成：`content`、`padding`、`border`、`margin`

盒模型有两种：IE 盒模型和标准盒模型。标准盒模型宽高不包含 border 和 padding， IE 盒模型的宽高包含 border 和 padding。

通过 `box-sizing` 属性配置：
- `content-box`：默认属性。width 只包含 content
- `border-box`：width 包含 content、padding、border

###  display 的 block、inline 和 inline-block 的区别

- `block` 块级元素：
	- 可以设置宽高
	- 可以设置 margin 和 padding
	- 独占一行
	- 能包含任何标签
- `inline` 行内元素：
	- ==无法设置宽高==
	- ==只能设置水平方向的 margin 和 padding==
	- 不会换行
	- 只能包含文本和行内元素
- `inline-block`
	- 不会换行，其他和块级元素一致

### CSS specificity（权重）

选择器的优先级分为三个等级：
1. `id` 选择器，如 `#app`
2. `class`、`attribute` 与 `pseudo-classes` 选择器，如 `.header`、`[type="radio"]` 与 `:hover`
3. `type` 标签选择器和伪元素选择器，如 `h1`、`p` 和 `::before`

计算规则：
1. 每个等级的值为选择器出现次数相加，不可进位
2. 优先级比较从高到低，如果某一等级数值相同，则比较下一级
3. 如果两个选择器优先级相同，则最后出现的优先级高
4. 通配符选择器 `*`，组合选择器 `+ ~ >`，否定伪类选择器 `:not()` 对优先级无影响
5. 内联样式和 `!important`(最高) 具有更高的权重
6. CSS 样式表的冲突：相同选择器采取就近原则；外部样式表的 id 选择器 > 内嵌样式表的标签选择器。

### CSS 选择器有哪些？

- id 选择器（`#myid`）
- 类选择器（`.myclassname`）
- 标签选择器（`div`）
- 后代选择器（`h1 p`）
- 交集选择器（`p.myclassname`）
- 并集选择器（`h1, p`）
- 相邻后代（子）选择器（`ul>li`）选择父元素为 ul 的所有 li 的元素
- 兄弟选择器（`li~a`）选择前面有 li 元素的每个 a 元素
- 相邻兄弟选择器（`li+a`）选择紧接在 li 之后的所有 a 元素
- 属性选择器（`a[rel="external"]`）
- 伪类选择器（`a:hover`）
- 伪元素选择器（`::before`）
- 通配符选择器（`*`）

#### 伪类和伪元素的区别？

- 伪类的操作对象是文档树中已有的元素，用于当已有元素处于的某个状态时，为其添加对应的样式；
- 伪元素则创建了一个文档树外的元素。
- 伪类与伪元素的区别在于：**有没有创建一个文档树之外的元素**

### 说说 em/px/rem/vh/vw 区别?

- **px** 像素
- **em** 相对于自身的 `font-size` 值，如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸（`1em = 16px`）
- **rem** 相对的只是 HTML 根元素 `font-size` 的值（*通常在根元素 `html` 中设置 `font-size: 62.5%`，使得 `1rem =10px`*）
- **vh、vw** 将窗口的宽高分成 100 等分（*在桌面端是可视区域，在移动端是布局视口*）

### 说说设备像素、css 像素、设备独立像素、dpr、ppi 之间的区别？

- **设备像素（物理像素）** 设备能控制显示的最小物理单位，不会改变
- **设备独立像素** 通过程序控制的虚拟像素，一个设备独立像素里可能包含 1 个或者多个物理像素点，包含的越多则屏幕看起来越清晰
- **CSS 像素** 无缩放情况下，1 个 CSS 像素等于 1 个设备独立像素
- **dpr** 设备像素比，`dpr=设备像素/设备独立像素`
- **ppi** 每英寸像素，表示像素密度，数值越高，说明屏幕能以更高密度显示图像

### 什么是层叠上下文？✨

[深入理解CSS中的层叠上下文和层叠顺序 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)

![[Pasted image 20240115150836.png]]

要比较两个元素的 z 轴位置：

- 如果处于同一个层叠上下文，那么比较他们的层叠顺序
- 如果处于同一个层叠上下文，且层叠顺序相同，那么后来居上
- 如果处于不同的层叠上下文，那么比较他们所在层叠上下文的层叠顺序

元素发生层叠时有着特定的垂直显示顺序：

1. **背景和边框**: 建立当前层叠上下文元素的背景和边框。
2. **负的 z-index**: 当前层叠上下文中，z-index 属性值为负的元素。
3. **块级盒**: 文档流内非行内级非定位后代元素。
4. **浮动盒**: 非定位浮动元素。
5. **行内盒**: 文档流内行内级非定位后代元素。
6. **z-index:0 或 z-index:auto**: 层叠级数为 0 或 auto 的定位元素（auto 是默认值）。
7. **正 z-index**: 定位元素。 层叠上下文中的最高等级。

#### 可以建立层叠上下文的元素

- 根元素 `html` ；
- 绝对定位 `absolute` 或相对定位 `relative` 且 `z-index` 值不为 `auto` ；
- 一个 `flex` 项目，且 `z-index` 值不为 `auto` ，也就是父元素 `display: flex|inline-flex` ；
- 元素的 `opacity` 属性值小于 `1` ；
- 元素的 `transform` 属性值不为 `none` ；
- 在 `mobile WebKit` 和 `Chrome 22+` 内核的浏览器中，`position: fixed` 时总是会创建一个新的层叠上下文, 即使 `z-index` 的值是 `auto` ；

#### 对 z-index 的理解

要判断元素在 `z轴` 上的**堆叠顺序**，并不仅仅是直接比较两个元素的 `z-index` 值的大小，同时，这个堆叠顺序还由元素的**层叠上下文**和**层叠等级**共同决定。

![[Pasted image 20240115152316.png]]

要让 z-index 值生效：

- 设置元素的 position 值为 relative/absolute/fixed
- 当父元素设置 display: flex | inline-flex 时，子元素设置 z-index
- 当 opacity 不为 1 或 0 时
- 当 transform 不为 none 时

#### z-index: 0 和 z-index: auto 的区别？

- `z-index: 0` 与 `z-index: auto` 在同一层级内没有高低之分，文档流中后出现的会覆盖先出现的（*后来居上原则*）
- `z-index:0` 产生了层叠上下文， `z-index: auto` 不会产生层叠上下文。

### 什么是 BFC（块级格式化上下文）？✨

`BFC`（Block Formatting Context），即块级格式化上下文，它是页面中的一块渲染区域，并且有一套属于自己的渲染规则。

特点：
- 内部的盒子（块级）会在垂直方向上一个接一个的放置
- 对于同一个 BFC 的两个相邻的盒子的 margin 会发生重叠，与方向无关。
- ==在一个 BFC 中，每一个盒子的左外边距应该和包含块的左边缘接触==（*关于左边缘，绝对定位是相对于最近非 static 祖先元素的 padding-box，其他是相对于父元素的 content-box*），即使存在浮动盒子也应该如此（*除非子盒子又形成了一个新的 bfc*）
- BFC 的区域不会与 float 的元素区域重叠
- 计算 BFC 的高度时，浮动子元素也参与计算
- BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然。

#### 如何创建 BFC？

- 根元素或其它包含它的元素
- 具有 overflow 且值不是 visible 的块元素
- 浮动元素 (元素的 float 不是 none)
- 绝对定位元素 (元素具有 position 为 absolute 或 fixed)
- 内联块 (元素具有 display: inline-block)
- 弹性盒（flex 或 inline-flex）
- 网格元素（display 为 grid 或 inline-grid 元素的直接子元素）
- 表格单元格 (元素具有 display: table-cell，HTML 表格单元格默认属性)
- 表格标题 (元素具有 display: table-caption, HTML 表格标题默认属性)

#### BFC 的应用？

- margin 重叠问题
- 高度塌陷（清除浮动）：对子元素设置浮动后，父元素高度变为 0，此时可以给父元素设置 `overflow: hidden`
- 阻止元素被浮动元素覆盖（自适应两栏布局）

#### margin 重叠发生的情况？

- 相邻兄弟元素
- 父元素和子元素的 margin-top
- 高度为 auto 的父元素和子元素的 margin-bottom
- 没有内容的元素，自身的 margin-top 和 margin-bottom

[css - 关于BFC的特征，为什么说内部的box会在垂直方向一个接一个放置？ - SegmentFault 思否](https://segmentfault.com/q/1010000008875016)

### 什么是包含块？✨

包含块是用来确定和影响元素的尺寸和位置属性的矩形区域（*注意，包含块不是一个完整的盒子，它是一个区域*）

一个元素的包含块完全受其 `position` 属性值的影响：

1. `static` 或 `relative`：最近的块级（`display` 属性值为 `block`，`inline-block` 或 `list-item`）祖先元素的 `content-box` 区域；或者最近的**BFC**祖先元素，比如：`table` 容器，`flex` 容器，`grid` 容器或块级容器。
2. `absolute`：最近的非 `static`（fixed, absolute, relative, or sticky）祖先元素的 `padding-box` 区域。
3. `fixed`：可视窗口 `viewport` 本身（属于 `continuous media` 类型时）或页面区域 `page area`（属于 `paged media` 类型时），即**初始包含块**；
4. 当属性值为 `fixed` 或 `absolute` 时，其包含块还有可能是最近的含有 `transform` 或 `perspective` 值不为 `none` 的祖先元素的 `padding-box` 区域。

### position 定位元素理解？✨

**绝对定位的特点**

- 一旦给元素加上 `absolute` 或 `float` 就相当于给元素加上了 `display:block`
- `absolute` 元素覆盖正常文档流内元素（不用设 z-index，自然覆盖）
- 可以减少重绘和回流的开销（如 `absolute+ top:-9999em`，或 `absolute + visibility:hidden`，将动画效果放到 `absolute` 元素中）
- 浮动、绝对定位和固定定位会脱离文档流，相对定位不会脱离文档流
- 绝对定位相对于该元素最近的非 static 祖先元素，如果没有一个祖先元素设置定位，那么参照物是 body 层
- 定位元素的 margin 还是能起作用的（BFC 的规则）

**绝对定位相对于包含块的起始位置**

- 如果祖先元素是块级元素，包含块则设置为该元素的内边距边界。
- 如果祖先元素是行内元素，包含块则设置为该祖先元素的内容边界。

#### background 会影响 border 吗？

会。

### 浮动元素的特点？✨

float 被设计出来的初衷是用于**文字环绕效果**，即一个图片一段文字，图片 `float:left` 之后，文字会环绕图片。但是，后来大家发现结合 `float + div` 可以实现之前通过 `table` 实现的网页布局，因此就被“误用”于网页布局了。

- **破坏性** 浮动元素脱离文档流。
- 浮动元素互相贴靠（可用于处理空白间隔）。
- **文字环绕效果** 浮动元素有“字围”效果。
- **包裹性** 一个浮动的元素，如果没有设置 width，那么将自动收缩为内容的宽度。

#### 如何清除浮动？

1. 定义一个 clearfix 类，使用伪元素 `::after` 插入一个空的块级元素，设置不可见且高度为 0，并为其设置样式 `clear: both;`。使用时只需要将 clearfix 类应用在浮动元素的父元素上。

```css
.clearfix::after {
	content: ' ';
	display: block;
	clear: both;
	height: 0;
	visibility: hidden;
}
```

1. 将浮动元素的父元素设置为 BFC

### css 中有哪些方式可以隐藏页面元素？

- `display:none` 元素不可见，不占据空间，无法响应点击事件
- `visibility:hidden` ==元素不可见，占据页面空间，无法响应点击事件==
- `opacity:0` 改变元素透明度，元素不可见，占据页面空间，可以响应点击事件
- `设置 height、width 模型属性为 0` 元素不可见，不占据页面空间，无法响应点击事件
- `position:absolute 将元素移出可视区域` 元素不可见，不影响页面布局

|  | **display: none** | **visibility: hidden** | **opacity: 0** |
| ---- | ---- | ---- | ---- |
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
