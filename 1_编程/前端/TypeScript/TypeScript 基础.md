## 入门

### TypeScript 特点

- 类型检查：在 ts 中为变量声明类型后，ts 编译器会对代码进行静态类型检查（_在编译阶段发现错误_），然后转换成 js 文件
- 代码智能提示
- 更好的重构支持

### 命令

```ts
npm i -g typescript // 全局安装
tsc --init // 生成 ts 配置文件
tsc // 将所有 ts 文件编译成 js 文件
```

### 常用配置

```json
{
  "compilerOptions": {
    "target": "es2016" /*生成的 js 版本*/,
    "rootDir": "./src", // 源文件根目录
    "outDir": "./dist", // 生成文件目录
    "removeComments": true, // 生成文件去除注释
    "noEmitOnError": true, // 存在类型错误时不生成文件
    "sourceMap": true, // 生成记录 ts 代码与生成代码映射关系的文件
    "noUnusedParameters": true, // 未使用的函数参数警告
    "noUnusedLocals": true, // 未使用的局部变量警告
    "allowUnreachableCode": false // 不可达的代码警告
  }
}
```

## 基础

```ts
// 基本类型声明
let sales: number = 123456;

// 初始化的变量可以自动推断
let sales = 123456; // sales: number

// 未初始化的变量类型为 any
let sales; // sales: any

// 数组
let numbers: number[] = [1, 2, 3];

// 元组 Tuples
// 表示固定长度的数组每一项有特定的类型
let user: [number, string] = [1, "rick"];

// 枚举 Enums
// 表示一系列相关的常量
enum Size {
  Small = 1,
  Medium,
  Large,
}
let mySize: Size = Size.Medium; // 2

// 函数
function calculateTax(income: number, taxYear: number = 2022): number {
  if (taxYear < 2022) return income * 1.2;
  return income * 1.3;
}

// 对象
let employee: {
  readonly id: number;
  name: string;
  retire: (date: Date) => void;
} = {
  id: 1,
  name: "Mosh",
  retire: (date) => console.log(date),
};
```

## 高级类型

```ts
// 定义类型别名
type Employee = {
  readonly id: number;
  name: string;
  retire: (date: Date) => void;
};

// 联合类型
function kgToLbs(weight: number | string): number {
  // Narrowing
  if (typeof weight === "number") return weight * 2.2;
  else return parseInt(weight) * 2.2;
}

// 交叉类型
// 常用来为对象类型添加新属性，表示对象的合成
type A = { foo: number };
type B = A & { bar: number };

// 字面量类型
type Metric = "cm" | "inch";

// 可选链
customer?.birthdate?.getFullYear();
customers?.[0];
log?.("message");

// 类型断言
// 可以用来指定一个更具体的类型
let phone = document.getElementById("phone") as HTMLInputElement;
let phone = <HTMLInputElement>document.getElementById("phone");

// unknown
// 不知道具体类型的情况下做类型检查
function render(document: unknown) {
  if (typeof document === "string") {
    return document.toLowerCase();
  }
  if (document instanceof Object) {
    return document.toString();
  }
}

// never
function reject(message: string): never {
  throw new Error(message);
}
reject("error");
console.log("never exec"); // 这段代码永远不会执行

// 声明对象的键值对类型
type SalaryObject = { [key: string]: number };
function getTotalSalary(salaryObject: SalaryObject) {
  let total = 0;
  for (const name in salaryObject) {
    total += salaryObject[name];
  }
  return total;
}
```

## 面向对象

### Class

```ts
class Account {
  readonly id: number; // 只读
  private _balance: number; // 私有，不可被继承
  // protected _balance: number; // 私有，可被继承
  nickname?: string; // 可选
  static desc: string = ""; // 通过类名访问 Account.desc

  constructor(id: number, balance: number) {
    this.id = id;
    this._balance = balance;
  }

  deposit(amount: number): void {
    this._balance += amount;
  }

  get balance(): number {
    return this._balance;
  }
}

// 继承
class Child extends Account {
  // 重写
  // 要求子类重写方法与父类被重写方法有相同的参数列表，有兼容的返回类型，比父类被重写方法更好访问，不能比父类被重写方法声明更多的异常
  override get balance(): number {
    return super.balance + 1;
  }
}

// 抽象类
// 用于继承，不能直接调用
abstract class Shape {
  constructor(public color: string) {}
  // 抽象方法
  abstract render(): void;
}

// 初始化简写形式
class Account1 {
  desc?: string;

  constructor(
    public readonly id: number,
    private _balance: number,
  ) {}
}
```

### Interface

`interface` 可以用来定义对象或类（替代不含代码逻辑的 `abstract`）的类型

```ts
// 定义接口
interface Calendar {
  name: string;
  addEvent(): void;
}

// 继承接口
interface GoogleCalendar extends Calendar {
  sync(): void;
}

// 应用接口
class GoogleCalendar implements GoogleCalendar {}
```

## 泛型

### 泛型类

```ts
class KeyValuePair<K, V> {
  constructor(
    public key: K,
    public value: V,
  ) {}
}

let pair = new KeyValuePair<number, string>(1, "s");
// ts 可以自动推断
let pair = new KeyValuePair(1, "s");
```

### 泛型函数

```ts
function wrapInArray<T>(value: T) {
  return [value];
}

let numbers = wrapInArray(1);
```

### 泛型接口

```ts
interface Result<T> {
  data: T | null;
}
```

### 泛型约束

```ts
function echo<T extends number | string>(value: T) {}

function echo<T extends { name: string }>(value: T) {}

// 约束为 interface 或 class
function echo<T extends Person>(value: T) {}
```

### 继承泛型类

```ts
// 传递一个泛型变量
class CompressibleStore<T> extends Store<T> {}

// 传递一个约束泛型
class SearchableStore<T extends { name: string }> extends Store<T> {}

// 修正泛型
class ProductStore extends Store<Product> {}
```

### `keyof`

```ts
interface Product {
  name: string;
  price: number;
}

let property: keyof Product;
// Same as
let property: "name" | "price";

property = "name";
property = "price";
property = "otherValue"; // Invalid
```

### 类型映射

```ts
type ReadOnly<T> = {
  readonly [K in keyof T]: T[K];
};

type Optional<T> = {
  [K in keyof T]?: T[K];
};

type Nullable<T> = {
  [K in keyof T]: T[K] | null;
};
```

### 类型工具

```ts
interface Product {
  id: number;
  name: string;
  price: number;
}

// A Product where all properties are optional
let product: Partial<Product>;

// A Product where all properties are required
let product: Required<Product>;

// A Product where all properties are read-only
let product: Readonly<Product>;

// A Product with two properties only (id and price)
let product: Pick<Product, "id" | "price">;

// A Product without a name
let product: Omit<Product, "name">;
```

## 装饰器

### 版本

TypeScript 从早期开始，就支持装饰器。但是，装饰器的语法后来发生了变化。ECMAScript 标准委员会最终通过的语法标准，与 TypeScript 早期使用的语法有很大差异。

目前，TypeScript 5.0 同时支持两种装饰器语法。标准语法可以直接使用，传统语法需要打开 `--experimentalDecorators` 编译参数。

下面使用标准语法介绍装饰器。

### 类装饰器

```ts
type ClassDecorator = (
  value: Function, // 类本身
  context: {
    kind: "class";
    name: string | undefined; // 类名
    addInitializer(initializer: () => void): void;
  },
) => Function | void;
```

### 方法装饰器

```ts
type ClassMethodDecorator = (
  value: Function, // 方法本身
  context: {
    kind: "method";
    name: string | symbol; // 方法名
    static: boolean; // 是否为静态方法
    private: boolean; // 是否为私有方法
    access: { get: () => unknown }; // 对象，包含了方法的存取器
    addInitializer(initializer: () => void): void; // 为方法增加初始化函数。
  },
) => Function | void;
```

### 属性装饰器

```ts
type ClassFieldDecorator = (
  value: undefined,
  context: {
    kind: "field";
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { get: () => unknown; set: (value: unknown) => void };
    addInitializer(initializer: () => void): void;
  },
) => (initialValue: unknown) => unknown | void;
```

### getter / setter 装饰器

```ts
type ClassGetterDecorator = (
  value: Function,
  context: {
    kind: "getter";
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { get: () => unknown };
    addInitializer(initializer: () => void): void;
  },
) => Function | void;

type ClassSetterDecorator = (
  value: Function,
  context: {
    kind: "setter";
    name: string | symbol;
    static: boolean;
    private: boolean;
    access: { set: (value: unknown) => void };
    addInitializer(initializer: () => void): void;
  },
) => Function | void;
```

### accessor 装饰器

```ts
class C {
  accessor x = 1;
}

// same as
class C {
  #x = 1;

  get x() {
    return this.#x;
  }

  set x(val) {
    this.#x = val;
  }
}
```

## 与 JavaScript 集成

混合使用 ts 和 js 时，需要修改 `tsconfig.json` 中的一些配置：

```json
{
  "compilerOptions": {
    "allowJs": true,
    "checkJs": true
  }
}
```

在 js 文件头添加 `// @ts-nocheck` 可以避免 ts 类型检查。

有两种方式为 js 模块添加类型声明：

- JSDoc 注释
- 新增 `[模块名].d.ts` 类型声明文件（_需要为所有导出的模块内容添加类型声明_）

### 依赖库

安装第三方库时，如果库里没有包含类型声明文件，可以安装社区提供的：

```zsh
npm i -D @types/<package>
```

注意：使用 yarn 作为包管理器时，由于 pnp 模式没有 node_modules 文件夹，需要做一些额外配置：[Editor SDKs | Yarn](https://yarnpkg.com/getting-started/editor-sdks)

### `declare`

declare 关键字用来告诉编译器，某个类型是存在的，可以在当前文件中使用。

它的主要作用，就是让当前文件可以使用其他文件声明的类型。它只是通知编译器某个类型是存在的，不用给出具体实现。比如，只描述函数的类型，不给出函数的实现，如果不使用 `declare`，这是做不到的。

## 与 React 集成

[Typing Component Props | React TypeScript Cheatsheets](https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example)

## 与 Vue 集成

[搭配 TypeScript 使用 Vue | Vue.js](https://cn.vuejs.org/guide/typescript/overview.html)
