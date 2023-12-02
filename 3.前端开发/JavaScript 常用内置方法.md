### Object

```js
```

### Array

```js
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