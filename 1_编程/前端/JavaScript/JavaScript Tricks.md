```js
// 1.利用解构赋值交换变量的值
let x = 1,
    y = 2;
[x, y] = [y, x]; // x = 2, y = 1

// 2.数组去重
const arr = [1, 2, 2, 'abc', 'abc', true, true, false, false, undefined, undefined, NaN, NaN];
const result = [...new Set(arr)]; // [ 1, 2, 'abc', true, false, undefined, NaN ]

// 3.立即解构作为参数传入函数的对象
const obj = { foo: 1, bar: 2 };
function getObj({ foo, bar }) {
    return [foo, bar];
}
getObj(obj); // [1,2]

// 4.使用展开运算符浅拷贝数组/合并数组/浅拷贝对象
const arr = [1, 2, 3];
const copyArr = [...arr];
const newArr = [...arr, ...copyArr];
const obj = { a: 1};
const copyObj = { ...obj }

// 5.遍历数组和对象
for (const [index, item] of arr.entries()) {}
for (const [key, value] of Object.entries(obj)) {}

// 6.获取数组的最后一个元素
const arr = ['a', 'b', 'c', 'd', 'e'];
arr.at(-1); // 'e'

```
