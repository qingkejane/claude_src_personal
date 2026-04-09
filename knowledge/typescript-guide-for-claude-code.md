# TypeScript for Claude Source Code

## 1. 类型注解（Type Annotations）

TypeScript 在变量、参数、返回值上标注类型，编译器会检查你是否用对了。

```typescript
// 变量类型
let name: string = "Claude"
let count: number = 42
let isActive: boolean = true

// 函数参数和返回值
function greet(name: string): string {
  return "Hello, " + name
}

// 无返回值
function log(msg: string): void {
  console.log(msg)
}
```

**源码中的例子** ([Tool.ts](../source/unpack/src/Tool.ts)):
```typescript
call(args: z.infer<Input>, context: ToolUseContext, ...): Promise<ToolResult<Output>>
```
— `args` 参数类型由 Zod schema 推断，返回一个 Promise。

---

## 2. 接口与类型别名（Interface & Type）

### type —— 给类型起个名字

```typescript
type UserId = string
type Point = { x: number; y: number }

type Config = {
  debug: boolean
  verbose?: boolean       // ? 表示可选属性
  readonly name: string   // 只读，不可修改
}
```

### interface —— 描述对象的形状

```typescript
interface User {
  id: string
  name: string
  age?: number            // 可选
}

// interface 可以被 class 实现
class UserImpl implements User {
  id = "1"
  name = "Claude"
}
```

### type vs interface

在本源码中，`type` 用得更多。两者主要区别：
- `type` 可以表示联合类型、元组等更灵活的形式
- `interface` 可以被 extends 和 implements
- 大多数场景可以互换

**源码中的例子** ([types/permissions.ts](../source/unpack/src/types/permissions.ts)):
```typescript
export type PermissionAllowDecision<
  Input extends { [key: string]: unknown } = { [key: string]: unknown },
> = {
  behavior: 'allow'
  updatedInput?: Input
  userModified?: boolean
}
```

---

## 3. 联合类型（Union Types）

用 `|` 表示"或"的关系，值可以是多种类型之一。

```typescript
type Status = "loading" | "success" | "error"
type Result = string | number | null

function format(value: string | number): string {
  return String(value)
}
```

### 可辨识联合（Discriminated Unions）

通过一个公共字段（通常是 `type`）来区分不同情况，这是源码中**极常用**的模式。

**源码中的例子** ([types/permissions.ts](../source/unpack/src/types/permissions.ts)):
```typescript
export type PermissionUpdate =
  | { type: 'addRules'; rules: PermissionRuleValue[] }
  | { type: 'replaceRules'; rules: PermissionRuleValue[] }
  | { type: 'removeRules'; rules: string[] }

// 使用时通过 type 字段区分
function handleUpdate(update: PermissionUpdate) {
  switch (update.type) {
    case 'addRules':
      // TypeScript 知道这里有 rules: PermissionRuleValue[]
      break
    case 'removeRules':
      // TypeScript 知道这里是 rules: string[]
      break
  }
}
```

---

## 4. 泛型（Generics）

泛型是本源码的**核心特性**。它让类型像参数一样可以传递。

### 基础泛型

```typescript
// T 是类型参数，调用时确定
function identity<T>(value: T): T {
  return value
}

identity<string>("hello")   // 返回 string
identity(42)                // 自动推断为 number
```

### 多个泛型参数 + 约束

**源码中的例子** ([Tool.ts](../source/unpack/src/Tool.ts)):
```typescript
export type Tool<
  Input extends AnyObject = AnyObject,    // Input 必须是 AnyObject 的子类型，默认 AnyObject
  Output = unknown,                        // Output 任意类型，默认 unknown
  P extends ToolProgressData = ToolProgressData,
> = {
  call(args: z.infer<Input>, context: ToolUseContext): Promise<ToolResult<Output>>
  // ...
}
```

解读：
- `Input extends AnyObject` — Input 必须满足 AnyObject 的形状
- `= AnyObject` — 如果不传，默认是 AnyObject
- 所以 `Tool` 可以写成 `Tool<MyInput, MyOutput>` 或简写 `Tool<MyInput>`

### 泛型在 React Hook 中

**源码中的例子** ([state/AppState.tsx](../source/unpack/src/state/AppState.tsx)):
```typescript
export function useAppState<T>(selector: (state: AppState) => T): T {
  const store = useAppStore()
  const get = () => selector(store.getState())
  return useSyncExternalStore(store.subscribe, get, get)
}

// 使用：selector 返回什么类型，T 就是什么
const model = useAppState(state => state.model)  // T 推断为 string
```

---

## 5. 数组与只读数组

```typescript
let nums: number[] = [1, 2, 3]
let strs: Array<string> = ["a", "b"]     // 等价写法
let readonlyNums: readonly number[] = [1, 2, 3]  // 不可修改
```

---

## 6. const 断言（as const）

让 TypeScript 把值当作**最具体的字面量类型**，而不是宽泛的 string/number。

```typescript
// 没有 as const
let modes = ["default", "plan", "auto"]  // 类型：string[]

// 有 as const
const MODES = ["default", "plan", "auto"] as const
// 类型：readonly ["default", "plan", "auto"]
// 每个 element 都是具体的字面量类型
```

### 从 as const 数组提取联合类型

**源码中的例子** ([types/permissions.ts](../source/unpack/src/types/permissions.ts)):
```typescript
export const EXTERNAL_PERMISSION_MODES = [
  'acceptEdits',
  'bypassPermissions',
  'default',
  'dontAsk',
  'plan',
] as const

// 提取联合类型
export type ExternalPermissionMode = (typeof EXTERNAL_PERMISSION_MODES)[number]
// 等价于：'acceptEdits' | 'bypassPermissions' | 'default' | 'dontAsk' | 'plan'
```

拆解 `(typeof EXTERNAL_PERMISSION_MODES)[number]`：
- `typeof EXTERNAL_PERMISSION_MODES` — 获取变量的类型
- `[number]` — 数组的数字索引，提取所有元素的联合类型

---

## 7. 工具类型（Utility Types）

TypeScript 内置的类型转换工具，在本源码中大量使用。

```typescript
type Config = {
  debug: boolean
  verbose: boolean
  name: string
}

Partial<Config>       // 所有属性变可选：{ debug?: boolean; verbose?: boolean; name?: string }
Required<Config>      // 所有属性变必选
Readonly<Config>      // 所有属性变只读
Pick<Config, 'debug' | 'name'>    // 只取部分属性：{ debug: boolean; name: string }
Omit<Config, 'verbose'>           // 去掉部分属性：{ debug: boolean; name: string }
Record<string, number>            // 键为 string、值为 number 的对象：{ [key: string]: number }
```

### 源码中的组合使用

**源码中的例子** ([Tool.ts](../source/unpack/src/Tool.ts)):
```typescript
// ToolDef = Tool 去掉某些属性，再合并这些属性的 Partial 版本
export type ToolDef<Input, Output, P> =
  Omit<Tool<Input, Output, P>, DefaultableToolKeys> &
  Partial<Pick<Tool<Input, Output, P>, DefaultableToolKeys>>
```

逐步拆解：
1. `DefaultableToolKeys` — 一组可设默认值的属性名
2. `Pick<Tool<...>, DefaultableToolKeys>` — 取出这些属性
3. `Partial<...>` — 让它们变为可选
4. `Omit<Tool<...>, DefaultableToolKeys>` — Tool 去掉这些属性
5. `&` — 把两部分合并（交叉类型）

---

## 8. 映射类型（Mapped Types）

根据已有类型生成新类型。

```typescript
type FeatureFlags = {
  login: boolean
  darkMode: boolean
  notifications: boolean
}

// 把所有值变成 string
type FeatureStrings = {
  [K in keyof FeatureFlags]: string
}
// { login: string; darkMode: string; notifications: string }
```

**源码中的例子** ([Tool.ts](../source/unpack/src/Tool.ts)):
```typescript
type BuiltTool<D> = Omit<D, DefaultableToolKeys> & {
  [K in DefaultableToolKeys]-?: K extends keyof D
    ? undefined extends D[K]
      ? ToolDefaults[K]
      : D[K]
    : ToolDefaults[K]
}
```

- `[K in DefaultableToolKeys]` — 遍历每个 key
- `-?` — 移除可选标记（确保属性必填）
- `K extends keyof D ? ... : ...` — 条件判断

---

## 9. 异步编程（Async/Await）

### Promise

```typescript
// async 函数返回 Promise
async function fetchData(): Promise<string> {
  const response = await fetch("/api/data")
  return response.text()
}

// 使用
const data = await fetchData()
```

### Async Generator（异步生成器）

这是 query loop 的核心机制。

```typescript
// async function* 定义异步生成器
async function* streamMessages(): AsyncGenerator<string> {
  yield "Hello"
  yield "World"
}

// 使用 for await...of 消费
for await (const msg of streamMessages()) {
  console.log(msg)
}
```

**源码中的例子** ([query.ts](../source/unpack/src/query.ts)):
```typescript
export async function* query(params: QueryParams): AsyncGenerator<StreamEvent | Message> {
  // yield 流式事件
  yield { type: 'stream_start' }

  // yield 消息
  yield assistantMessage

  // 内部可以 await 异步操作
  const result = await callApi()

  yield { type: 'stream_end' }
}
```

---

## 10. 可选链与空值合并

```typescript
const user = { name: "Claude", address: { city: "SF" } }

// 可选链 ?.  — 如果前面是 null/undefined 就返回 undefined
user?.address?.city      // "SF"
user?.phone?.number      // undefined（不会报错）

// 空值合并 ??  — 如果左边是 null/undefined 就用右边
const name = user.nickname ?? "anonymous"
```

**源码中随处可见**：
```typescript
msg.data?.type !== 'hook_progress'
const config = options.customSystemPrompt ?? getDefaultPrompt()
```

---

## 11. 解构与展开

```typescript
// 对象解构
const { name, age } = { name: "Claude", age: 2 }

// 数组解构
const [first, ...rest] = [1, 2, 3]   // first=1, rest=[2,3]

// 展开运算符
const obj = { a: 1, b: 2 }
const newObj = { ...obj, c: 3 }      // { a: 1, b: 2, c: 3 }

const arr = [1, 2, 3]
const newArr = [...arr, 4]           // [1, 2, 3, 4]
```

---

## 12. 模块系统（import/export）

```typescript
// 命名导出
export function add(a: number, b: number): number { return a + b }
export type Config = { debug: boolean }

// 默认导出（每个文件最多一个）
export default class App { }

// 命名导入
import { add, type Config } from './utils'

// 默认导入
import App from './App'

// import type — 只导入类型，不产生运行时代码
import type { Message } from './types'
```

### 源码中的条件导入模式

本源码大量使用 `feature()` 控制的条件导入：

```typescript
// 条件 require + 类型断言
const reactiveCompact = feature('REACTIVE_COMPACT')
  ? (require('./services/compact/reactiveCompact.js') as typeof import('./services/compact/reactiveCompact.js'))
  : null

// 使用时需要判空
if (reactiveCompact) {
  reactiveCompact.doSomething()
}
```

- `require()` — 运行时加载，不参与 tree-shaking
- `as typeof import(...)` — 让 TypeScript 知道模块的类型
- 配合 `feature()` 编译时可以消除死代码

---

## 13. 类型守卫（Type Guards）

在条件分支中缩小类型范围。

```typescript
// typeof 守卫
function process(value: string | number) {
  if (typeof value === "string") {
    // 这里 value 是 string
    return value.toUpperCase()
  }
  // 这里 value 是 number
  return value.toFixed(2)
}

// 自定义类型守卫（类型谓词）
function isString(value: unknown): value is string {
  return typeof value === "string"
}
```

**源码中的例子** ([Tool.ts](../source/unpack/src/Tool.ts)):
```typescript
// 数组 filter 的类型谓词
function filterToolProgressMessages(
  progressMessages: ProgressMessage[],
): ProgressMessage<ToolProgressData>[] {
  return progressMessages.filter(
    (msg): msg is ProgressMessage<ToolProgressData> =>
      msg.data?.type !== 'hook_progress',
  )
}
```

`msg is ProgressMessage<ToolProgressData>` 告诉 TypeScript：如果这个回调返回 true，`msg` 的类型就是 `ProgressMessage<ToolProgressData>`。

---

## 14. Zod —— 运行时类型验证

Zod 是本源码重度依赖的库。TypeScript 的类型只在编译时存在，Zod 在**运行时**验证数据。

```typescript
import { z } from 'zod'

// 定义 schema
const UserSchema = z.object({
  name: z.string(),
  age: z.number().min(0),
  email: z.string().email().optional(),
})

// 从 schema 推断 TypeScript 类型
type User = z.infer<typeof UserSchema>
// 等价于：{ name: string; age: number; email?: string }

// 运行时验证
const result = UserSchema.safeParse(input)
if (result.success) {
  console.log(result.data.name)  // 类型安全
}
```

**源码中的模式** ([keybindings/schema.ts](../source/unpack/src/keybindings/schema.ts)):
```typescript
const KeybindingBlockSchema = z.object({
  context: z.enum(['Global', 'Chat', 'Autocomplete']),
  bindings: z.record(
    z.string(),
    z.union([z.enum(['action1', 'action2']), z.string().regex(/^command:.+$/), z.null()])
  ),
})

// 推断类型
type KeybindingBlock = z.infer<typeof KeybindingBlockSchema>
```

常见 Zod 方法：
- `z.string()`, `z.number()`, `z.boolean()` — 基本类型
- `z.object({})` — 对象
- `z.array(z.string())` — 数组
- `z.union([A, B])` 或 `A.or(B)` — 联合类型
- `z.literal("value")` — 字面量
- `z.enum(["a", "b"])` — 枚举
- `.optional()` — 可选
- `.default(value)` — 默认值
- `.describe("...")` — 描述（用于生成文档/prompt）
- `z.infer<typeof Schema>` — 从 schema 提取 TS 类型

---

## 15. TSX —— React 组件的写法

`.tsx` 文件 = TypeScript + JSX（HTML-like 语法）。

### 函数组件

```typescript
// 定义 props 类型
type GreetingProps = {
  name: string
  count?: number
}

// 函数组件
function Greeting({ name, count = 0 }: GreetingProps) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Count: {count}</p>
    </div>
  )
}
```

### React Hook

Hook 是以 `use` 开头的函数，用于在函数组件中管理状态和副作用。

```typescript
function Counter() {
  const [count, setCount] = useState(0)           // 状态
  const ref = useRef<HTMLDivElement>(null)         // DOM 引用

  useEffect(() => {                                 // 副作用
    console.log("count changed:", count)
    return () => console.log("cleanup")             // 清理函数
  }, [count])                                       // 依赖数组

  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

常见 Hook：
- `useState<T>(initial)` — 状态管理
- `useEffect(() => {}, [deps])` — 副作用
- `useRef<T>(initial)` — 可变引用
- `useContext(Context)` — 上下文消费
- `useCallback(fn, [deps])` — 缓存函数
- `useMemo(() => value, [deps])` — 缓存计算结果

### 本源码的特殊点：Ink（终端 UI）

Claude Code 不用浏览器 DOM，而是用 **Ink** 在终端渲染。组件写法相同，但用的是 Ink 的组件：

```typescript
import { Box, Text } from 'ink'

function MyComponent() {
  return (
    <Box flexDirection="column">
      <Text color="green">Success!</Text>
      <Text dimColor>Secondary info</Text>
    </Box>
  )
}
```

- `<Box>` — 类似 div，支持 flexbox 布局
- `<Text>` — 文本，支持颜色、粗体等

---

## 16. 其他常见语法

### 三元表达式

```typescript
const label = isActive ? "Active" : "Inactive"
```

### 模板字符串

```typescript
const greeting = `Hello, ${name}!`
```

### ?? 与 || 的区别

```typescript
const a = 0 || "default"     // "default"（0 是 falsy）
const b = 0 ?? "default"     // 0（?? 只在 null/undefined 时用默认值）
```

### satisfies 运算符

```typescript
const config = {
  debug: true,
  port: 3000,
} satisfies Record<string, string | number | boolean>
// 确保值满足类型，但保留每个属性的具体类型
```

### never 类型

```typescript
// switch 语句的穷尽检查
type Shape = "circle" | "square"

function getArea(shape: Shape): number {
  switch (shape) {
    case "circle": return Math.PI
    case "square": return 1
    default:
      const _exhaustive: never = shape  // 如果漏了某个 case，这里会报错
      throw new Error(`Unhandled: ${_exhaustive}`)
  }
}
```

---

## 速查表：阅读源码时的常见标记

| 语法 | 含义 | 例子 |
|------|------|------|
| `?:` | 可选属性 | `{ name?: string }` |
| `\|` | 联合类型（或） | `string \| number` |
| `&` | 交叉类型（且） | `A & B` |
| `T extends U` | 泛型约束 | `<T extends string>` |
| `as const` | 常量断言 | `[1, 2] as const` |
| `as Type` | 类型断言 | `value as string` |
| `keyof T` | 获取所有键 | `keyof User = "name" \| "age"` |
| `typeof x` | 获取变量的类型 | `typeof config` |
| `T[K]` | 索引访问类型 | `User["name"]` → `string` |
| `!` | 非空断言 | `user.name!` |
| `...args` | 剩余参数 | `function fn(...args: string[])` |
| `import type` | 仅类型导入 | `import type { X } from '...'` |
| `<T>` | 泛型参数 | `Array<string>` |
| `z.infer<>` | 从 Zod schema 提取类型 | `z.infer<typeof Schema>` |
