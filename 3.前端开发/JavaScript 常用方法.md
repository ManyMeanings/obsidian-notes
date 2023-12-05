### Object

```js
```

### Array

![[Pasted image 20231204111414.png]]

```js
const arr = ['a', 'b', 'c', 'd', 'e'];
const arr = new Array('a', 'b', 'c', 'd', 'e');
// 创建长度为 7 的空数组
const x = new Array(7);
// [begin, end) 位置全部填充为 1
x.fill(1, begin, end);
// Array.from
const y = Array.from({length: 5}, (_, i) => i + 1); // [1, 2, 3, 4, 5]

// 不改变原数组
arr.slice(2); // ['c', 'd', 'e']
arr.slice(2, 4); // ['c', 'd']
arr.slice(-2); // ['d', 'e']
arr.concat(['f']); // ['a', 'b', 'c', 'd', 'e', 'f']
arr.at(1); // 'b'
arr.at(-1); // 'e'


// 改变原数组
arr.splice(-1); // arr = ['a', 'b', 'c', 'd']
arr.splice(1, 2); // arr = ['a', 'd', 'e']
arr.reverse(); // arr = ['e', 'd', 'c', 'b', 'a']

// find
// 迭代数组的每个元素，返回第一个函数返回值为 true 的元素
arr.find(item => item === 'a'); // 'a'
// findIndex
// 返回第一个匹配到的索引
arr.findIndex(item => item === 'a') // 0

// some/every
// 判断数组中是否有元素满足给定条件
arr.some(item => item === 'a'); // true
// 判断数组中是否**所有元素**都满足给定条件
arr.every(item => item === 'a'); // false

// flat
// 不改变原数组
// 传入一个number表示深度，默认为1
const arr = [[1, 2, 3], 4, 5];
arr.flat(1); // [1, 2, 3, 4, 5]
// map 和 flat(1) 的结合
arr.flatMap(item => item); // // [1, 2, 3, 4, 5]

// foreach
// 无法中断循环
arr.forEach((item, index, arr) => {
	// ...
});

// sort
// 改变了原数组
// 不传参数时，按照字符串顺序进行排序
arr.sort(); // ['a', 'b', 'c', 'd', 'e'];
// 传入比较函数
// return < 0, a, b 小于零不变
// return > 0, b, a 大于零就交换
const arr = [1, 0 , -4, 5];
arr.sort((a, b) => a - b); // [-4, 0, 1, 5]


// map
// 迭代处理数组的每个值，返回一个处理后的新数组
const newArr = arr.map((item, index, arr) => {
	//...
});

// filter
// 过滤满足条件的数组元素，返回一个新数组
const newArr = arr.filter((item, index, arr) => {
	// true 保留元素 
	return true;
});

// reduce
// 将原始数组的所有元素转换为一个单独的值
// 传入一个迭代函数和一个累加器的初始值，函数的第一个参数为累加器
// 每次迭代函数的返回值会被赋值给累加器,从而进行下一次迭代
const res = arr.reduce((acc, cur, i, arr) => {
	return acc + cur;
}, 0);

```

### String

```js
const str = 'abcde';

// 获取第一个匹配到的子串开始索引
str.indexOf(b); // 1
// 获取最后一个匹配到的子串开始索引
str.lastIndexOf(b); // 1

// 截取 [a, b) 范围的字符生成一个新字符串
str.slice(1, 3); // 'bc'
str.slice(1); // 'bcde'
// 负数表示从右边开始
str.slice(-2); // 'de'
str.slice(0, -2); // 'abc'

// 大小写转换
str.toUpperCase(); // 'ABCDE'
str.toLowerCase(); // 'abcde'

// 从两端移除空白字符
str.trim();
str.trimStart();
str.trimEnd();

// 替换
str.replace('ab', 'z'); // 'zcde
str.replaceAll('ab', 'z'); // 'zcde'
str.replace(/ab/g, 'z'); // 'zcde'

// 判断
str.includes('a'); // true
str.startsWith('a'); // true
str.endsWith('e'); // true

// 与数组相互转换
str.split(''); // ['a', 'b', 'c', 'd', 'e']
['a', 'b', 'c', 'd', 'e'].join(''); // 'abcde'

```

### Number

```js
```

### Math

```js
// 平方根
Math.sqrt(25); // 5

// 最大值
Math.max(2, 4, -1); // 4
// 最小值
Math.min(2, 4, -1); // -1

// 生成 [0, 1) 之间的随机数
Math.random();
// 生成 [min, max] 之间的随机整数
const randomInt = (min, max) => Math.floor(Math.random() * (max - min) + 1) + min

// 舍去小数部分
Math.trunc(23.3); // 23
// 四舍五入为整数
Math.round(23.3); // 23
// 向上取整
Math.ceil(23.3); // 24
// 向下取整
Math.floor(-23.3); // -24

```

### Date

```js
const now = new Date();
const future = new Date(2037, 10, 19, 15, 23);

future.getFullYear(); // 2037
future.getMonth(); // 10
future.getDate(); // 19
future.getHours(); // 15
future.getMinutes(); // 23
future.getSeconds(); // 0
// 一周的第几天， 0表示星期天
future.getDay(); // 4 
// 返回一个 ISO 格式的字符串,时区总是 UTC
future.toISOString(); // '2037-11-19T07:23:00.000Z'
// 时间戳，以毫秒为单位
future.getTime();
// 转换为数值（时间戳)
+future
Number(future)

// set
future.setFullYear(2040);
...

```

### Dom

```js
// 选择元素
document.documentElement; // html
document.head; // head
document.body; // body
// 返回一个 dom 元素
const element = document.querySelector('div');
document.getElementById('div-id');
// 返回一个 dom 元素列表，类型为 NodeList
document.querySelectorAll('div');
// 返回一个 HTMLCollection 类型的 dom 元素集合，会动态改变
document.getElementsByTagName('button');
document.getElementsByClassName('btn');

// 将指定的文本解析为dom元素，并将结果节点插入到 DOM 树中的指定位置
// position: 'beforebegin','afterbegin','beforeend','afterend'
element.insertAdjacentHTML(position, text);
// 创建元素
const div = document.createElement('div');
// 插入元素
// 在父节点的第一个子节点之前插入一系列Node对象或者DOMString对象
element.prepend(div);
// 在父节点的最后一个子节点之后插入一系列Node对象或者DOMString对象
element.append(div);
// 插入的 div 与 element 同级
element.before(div);
element.after(div);
// 删除
div.remove();


// 样式
getComputedStyle(element); // 获取所有样式计算属性值
element.style.backgroundColor = 'red';
element.style.setProperty('margin', '1px 2px');
element.style.setProperty('--color-primary', 'orangered'); // 设置css变量
element.className = 'primary';
element.classList.add('primary', 'hiden');
element.classList.remove('primary', 'hiden');
element.classList.contains('primary');
// 如果 primary 类值已存在，则移除它，否则添加它, 
element.classList.toggle('primary');

// 属性
// 已有属性直接获取
img.alt = 'img'
// dataset
element.dataset.versionNumber; // 在 html 中定义为 data-version-number
// 设置属性
element.setAttribute('my-attr', '123');
// 获取自定义属性
element.getAttribute('my-attr');


// 在 dom 元素上添加监听事件
dom.addEventListener('click', function(e) {
	// 阻止默认行为
	e.preventDefault();
})
dom.removeEventListener('click', handle)
```