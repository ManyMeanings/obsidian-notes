
### 变量名规范

- 只能包含数字、字母、下划线和美元符号$
- 首字母不能是数字
- 不能是关键字和保留字
- 普通变量名采用驼峰命名法，常量使用大写

```js
let firstName = 'Rick';
const PI = 3.14
```

### 数据类型

- 包括基本数据类型和引用类型
- 七种基本数据类型：Number、String、Boolean、Undefined、Null、Symbol、BigInt
- 在比较两个操作数时，双等号（`==`）将执行类型转换，并且会按照 IEEE 754 标准对 `NaN`、`-0` 和 `+0` 进行特殊处理（故 `NaN != NaN`，且 `-0 == +0`）；
- 三等号（`===`）做的比较与双等号相同（包括对 `NaN`、`-0` 和 `+0` 的特殊处理）但不进行类型转换；如果类型不同，则返回 `false`；

```js
// BigInt 可以表示表示任意大小的整数
const x = BigInt(Number.MAX_SAFE_INTEGER); // 9007199254740991n
x + 1n === x + 2n; // false，因为 9007199254740992n 和 9007199254740993n 不相等
```

| x                   | y                   | `==`       | `===`      | `Object.is` | `SameValueZero` |
| ------------------- | ------------------- | ---------- | ---------- | ----------- | --------------- |
| `undefined`         | `undefined`         | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `null`              | `null`              | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `true`              | `true`              | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `false`             | `false`             | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `'foo'`             | `'foo'`             | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `0`                 | `0`                 | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `+0`                | `-0`                | `✅ true`  | `✅ true`  | `❌ false`  | `✅ true`       |
| `+0`                | `0`                 | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `-0`                | `0`                 | `✅ true`  | `✅ true`  | `❌ false`  | `✅ true`       |
| `0n`                | `-0n`               | `✅ true`  | `✅ true`  | `✅ true`   | `✅ true`       |
| `0`                 | `false`             | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `""`                | `false`             | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `""`                | `0`                 | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `'0'`               | `0`                 | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `'17'`              | `17`                | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `[1, 2]`            | `'1,2'`             | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `new String('foo')` | `'foo'`             | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `null`              | `undefined`         | `✅ true`  | `❌ false` | `❌ false`  | `❌ false`      |
| `null`              | `false`             | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `undefined`         | `false`             | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `{ foo: 'bar' }`    | `{ foo: 'bar' }`    | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `new String('foo')` | `new String('foo')` | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `0`                 | `null`              | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `0`                 | `NaN`               | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `'foo'`             | `NaN`               | `❌ false` | `❌ false` | `❌ false`  | `❌ false`      |
| `NaN`               | `NaN`               | `❌ false` | `❌ false` | `✅ true`   | `✅ true`       |
### 声明

- `var` 声明一个变量，可选初始化一个值。
- `let` 声明一个块作用域的局部变量，可选初始化一个值。
- `const` 声明一个块作用域的只读常量。
- `let` 和 `const` **会被提升**变量到代码块的顶部但是不会被赋予初始值。在变量声明之前引用这个变量，将抛出引用错误（ReferenceError）。这个变量将从代码块一开始的时候就处在一个“暂时性死区”，直到这个变量被声明为止。
- 对于函数来说，只有**函数声明**会被提升到顶部，而**函数表达式**不会被提升。

### 假值

**假值**（**falsy**，有时写为 **falsey**）是在 Boolean上下文中认定为 false 的值。

JavaScript在需要用到布尔类型值的上下文中使用强制类型转换将值转换为布尔值，例如条件语句。

5个假值：0，''，undefined，null，NaN

### 运算符

```js
// 指数运算
2 ** 3 // 2 * 2 * 2

// 展开运算符
const arr = [1, 2, 3];
const newArr = [4, 5, ...arr];
Math.max(...arr); // 传递多个参数
// 展开对象
const obj = { a: 1, b: 2 };
const obj2 = { ...obj };
// 可用于所有具备Iterator接口的数据结构
const s = 'hello';
const arr2 = [...s]; // arr2 = [h,e,l,l,0]

// 链判断运算符
// 左侧对象为 null 或 undefined 时直接返回 undefined
const firstName = message?.body?.user?.firstName || 'default';
// 属性表达式
obj?.[expr]
// 判断对象方法是否存在
obj.clac?.();

// 短路运算符
// 返回第一个真值，若全为假值，则返回最后一个
undefined || null // null
// 返回第一个假值，若全为真，则返回最后一个
true && '123' // '123' 

// Null 判断运算符
// 只有左侧为 undefined 或 null 时才返回右侧
undefined ?? '123' // '123'
'' ?? '123' // ''
0 ?? '123' // 0

// 逻辑赋值运算符
x ||= y // x || (x = y)
x &&= y // x && (x = y)
x ??= y // x ?? (x = y)

x = y = 20 - 10 // x = 10, y = 10
```

| Operator type          | Individual operators                      |
| ---------------------- | ----------------------------------------- |
| member                 | `. []`                                    |
| call / create instance | `() new`                                  |
| negation/increment     | `! ~ - + ++ -- typeof void delete`        |
| multiply/divide        | `* / %`                                   |
| addition/subtraction   | `+ -`                                     |
| bitwise shift          | `<< >> >>>`                               |
| relational             | `< <= > >= in instanceof`                 |
| equality               | `== != === !==`                           |
| bitwise-and            | `&`                                       |
| bitwise-xor            | `^`                                       |
| bitwise-or             | `\|`                                      |
| logical-and            | `&&`                                      |
| logical-or             | `\|`                                      |
| conditional            | `?:`                                      |
| assignment             | `= += -= *= /= %= <<= >>= >>>= &= ^= \|=` |
| comma                  | `,`                                       |

### 数值

```js
// 存储基于2进制带来的精度问题
0.1 + 0.2 === 0.3; // false

// toFixed 返回四舍五入指定位数的字符串
(2.7).toFixed(0); // '3'

// 字符串转数字
Number('23'); // 23
+'23'; // 23
// 从字符串中解析数字
Number.parseInt('30px'); // 30

// 检查是不是有限数
Number.isFinite(23); // true
Number.isFinite(NaN); // false
Number.isFinite(1 / 0); // false

// 检查是否是非数字
Number.isNaN('20'); // true

// 检查是否是整数
Number.isInteger(20.0); // true

// 数字分隔符
const money = 12_345_678_900; // 12345678900
```

#### BigInt

number类型在内部存储为64位，其中有53位存储数字，因此最大的安全数字为 `2^53 - 1`（数字从0开始）。

**`BigInt`** 是一种内置对象，它提供了一种方法来表示大于 `2^53 - 1` 的整数，可以表示任意大的整数。可以用在一个整数字面量后面加 `n` 的方式定义一个 `BigInt` ，如：`10n`，或者调用函数 `BigInt()`（但不包含 `new` 运算符）并传递一个整数值或字符串值。

BigInt 不能用于 Math 对象中的方法；不能和任何 Number 实例混合运算，两者必须转换成同一种类型。

```js
const x = 1231231231231231231n;
const y = BigInt(2123123123123123);

typeof x // 'bigint'
// number 和 bigint 可以比较
20n > 15; // true
20n === 20; // false
20n == 20; // true

```

### 字符串

对字符串调用方法时，javascript会将其转换为字符串对象，处理完后再转回字符串。

```js
const str = 'abcde';

str[1]; // b
str[1] = c; // 不生效，无法直接通过索引访问改变字符串
```
### 对象

#### 简写

```js
// 属性名简写
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}

// 方法简写
const o = {
  method() {
    return "Hello!";
  }
};

// getter & setter
// 不能为一个已有真实值的变量使用 set 和 get
const obj = {
  log: ["example", "test"],
  get latest() {
    if (this.log.length == 0) return undefined;
    return this.log[this.log.length - 1];
  },
  set latest(value) {
	  this.log.push(value)
  }
};
obj.latest // "test"
obj.latest = 'add';
obj.latest // 'add'
obj.log // ['example', 'test', 'qq']
```

#### 属性名表达式

```javascript
let lastWord = 'last word';

const a = {
  'first word': 'hello',
  [lastWord]: 'world',
  ['h' + 'ello']() {
    return 'hi';
  }
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
a.hello() // hi
```

### Set

类似于数组，但是成员的值都是唯一的，没有重复的值。

`Set`构造函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。

```javascript
const set = new Set([1, 2, 3, 4, 4]);
[...set]; // [1, 2, 3, 4]
set.size // 4

set.add(1); // 返回当前set对象
set.has(1); // true
set.delete(1); // true
set.clear();

for (let x of set) {
  console.log(x); // 1 2 3 4
}

set.forEach((value, key, map) => {});
```

#### WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，WeakSet 的成员只能是对象和 Symbol 值，而不能是其他类型的值。

WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。

另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。

### Map

类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```js
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size; // 2
map.has('name'); // true
map.get('name'); // 张三
map.set('age', 25); // 返回当前map对象
map.delete('age'); // true
map.clear();

for (let key of map.keys()) {} // 遍历键

for (let value of map.values()) {} // 遍历值

// 等同于使用map.entries()
for (let [key, value] of map) {} // 遍历键值对

map.forEach((value, key, map) => {})

// 对象转为 Map
let map = new Map(Object.entries(obj));
// Map 转为 Array
let arr = [...map];

```

#### WeakMap

只接受对象（`null`除外）和 Symbol 值作为键名，不接受其他类型的值作为键名。键名所指向的对象，不计入垃圾回收机制。

### 函数

```js
// 函数声明
function calc() {
 return true;
}

// 函数表达式/匿名函数
// 无法在声明之前调用
const calc = function () {
	return true
}

// 箭头函数
// 不存在 arguments 关键字
const calc = test => true

// 立即调用函数表达式/自执行匿名函数
(function () {
	//...
})();
// 箭头函数写法
(() => {
	//...
})();
// 作用1:避免污染全局命名空间/创建私有变量
(() => {
  // 初始化代码
  let firstVariable;
  let secondVariable;
})(); // firstVariable 和 secondVariable 变量在函数执行后会被丢弃
// 作用2:执行一个异步函数
(async () => {
  const stream = await getFileStream("https://domain.name/path/file.ext");
  for await (const chunk of stream) {
    console.log({ chunk });
  }
})();

// 参数默认值
function clac(param1 = 1, param2 = param1 * 2) {}

// 无用参数
calc(1, _, 2){};

// 对引用类型参数的修改会影响传入的外部参数
const obj = { a: 1 };
function change(param) {
	param.a = 2;
}
change(obj);
obj // { a: 2 } 

// 函数的 name 属性
clac.name; // 'calc'

// 高阶函数：把函数作为参数传入
// 委托处理
function clac(num1, num2, fn) {
	return fn(num1, num2); 
}
```

### 变量的解构赋值

#### 数组

```js
const arr = [1, 2, 3];
const arr2 = [1, [2, 3]];
let [x, y, z] = arr; // x = 1, y = 2, z = 3

// 根据模式匹配
let [x, , y] = arr; // x = 1, y = 3
let [x, ...y] = arr; // x = 1, y = [2, 3]
let [x, [y, z]] = arr2; // // x = 1, y = 2, z = 3

// 默认值
let [x = 4, , , y = 5] = arr; // x = 1, y = 5
```

#### 对象

```js
const obj = { foo: 1, bar: 2 };
let { foo, bar } = obj; // foo = 1, bar = 2

// 变量必须与属性同名
let { baz } = obj; // baz = undefined

// 指定新变量名
let { foo: f, bar: b } = obj; // f = 1, b = 2

// 默认值
let { foo = 2, bar, baz = 3 } = obj; // foo = 1, bar = 2, baz = 3

// 赋值已声明的变量
let a = 5;
({ foo: a } = obj); // a = 1

// 嵌套对象
const obj2 = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};
let { p: [x, { y }] } = obj2; // x = 'Hello', y = 'World'
```

### Iterator

遍历器（Iterator）是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

```js
interface Iterable {
  [Symbol.iterator]() : Iterator,
}

interface Iterator {
  next(value?: any) : IterationResult,
}

interface IterationResult {
  value: any,
  done: boolean,
}
```

一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内。

```js
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
obj[Symbol.iterator]().next(); // { value: 1, done: true }
// Generator 函数的简单实现
let myIterable = {
  [Symbol.iterator]: function* () {
    yield 1;
    yield 2;
    yield 3;
  }
};
[...myIterable] // [1, 2, 3]
```

原生具备遍历器接口的数据结构：
- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象
#### for...of

一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员。

```js
// 数组
var arr = ['a', 'b', 'c', 'd'];

for (let a in arr) {
  console.log(a); // 0 1 2 3
}

for (let a of arr) {
  console.log(a); // a b c d
}

// 对象
const obj = {
	a: 0,
	b: 1
	c: 2
}
for (const key of Object.keys(obj)) {
	console.log(key); // a b c
}
for (const value of Object.values(obj)) {
	console.log(value); // 0 1 2
}

```

### 国际化

```js
// 日期时间国际化
const now = new Date();
const locale = navigator.language;
const options = {
    weekday: "long",
    year: "numeric",
    month: "long",
    day: "numeric",
};
new Intl.DateTimeFormat(locale, options).format(now);

// 数值国际化
const num = 2884746.23;
const options = {};
new Intl.NumberFormat(locale, options).format(num);
```

### 计时器

```js
const timer = setTimeout(
    (param1, param2) => console.log("call afther 3 second"),
    3000,
    "param1",
    "param2"
);
clearTimeout(timer);

const timer = setInterval(
    (param1, param2) => console.log("call every 3 second"),
    3000,
    "param1",
    "param2"
);
clearInterval(timer);
```

### 类（Class）

类是用于创建对象的模板，实际上就是构造函数的语法糖，只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。类不存在变量提升。

```js
class Person {
	publicProps = 1; // 公共变量，可以从实例访问
	#privateProps = 1; // 私有变量，无法从外部访问
	static props = 1; // 静态变量，只在类中使用
	
    constructor(name, birthYear) {
        this.name = name;
        this.birthYear = birthYear;
    }

    get age() {
        return 2023 - this.birthYear;
    }

    set age(value) {
        this.birthYear = 2023 - value;
    }
    
    greet() {
        console.log(`hello, ${this.name}`);
    }

	// 静态方法只能通过类调用，无法被实例继承
	static hey() {
		console.log('call by Person');
	}
	
	#privateMethod() {
		// 私有方法，无法从外部访问
	}
}

const rick = new Person("rick", 1998);
rick.age; // 25
rick.age = 26;
rick.birthYear; // 1997
Person.hey(); // 'call by Person'

// 类的继承
class Student extends Person {
    constructor(name, birthYear, classNmae) {
        super(name, birthYear); // 调用父类构造函数
        this.className = className;
    }

    greet() {
        super.greet(); // super 指向父类的原型对象
        console.log(`from ${this.className}`);
    }
}

// 子类可以继承静态方法和静态属性
Student.hey();


```

