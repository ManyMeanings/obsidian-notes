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
```

### Date

```js
```

### 其他

```js
```