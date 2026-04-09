# TypeScript Guide

## 第零部分：JavaScript 基础

TypeScript 是 JavaScript 的超集，先理解 JS 才能理解 TS。

### 0.1 变量声明

```javascript
// let —— 可重新赋值的变量（块级作用域）
let name = "Claude"
name = "Alice"            // OK

// const —— 不可重新赋值（推荐优先使用）
const PI = 3.14
PI = 3                    // 报错

// var —— 旧语法，有作用域问题，不要用
var x = 1

// 一行声明多个
let a = 1, b = 2, c = 3
```

### 0.2 数据类型

JavaScript 有 7 种原始类型 + 1 种引用类型：

```javascript
// 原始类型（不可变）
let str = "hello"                // string
let num = 42                     // number（整数和小数不分）
let float = 3.14                 // number
let bool = true                  // boolean
let n = null                     // null（空值）
let u = undefined                // undefined（未定义）
let big = 9007199254740991n      // BigInt（超大整数）
let sym = Symbol("id")           // Symbol（唯一标识符）

// 引用类型
let obj = { name: "Claude" }     // object
let arr = [1, 2, 3]              // object（数组也是对象）
let fn = function() {}           // object（函数也是对象）

// typeof 检查类型
typeof "hello"    // "string"
typeof 42         // "number"
typeof true       // "boolean"
typeof undefined  // "undefined"
typeof null       // "object"（这是 JS 的历史 bug）
typeof {}         // "object"
typeof []         // "object"
typeof fn         // "function"
```

### 0.3 字符串

```javascript
// 单引号、双引号、反引号都可以
let a = 'hello'
let b = "hello"
let c = `hello`

// 模板字符串（反引号）—— 支持插值和换行
let name = "Claude"
let greeting = `Hello, ${name}!`
let multi = `
  第一行
  第二行
  ${1 + 1} = 2
`

// 常用方法
"hello".length          // 5
"hello".toUpperCase()   // "HELLO"
"hello".includes("ell") // true
"hello".slice(1, 3)    // "el"
"hello".split("")       // ["h", "e", "l", "l", "o"]
"a,b,c".split(",")     // ["a", "b", "c"]
"  hi  ".trim()        // "hi"
```

### 0.4 数字

```javascript
// 整数和小数都是 number
let int = 42
let float = 3.14
let neg = -10
let hex = 0xff          // 255（十六进制）

// 算术运算
1 + 2       // 3
10 - 3      // 7
4 * 5       // 20
10 / 3      // 3.333...（不是整数除法！）
10 % 3      // 1（取余）
2 ** 10     // 1024（幂运算）

// 特殊值
Infinity           // 无穷大
-Infinity          // 无穷小
NaN                // Not a Number（0/0 等非法运算的结果）
Number.isNaN(NaN)  // true
isNaN("hello")     // true（会先转换再判断）
```

### 0.5 比较与逻辑

```javascript
// 比较运算符
1 === 1        // true（严格相等，类型和值都相同）✅ 推荐用这个
1 == "1"       // true（宽松相等，会做类型转换）❌ 避免使用
1 !== "1"      // true（严格不等）
1 != "1"       // false（宽松不等）
1 > 2          // false
1 < 2          // true
1 >= 1         // true

// 逻辑运算符
true && false    // false（与）
true || false    // true（或）
!true            // false（非）

// 短路求值（常用于默认值）
let name = null
let display = name || "anonymous"    // "anonymous"
let display2 = name ?? "anonymous"   // "anonymous"

// || 与 ?? 的区别
0 || "default"     // "default"（0 是 falsy）
0 ?? "default"     // 0（?? 只在 null/undefined 时触发）

// falsy 值（以下值在布尔上下文中为 false）
// false, 0, -0, "", null, undefined, NaN
// 其他所有值为 truthy（包括 [], {}, "0", "false"）
```

### 0.6 条件语句

```javascript
// if-else
let age = 20
if (age >= 18) {
  console.log("adult")
} else if (age >= 12) {
  console.log("teenager")
} else {
  console.log("child")
}

// 三元表达式
let label = age >= 18 ? "adult" : "minor"

// switch
let color = "red"
switch (color) {
  case "red":
    console.log("红色")
    break
  case "blue":
    console.log("蓝色")
    break
  default:
    console.log("其他")
}
```

### 0.7 循环

```javascript
// for 循环
for (let i = 0; i < 5; i++) {
  console.log(i)   // 0, 1, 2, 3, 4
}

// while 循环
let i = 0
while (i < 5) {
  console.log(i)
  i++
}

// for...of（遍历值）
let arr = [10, 20, 30]
for (let item of arr) {
  console.log(item)   // 10, 20, 30
}

// for...in（遍历键）
let obj = { a: 1, b: 2 }
for (let key in obj) {
  console.log(key, obj[key])   // "a" 1, "b" 2
}

// break 和 continue
for (let i = 0; i < 10; i++) {
  if (i === 3) continue   // 跳过本次
  if (i === 7) break      // 终止循环
  console.log(i)          // 0, 1, 2, 4, 5, 6
}
```

### 0.8 函数

```javascript
// 函数声明
function add(a, b) {
  return a + b
}
add(1, 2)   // 3

// 默认参数
function greet(name, greeting = "Hello") {
  return `${greeting}, ${name}!`
}
greet("Claude")               // "Hello, Claude!"
greet("Claude", "Hi")        // "Hi, Claude!"

// 剩余参数
function sum(...nums) {
  return nums.reduce((a, b) => a + b, 0)
}
sum(1, 2, 3, 4)   // 10

// 箭头函数
const double = (x) => x * 2
const add2 = (a, b) => a + b

// 箭头函数带函数体
const greet2 = (name) => {
  let msg = `Hello, ${name}`
  return msg
}

// 函数作为参数（回调）
function doMath(a, b, operation) {
  return operation(a, b)
}
doMath(3, 4, (a, b) => a + b)   // 7
doMath(3, 4, (a, b) => a * b)   // 12
```

### 0.9 对象

```javascript
// 创建对象
let user = {
  name: "Claude",
  age: 2,
}

// 访问属性
user.name          // "Claude"（点号访问）
user["name"]       // "Claude"（方括号访问）
user.email         // undefined（不存在的属性）

// 修改和添加属性
user.age = 3              // 修改
user.email = "a@b.com"   // 添加新属性

// 删除属性
delete user.email

// 检查属性是否存在
"name" in user            // true
user.hasOwnProperty("name") // true

// 对象解构
let { name, age } = user
console.log(name)  // "Claude"

// 解构并重命名
let { name: userName } = user
console.log(userName)  // "Claude"

// 解构带默认值
let { email = "none" } = user
console.log(email)  // "none"

// 展开运算符（浅拷贝）
let user2 = { ...user, age: 5 }
// { name: "Claude", age: 5 }

let merged = { ...user, ...{ role: "admin" } }
// { name: "Claude", age: 3, role: "admin" }

// Object 静态方法
Object.keys(user)      // ["name", "age"]
Object.values(user)    // ["Claude", 3]
Object.entries(user)   // [["name", "Claude"], ["age", 3]]
```

### 0.10 数组

```javascript
// 创建数组
let nums = [1, 2, 3, 4, 5]
let mixed = [1, "two", true, null]   // JS 数组可以混合类型

// 访问
nums[0]             // 1
nums[nums.length - 1] // 5（最后一个元素）
nums.at(-1)         // 5（负索引，从末尾数）

// 修改
nums[0] = 10        // [10, 2, 3, 4, 5]

// 数组解构
let [first, second, ...rest] = [1, 2, 3, 4, 5]
// first=1, second=2, rest=[3,4,5]

// 展开运算符
let combined = [...nums, 6, 7]   // [10, 2, 3, 4, 5, 6, 7]

// 常用方法
nums.push(6)                  // 末尾添加，返回新长度
nums.pop()                    // 末尾删除，返回删除的值
nums.unshift(0)               // 开头添加
nums.shift()                  // 开头删除
nums.includes(3)              // true
nums.indexOf(3)               // 2
nums.slice(1, 3)              // [2, 3]（不改变原数组）
nums.splice(1, 2)             // 从索引 1 删除 2 个（改变原数组）
nums.concat([7, 8])           // 合并，返回新数组
nums.reverse()                // 反转（改变原数组）
[3, 1, 2].sort()              // [1, 2, 3]（默认按字符串排序）
[3, 1, 2].sort((a, b) => a - b)  // [1, 2, 3]（数字排序）
Array.from("hello")           // ["h", "e", "l", "l", "o"]

// 高阶方法（不改变原数组，返回新数组或值）
let doubled = [1, 2, 3].map(x => x * 2)          // [2, 4, 6]
let evens = [1, 2, 3, 4].filter(x => x % 2 === 0) // [2, 4]
let found = [1, 2, 3].find(x => x > 2)            // 3（第一个满足的）
let hasLarge = [1, 2, 3].some(x => x > 2)         // true
let allPositive = [1, 2, 3].every(x => x > 0)     // true
let sum = [1, 2, 3].reduce((acc, x) => acc + x, 0) // 6
[1, 2, 3].forEach(x => console.log(x))            // 遍历，无返回值
```

### 0.11 Promise 与异步编程

JavaScript 是单线程的，通过事件循环处理异步操作。

```javascript
// Promise —— 代表一个未来的值（成功或失败）
let promise = new Promise((resolve, reject) => {
  // 模拟异步操作（如网络请求）
  setTimeout(() => {
    resolve("成功的数据")
  }, 1000)
})

// 消费 Promise
promise
  .then(data => console.log(data))       // 1 秒后输出 "成功的数据"
  .catch(error => console.error(error))  // 失败时
  .finally(() => console.log("完成"))     // 无论成功失败都执行


// async/await —— Promise 的语法糖（更直观）
async function fetchData() {
  try {
    let response = await fetch("https://api.example.com/data")  // 等待请求完成
    let data = await response.json()                            // 等待解析 JSON
    return data
  } catch (error) {
    console.error("请求失败:", error)
  }
}

// 调用 async 函数
fetchData().then(data => console.log(data))


// 并行执行多个 Promise
let p1 = fetch("/api/users")
let p2 = fetch("/api/posts")

// 等待全部完成（有一个失败就整体失败）
let [users, posts] = await Promise.all([p1, p2])

// 等待任意一个完成
let fastest = await Promise.race([p1, p2])

// 等待全部完成（无论成功失败，收集所有结果）
let results = await Promise.allSettled([p1, p2])
```

### 0.12 解构与展开总结

```javascript
// ===== 解构 =====

// 数组解构
let [a, b, c] = [1, 2, 3]           // a=1, b=2, c=3
let [first, ...rest] = [1, 2, 3]    // first=1, rest=[2,3]
let [, second] = [1, 2, 3]          // second=2（跳过第一个）

// 对象解构
let { name, age } = { name: "Claude", age: 2 }
let { x = 10 } = {}                  // x=10（默认值）

// 函数参数解构
function draw({ x, y, color = "black" }) {
  console.log(x, y, color)
}
draw({ x: 1, y: 2 })                // 1 2 "black"
draw({ x: 1, y: 2, color: "red" })  // 1 2 "red"


// ===== 展开 =====

// 展开数组
let a1 = [1, 2]
let a2 = [3, 4]
let combined = [...a1, ...a2]        // [1, 2, 3, 4]

// 展开对象（后面的属性覆盖前面的）
let o1 = { a: 1, b: 2 }
let o2 = { ...o1, b: 3, c: 4 }      // { a: 1, b: 3, c: 4 }

// 展开函数参数
let args = [1, 2, 3]
Math.max(...args)                    // 等价于 Math.max(1, 2, 3)
```

### 0.13 可选链与空值合并

```javascript
// 可选链 ?. —— 安全访问可能为 null/undefined 的属性
let user = { name: "Claude", address: { city: "SF" } }

user.address.city         // "SF"
user.phone?.number        // undefined（不会报错）
user.getAddress?.()       // undefined（方法不存在也不报错）
user.tags?.[0]            // undefined（数组也不报错）

// 空值合并 ?? —— 左边为 null/undefined 时使用右边
let name = null
let display = name ?? "unknown"    // "unknown"

let count = 0
let show = count ?? 10             // 0（0 不是 null/undefined）
```

### 0.14 class 类

```javascript
class Animal {
  constructor(name) {
    this.name = name
  }

  speak() {
    return `${this.name} makes a sound`
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name)          // 调用父类构造函数
    this.breed = breed
  }

  speak() {
    return `${this.name} barks`
  }
}

let dog = new Dog("Rex", "Husky")
dog.speak()              // "Rex barks"
dog instanceof Dog       // true
dog instanceof Animal    // true
```

### 0.15 模块（import/export）

```javascript
// ===== math.js（导出）=====

// 命名导出
export const PI = 3.14
export function add(a, b) { return a + b }

// 默认导出（每个文件最多一个）
export default class Calculator { }


// ===== app.js（导入）=====

// 命名导入
import { PI, add } from './math.js'

// 默认导入
import Calculator from './math.js'

// 全部导入
import * as math from './math.js'
math.add(1, 2)   // 3

// 重命名导入
import { add as sum } from './math.js'
```

---

## 第一部分：TypeScript 基础篇

---

### 1. 什么是 TypeScript

TypeScript = JavaScript + **类型系统**。类型在编译时检查，编译后生成普通 JavaScript，类型信息被擦除。

```typescript
// JavaScript — 运行时才发现错误
function add(a, b) {
  return a + b
}
add("1", "2")  // 返回 "12"，不是 3，但没有报错

// TypeScript — 编译时就发现错误
function add(a: number, b: number): number {
  return a + b
}
add("1", "2")  // 编译报错：Argument of type 'string' is not assignable to parameter of type 'number'
```

---

### 2. 安装与运行

```bash
# 安装
npm install -g typescript

# 编译
tsc hello.ts          # 生成 hello.js

# 直接运行（需要 tsx 或 ts-node）
npx tsx hello.ts
```

---

### 3. 基本类型

```typescript
// 原始类型
let str: string = "hello"
let num: number = 42
let bool: boolean = true
let n: null = null
let u: undefined = undefined

// 特殊类型
let anything: any = "could be anything"        // 关闭类型检查，尽量避免
let unknown: unknown = "safe any"              // 比 any 安全，使用前必须检查
let nothing: never = undefined as never        // 永远不会有值的类型
let empty: void = undefined                    // 函数没有返回值时用

// 数组
let nums: number[] = [1, 2, 3]
let strs: Array<string> = ["a", "b", "c"]      // 等价写法

// 元组（Tuple）—— 固定长度和类型的数组
let pair: [string, number] = ["age", 25]
let triple: [string, number, boolean] = ["name", 1, true]
```

---

### 4. 类型注解与类型推断

TypeScript 能自动推断很多类型，不需要处处标注。

```typescript
// 显式注解
let name: string = "Claude"

// 自动推断 —— name2 的类型也被推断为 string
let name2 = "Claude"

// 函数返回值也可以自动推断
function double(x: number) {   // 返回值推断为 number
  return x * 2
}

// 但参数必须注解（无法推断）
function greet(name: string) {  // name 必须标注
  return "Hello, " + name
}
```

**原则**：能推断的就不写，参数和返回值推荐写。

---

### 5. 对象类型

```typescript
// 内联写法
function printUser(user: { name: string; age: number }) {
  console.log(user.name, user.age)
}

// 可选属性用 ?
function printConfig(config: { host: string; port?: number }) {
  console.log(config.host, config.port ?? 3000)
}

printConfig({ host: "localhost" })           // OK，port 可选
printConfig({ host: "localhost", port: 8080 }) // OK
```

---

### 6. 函数

```typescript
// 参数类型 + 返回值类型
function add(a: number, b: number): number {
  return a + b
}

// 可选参数（用 ? 标记，必须在必选参数后面）
function greet(name: string, title?: string): string {
  return title ? `${title} ${name}` : name
}

// 默认参数
function greet2(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}`
}

// 剩余参数
function sum(...nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0)
}

// 箭头函数
const multiply = (a: number, b: number): number => a * b

// 函数类型（把函数本身当作类型）
type MathOp = (a: number, b: number) => number
const add2: MathOp = (a, b) => a + b
```

---

### 7. type 和 interface

#### type —— 类型别名

```typescript
// 基本别名
type ID = string
type Point = { x: number; y: number }

// 联合类型
type Status = "active" | "inactive"

// 复杂对象
type User = {
  id: string
  name: string
  email?: string             // 可选
  readonly createdAt: Date   // 只读
}
```

#### interface —— 接口

```typescript
interface Animal {
  name: string
  speak(): string
}

interface Dog extends Animal {        // 继承
  breed: string
}

// 接口可以声明合并（同名自动合并）
interface Config {
  host: string
}
interface Config {
  port: number        // 自动合并到上面的 Config
}
// 最终 Config = { host: string; port: number }
```

#### type vs interface 对比

| 能力 | type | interface |
|------|------|-----------|
| 描述对象 | ✅ | ✅ |
| 联合类型 | ✅ `A \| B` | ❌ |
| 交叉组合 | ✅ `A & B` | ✅ `extends` |
| 基本类型别名 | ✅ `type X = string` | ❌ |
| 声明合并 | ❌ | ✅ |
| class implements | ✅ | ✅ |

**选择建议**：个人/团队偏好。本指南两者都会展示。

---

### 8. class 类

```typescript
class Person {
  // 属性声明
  name: string
  private age: number           // 私有，类外部不可访问
  protected id: string          // 受保护，子类可访问
  public email: string          // 公开（默认）

  // 构造函数
  constructor(name: string, age: number, email: string) {
    this.name = name
    this.age = age
    this.email = email
  }

  // 方法
  greet(): string {
    return `Hi, I'm ${this.name}`
  }
}

// 简写（构造函数参数自动赋值）
class Person2 {
  constructor(
    public name: string,
    private age: number,
  ) {}
}

// 继承
class Employee extends Person2 {
  constructor(
    name: string,
    age: number,
    public role: string,     // 新增的公开属性
  ) {
    super(name, age)         // 调用父类构造函数
  }
}

// 实现接口
interface Loggable {
  log(message: string): void
}

class Logger implements Loggable {
  log(message: string): void {
    console.log(message)
  }
}
```

#### 访问修饰符

| 修饰符 | 类内部 | 子类 | 外部 |
|--------|--------|------|------|
| `public` | ✅ | ✅ | ✅ |
| `protected` | ✅ | ✅ | ❌ |
| `private` | ✅ | ❌ | ❌ |

---

### 9. 枚举（Enum）

```typescript
// 数字枚举（自动从 0 递增）
enum Direction {
  Up,       // 0
  Down,     // 1
  Left,     // 2
  Right,    // 3
}

// 字符串枚举
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE",
}

let dir: Direction = Direction.Up
let color: Color = Color.Red
```

#### 更推荐的做法：const enum 或 as const

```typescript
// const enum — 编译后内联，不生成额外代码
const enum Status {
  Active = "ACTIVE",
  Inactive = "INACTIVE",
}

// 或用 as const + type 提取
const ROLES = ["admin", "editor", "viewer"] as const
type Role = (typeof ROLES)[number]   // "admin" | "editor" | "viewer"
```

---

### 10. 字面量类型（Literal Types）

```typescript
// 单个字面量
let direction: "up" | "down" = "up"

// 数字字面量
let port: 3000 | 8080 = 3000

// 布尔字面量
let mode: true = true

// 结合函数使用
function setAlign(align: "left" | "center" | "right") {
  // align 只能是这三个值之一
}

setAlign("left")    // OK
setAlign("top")     // 报错
```

---

### 11. 联合类型与交叉类型

#### 联合类型（Union）—— "或"

```typescript
type ID = string | number

function findUser(id: ID) {
  if (typeof id === "string") {
    // 这里 id 是 string
    return id.toUpperCase()
  }
  // 这里 id 是 number
  return id.toFixed(0)
}
```

#### 交叉类型（Intersection）—— "且"

```typescript
type Named = { name: string }
type Aged = { age: number }

type Person = Named & Aged
// 等价于 { name: string; age: number }

const p: Person = { name: "Claude", age: 2 }
```

---

### 12. 可辨识联合（Discriminated Unions）

通过一个公共字段区分联合类型中的不同变体，是 TypeScript 中**最重要的模式之一**。

```typescript
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "rectangle"; width: number; height: number }
  | { kind: "triangle"; base: number; height: number }

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2         // TypeScript 知道这里有 radius
    case "rectangle":
      return shape.width * shape.height           // TypeScript 知道这里有 width, height
    case "triangle":
      return 0.5 * shape.base * shape.height
  }
}
```

`kind` 就是"辨识字段"（discriminant），switch 之后 TypeScript 自动收窄类型。

---

### 13. 类型守卫（Type Guards）

在条件分支中缩小类型范围。

```typescript
// typeof 守卫 —— 适用于原始类型
function process(value: string | number) {
  if (typeof value === "string") {
    return value.toUpperCase()     // 这里 value 是 string
  }
  return value.toFixed(2)          // 这里 value 是 number
}

// instanceof 守卫 —— 适用于类
class Dog { bark() {} }
class Cat { meow() {} }
function speak(pet: Dog | Cat) {
  if (pet instanceof Dog) {
    pet.bark()
  } else {
    pet.meow()
  }
}

// in 操作符 —— 检查属性是否存在
type Fish = { swim: () => void }
type Bird = { fly: () => void }
function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim()
  } else {
    animal.fly()
  }
}

// 自定义类型守卫
function isString(val: unknown): val is string {
  return typeof val === "string"
}

// 使用
function processUnknown(val: unknown) {
  if (isString(val)) {
    console.log(val.toUpperCase())   // TypeScript 知道 val 是 string
  }
}
```

---

### 14. 类型断言（Type Assertions）

告诉编译器"我比你更了解这个类型"。

```typescript
// as 语法（推荐）
const input = document.getElementById("input") as HTMLInputElement
input.value = "hello"

// 尖括号语法（JSX 文件中不可用）
const input2 = <HTMLInputElement>document.getElementById("input")

// 非空断言 ! —— 告诉编译器值一定不是 null/undefined
const el = document.querySelector(".box")!
console.log(el.className)

// 注意：断言不会做运行时转换，滥用会导致运行时错误
const x = "hello" as number   // 报错：string 不能断言为 number
const x2 = "hello" as unknown as number  // 可以绕过，但很危险
```

---

### 15. 模块系统

```typescript
// ===== 导出 =====

// 命名导出
export const PI = 3.14
export function add(a: number, b: number): number { return a + b }
export type User = { name: string }
export interface Config { debug: boolean }

// 默认导出（每个文件最多一个）
export default class App {
  // ...
}

// 重新导出
export { add as sum } from './math'
export * from './utils'              // 导出 utils 的所有导出


// ===== 导入 =====

// 命名导入
import { add, type User } from './math'

// 默认导入
import App from './App'

// 全部导入
import * as math from './math'

// 仅类型导入（编译后消失，不产生运行时代码）
import type { Config } from './config'

// 内联 type 修饰符
import { type User, add } from './math'   // User 是纯类型，add 是值
```

---

## 第二部分：进阶篇

---

### 16. 泛型（Generics）

泛型让类型变成参数，实现类型级别的"函数"。

#### 基础

```typescript
// 泛型函数
function identity<T>(value: T): T {
  return value
}

identity<string>("hello")   // 显式指定 T = string
identity(42)                // 自动推断 T = number

// 泛型接口
interface Box<T> {
  value: T
}

const strBox: Box<string> = { value: "hello" }
const numBox: Box<number> = { value: 42 }

// 泛型类型别名
type Pair<A, B> = { first: A; second: B }
const p: Pair<string, number> = { first: "age", second: 25 }

// 泛型类
class Stack<T> {
  private items: T[] = []
  push(item: T) { this.items.push(item) }
  pop(): T | undefined { return this.items.pop() }
}
```

#### 泛型约束（extends）

```typescript
// T 必须有 length 属性
function logLength<T extends { length: number }>(value: T): void {
  console.log(value.length)
}

logLength("hello")      // OK，string 有 length
logLength([1, 2, 3])   // OK，array 有 length
logLength(123)          // 报错，number 没有 length

// 约束为另一个类型
type WithId = { id: string }
function findById<T extends WithId>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id)
}
```

#### 泛型默认值

```typescript
type Result<T = string, E = Error> = {
  data: T | null
  error: E | null
}

const r1: Result = { data: "hello", error: null }         // T=string, E=Error
const r2: Result<number> = { data: 42, error: null }      // T=number, E=Error
const r3: Result<number, string> = { data: 42, error: "fail" }
```

#### 常用泛型工具函数模式

```typescript
// 键值对提取
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}

const user = { name: "Claude", age: 2 }
getProperty(user, "name")          // 返回 string
getProperty(user, "age")           // 返回 number
getProperty(user, "email")         // 报错，"email" 不是 user 的 key
```

---

### 17. 工具类型（Utility Types）

TypeScript 内置的类型转换函数。

```typescript
interface User {
  name: string
  age: number
  email: string
  avatar: string
}

// Partial<T> — 所有属性变可选
type PartialUser = Partial<User>
// { name?: string; age?: number; email?: string; avatar?: string }

// Required<T> — 所有属性变必选
type RequiredUser = Required<PartialUser>

// Readonly<T> — 所有属性变只读
type ReadonlyUser = Readonly<User>

// Pick<T, K> — 只取部分属性
type UserPreview = Pick<User, "name" | "avatar">
// { name: string; avatar: string }

// Omit<T, K> — 去掉部分属性
type UserWithoutEmail = Omit<User, "email" | "avatar">
// { name: string; age: number }

// Record<K, V> — 键值对对象
type UserMap = Record<string, User>
// { [key: string]: User }

// Exclude<T, U> — 从联合类型中排除
type T = Exclude<"a" | "b" | "c", "a">
// "b" | "c"

// Extract<T, U> — 从联合类型中提取
type T2 = Extract<"a" | "b" | "c", "a" | "b">
// "a" | "b"

// NonNullable<T> — 排除 null 和 undefined
type T3 = NonNullable<string | null | undefined>
// string

// ReturnType<T> — 获取函数返回值类型
function getUser() { return { name: "Claude", age: 2 } }
type GetUserReturn = ReturnType<typeof getUser>
// { name: string; age: number }

// Parameters<T> — 获取函数参数类型（元组）
type GetUserParams = Parameters<typeof getUser>
// []

// Awaited<T> — 获取 Promise 解析后的类型
type Data = Awaited<Promise<string>>
// string
```

---

### 18. 映射类型（Mapped Types）

根据一个类型生成另一个类型。

```typescript
// 基础映射
type Stringify<T> = {
  [K in keyof T]: string
}

type UserStrings = Stringify<{ name: string; age: number }>
// { name: string; age: string }

// 让所有属性可选
type MyPartial<T> = {
  [K in keyof T]?: T[K]
}

// 让所有属性只读
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K]
}

// 移除 readonly
type Mutable<T> = {
  -readonly [K in keyof T]: T[K]
}

// 移除可选（-?）
type Required2<T> = {
  [K in keyof T]-?: T[K]
}

// 过滤属性
type PickByValue<T, V> = {
  [K in keyof T as T[K] extends V ? K : never]: T[K]
}

type User = { name: string; age: number; active: boolean }
type StringFields = PickByValue<User, string>
// { name: string }
```

映射类型语法总结：
- `[K in keyof T]` — 遍历 T 的所有 key
- `T[K]` — 获取原始值类型
- `?` / `-?` — 添加/移除可选
- `readonly` / `-readonly` — 添加/移除只读
- `as NewKeyType` — 键的重映射

---

### 19. 条件类型（Conditional Types）

类型层面的 if-else。

```typescript
// 基础语法：T extends U ? X : Y
type IsString<T> = T extends string ? "yes" : "no"

type A = IsString<string>    // "yes"
type B = IsString<number>    // "no"

// 实际应用：扁平化 Promise
type Unwrap<T> = T extends Promise<infer U> ? U : T

type X = Unwrap<Promise<string>>   // string
type Y = Unwrap<number>            // number
```

#### infer 关键字

在条件类型中"推断"一个类型变量。

```typescript
// 提取数组元素类型
type ElementOf<T> = T extends (infer E)[] ? E : T
type T1 = ElementOf<string[]>      // string
type T2 = ElementOf<number>        // number

// 提取函数返回值类型（简化版 ReturnType）
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never

// 提取函数第一个参数类型
type FirstParam<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never
type FP = FirstParam<(name: string, age: number) => void>  // string
```

#### 分布式条件类型

```typescript
// 当 T 是联合类型时，条件类型会自动分发
type ToArray<T> = T extends unknown ? T[] : never

type Result = ToArray<string | number>
// 自动分发为：ToArray<string> | ToArray<number>
// 结果：string[] | number[]

// 阻止分发：用 [T] 包裹
type ToArray2<T> = [T] extends [unknown] ? T[] : never
type Result2 = ToArray2<string | number>
// 结果：(string | number)[]
```

---

### 20. 模板字面量类型（Template Literal Types）

```typescript
type EventName = "click" | "focus" | "blur"
type EventHandler = `on${Capitalize<EventName>}`
// "onClick" | "onFocus" | "onBlur"

// 内置字符串操作类型
type Upper = Uppercase<"hello">        // "HELLO"
type Lower = Lowercase<"HELLO">        // "hello"
type Cap = Capitalize<"hello">         // "Hello"
type Uncap = Uncapitalize<"Hello">     // "hello"

// 实际应用：CSS 属性
type CSSProperty = "margin" | "padding"
type CSSDirection = "top" | "right" | "bottom" | "left"
type CSSRule = `${CSSProperty}-${CSSDirection}`
// "margin-top" | "margin-right" | ... | "padding-left"
```

---

### 21. 索引访问类型（Indexed Access Types）

```typescript
type User = { name: string; age: number; address: { city: string } }

// 用键访问
type Name = User["name"]            // string
type Age = User["age"]              // number

// 嵌套访问
type City = User["address"]["city"] // string

// 联合键
type UserInfo = User["name" | "age"] // string | number

// keyof 获取所有键
type UserKeys = keyof User          // "name" | "age" | "address"

// 结合数组
const roles = ["admin", "editor", "viewer"] as const
type Roles = typeof roles[number]   // "admin" | "editor" | "viewer"
```

---

### 22. as const 与 const 断言

```typescript
// 让对象的所有属性变为 readonly + 字面量类型
const config = {
  host: "localhost",
  port: 3000,
} as const
// 类型：{ readonly host: "localhost"; readonly port: 3000 }

// 数组的 as const
const ROLES = ["admin", "editor", "viewer"] as const
// 类型：readonly ["admin", "editor", "viewer"]

// 提取联合类型
type Role = (typeof ROLES)[number]
// "admin" | "editor" | "viewer"
```

---

### 23. satisfies 运算符

验证值满足某个类型，但**保留更具体的类型信息**。

```typescript
type ColorMap = Record<string, [number, number, number] | string>

// 用注解 —— 类型被拓宽
const colors1: ColorMap = {
  red: [255, 0, 0],
  blue: "blue",
}
colors1.red[0]        // number ✅
colors1.red.join(",") // 报错 ❌ — TypeScript 不确定 red 是数组还是 string

// 用 satisfies —— 保留具体类型
const colors2 = {
  red: [255, 0, 0],
  blue: "blue",
} satisfies ColorMap
colors2.red[0]        // 255（具体数字）✅
colors2.red.join(",") // OK ✅ — TypeScript 知道 red 是 [number, number, number]
colors2.blue.toUpperCase() // OK ✅ — TypeScript 知道 blue 是 string
```

---

### 24. 异步编程类型

```typescript
// Promise
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`)
  return response.json()
}

// Async Generator
async function* paginate(url: string): AsyncGenerator<User[]> {
  let page = 1
  while (true) {
    const users = await fetch(`${url}?page=${page}`).then(r => r.json())
    if (users.length === 0) break
    yield users
    page++
  }
}

// 消费异步生成器
for await (const users of paginate("/api/users")) {
  console.log(users)
}
```

---

### 25. 函数重载（Function Overloads）

同一个函数支持多种参数组合。

```typescript
// 重载签名
function format(value: string): string
function format(value: number): string
function format(value: string | number): string {
  if (typeof value === "string") {
    return value.trim()
  }
  return value.toFixed(2)
}

format("  hello  ")  // "hello"
format(3.14159)      // "3.14"
format(true)         // 报错
```

---

### 26. this 类型

```typescript
class Calculator {
  private value = 0

  add(n: number): this {      // 返回 this，支持链式调用
    this.value += n
    return this
  }

  subtract(n: number): this {
    this.value -= n
    return this
  }

  getResult(): number {
    return this.value
  }
}

new Calculator().add(10).subtract(3).getResult()  // 7
```

---

### 27. 装饰器（Decorators）

装饰器是实验性功能，用于修改类、方法、属性、参数的行为。

```typescript
// 方法装饰器
function log(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${key} with`, args)
    return original.apply(this, args)
  }
}

class Calculator {
  @log
  add(a: number, b: number): number {
    return a + b
  }
}

new Calculator().add(1, 2)
// 输出：Calling add with [1, 2]
```

启用装饰器需要在 `tsconfig.json` 中设置：
```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

---

### 28. 声明文件（.d.ts）

为没有类型的 JavaScript 库提供类型信息。

```typescript
// types/my-lib.d.ts
declare module "my-lib" {
  export function doSomething(value: string): number
  export interface Config {
    debug: boolean
  }
}
```

```typescript
// 使用
import { doSomething, Config } from "my-lib"
```

---

### 29. 类型收窄（Type Narrowing）

TypeScript 在以下场景会自动收窄类型：

```typescript
// 1. typeof
function fn(x: string | number) {
  if (typeof x === "string") { /* x 是 string */ }
}

// 2. instanceof
function fn2(x: Date | string) {
  if (x instanceof Date) { /* x 是 Date */ }
}

// 3. in 操作符
function fn3(x: { a: string } | { b: number }) {
  if ("a" in x) { /* x 是 { a: string } */ }
}

// 4. 字面量类型检查
function fn4(x: "a" | "b") {
  if (x === "a") { /* x 是 "a" */ }
}

// 5. 可辨识联合
function fn5(x: { kind: "a"; a: string } | { kind: "b"; b: number }) {
  if (x.kind === "a") { /* x.a 存在 */ }
}

// 6. 赋值收窄
let x: string | number = "hello"
x.toUpperCase()  // OK，赋值后收窄为 string

// 7. Truthiness 收窄
function fn6(x: string | null) {
  if (x) { /* x 是 string */ }
}

// 8. 类型谓词
function isNonNull<T>(value: T | null): value is T {
  return value !== null
}
```

---

### 30. 递归类型

```typescript
// JSON 值
type JSONValue =
  | string
  | number
  | boolean
  | null
  | JSONValue[]
  | { [key: string]: JSONValue }

// 深度只读
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object
    ? DeepReadonly<T[K]>
    : T[K]
}

// 深度 Partial
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object
    ? DeepPartial<T[K]>
    : T[K]
}

// 提取 Promise 链的最终类型
type AwaitedDeep<T> = T extends Promise<infer U>
  ? AwaitedDeep<U>
  : T

type T = AwaitedDeep<Promise<Promise<Promise<string>>>>
// string
```

---

### 31. 协变与逆变

```typescript
// 协变（Covariant）—— 子类型关系保持方向
interface Animal { name: string }
interface Dog extends Animal { breed: string }

// Dog[] 可以赋值给 Animal[]（协变）
const dogs: Dog[] = [{ name: "Rex", breed: "Husky" }]
const animals: Animal[] = dogs  // OK

// 逆变（Contravariant）—— 函数参数类型方向相反
type AnimalHandler = (animal: Animal) => void
type DogHandler = (dog: Dog) => void

// DogHandler 不能赋值给 AnimalHandler
// 因为 AnimalHandler 可能传入 Cat，DogHandler 处理不了
let dogHandler: DogHandler = (d) => console.log(d.breed)
let animalHandler: AnimalHandler = dogHandler  // 严格模式下报错

// 函数返回值是协变的
type CreateAnimal = () => Animal
type CreateDog = () => Dog
let createDog: CreateDog = () => ({ name: "Rex", breed: "Husky" })
let createAnimal: CreateAnimal = createDog  // OK
```

---

### 32. 全局类型扩展

```typescript
// 扩展全局类型
declare global {
  interface Array<T> {
    last(): T | undefined
  }
}

// 现在所有数组都有 last() 方法
[1, 2, 3].last()  // 3

// 扩展 Process 环境变量类型
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      API_KEY: string
      NODE_ENV: "development" | "production"
    }
  }
}
```

---

## 速查表

### 常见符号

| 符号 | 含义 | 示例 |
|------|------|------|
| `:` | 类型注解 | `let x: string` |
| `as` | 类型断言 | `x as number` |
| `!` | 非空断言 | `x!.toString()` |
| `?` | 可选属性/参数 | `{ name?: string }` |
| `??` | 空值合并 | `x ?? default` |
| `?.` | 可选链 | `user?.name` |
| `\|` | 联合类型 | `string \| number` |
| `&` | 交叉类型 | `A & B` |
| `keyof` | 取所有键 | `keyof User` |
| `typeof` | 取值类型 | `typeof x` |
| `infer` | 条件推断 | `infer R` |
| `...` | 展开/剩余 | `[...arr]`, `...args` |

### 关键字对照

| 关键字 | 用途 |
|--------|------|
| `type` | 定义类型别名 |
| `interface` | 定义接口 |
| `enum` | 定义枚举 |
| `extends` | 继承 / 泛型约束 |
| `implements` | 类实现接口 |
| `declare` | 声明外部类型 |
| `abstract` | 抽象类/方法 |
| `readonly` | 只读 |
| `namespace` | 命名空间 |
| `satisfies` | 类型验证（保留具体类型） |

### 泛型常用写法

```typescript
<T>                     // 无约束泛型
<T extends string>      // 约束为 string 的子类型
<T = string>            // 默认类型参数
<T extends keyof U>     // 约束为 U 的键
<T extends any[]>(>     // 约束为数组
<infer R>               // 在条件类型中推断
```
