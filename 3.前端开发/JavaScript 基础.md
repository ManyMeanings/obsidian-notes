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

### 运算符

```js
// 指数运算
2 ** 3 // 2 * 2 * 2

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
### 假值

**假值**（**falsy**，有时写为 **falsey**）是在 Boolean上下文中认定为 false 的值。

JavaScript在需要用到布尔类型值的上下文中使用强制类型转换将值转换为布尔值，例如条件语句。

5个假值：0，''，undefined，null，NaN

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
const calc = test => true
```