### 按比例在元素背后画一个实心圆

```css
.img-box::after {
	content: '';
	display: block;
	background-color: red;
	border-radius: 50%;
	z-index: -1;
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	width: 60%;
	/* height: 60%; 不起作用: 父元素高度不确定*/

	/* CSS 百分比 padding 都是相对宽度计算的 */
	/* 父元素宽度的 60% */
	padding-bottom: 60%;
}
```

### 添加内部 border

```css
.border-inside {
	/* border: 3px solid #000; 会改变元素宽高 */
	box-shadow: inset 0 0 0 3px #000;
}
```

### 调暗背景图片

```css
.img-box {
	background-image: linear-gradient(rgba(0, 0, 0, 0.4), rgba(0, 0, 0, 0.4)), url(img/bg.jpg);
}
```

### 基于元素属性选择

```css
.my-input[name="name"] {
	
}
```