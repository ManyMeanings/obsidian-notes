### Object

```js
```

### Array

```js
const arr = ['a', 'b', 'c', 'd', 'e'];

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

// foreach
// 无法中断循环
arr.forEach((item, index, arr) => {
	// ...
});

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
// 分解数组所有元素，将原始数组的值转换为一个单独的值
const res = arr.reduce();


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