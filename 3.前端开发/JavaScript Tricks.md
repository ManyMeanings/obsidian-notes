```js
// 1.利用解构赋值交换变量的值
let x = 1,
    y = 2;
[x, y] = [y, x]; // x = 2, y = 1

// 2.数组去重
const arr = [1, 2, 2, 'abc', 'abc', true, true, false, false, undefined, undefined, NaN, NaN];
const result = Array.from(new Set(arr)); // [ 1, 2, 'abc', true, false, undefined, NaN ]

// 3.立即解构作为参数传入函数的对象
const obj = { foo: 1, bar: 2 };
function getObj({ foo, bar }) {
    return [foo, bar];
}
getObj(obj); // [1,2]
```