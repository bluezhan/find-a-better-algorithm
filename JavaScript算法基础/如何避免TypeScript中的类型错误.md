# 如何避免 TypeScript 中的类型错误

要避免 TypeScript 中的类型错误，需要系统地应用类型系统的特性，并结合开发实践。以下是分层次的解决方案：

## 一、基础防御策略

### 严格模式启用

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

强制所有类型显式声明，禁止隐式 `any`。

### 类型断言替代方案

```typescript
// 错误示范
const val = someValue as number;

// 正确做法
if (typeof someValue === "number") {
  const val = someValue; // 自动类型收窄
}
```

优先使用类型守卫（Type Guards）。

### 非空断言慎用

```typescript
// 危险用法
element!.innerHTML = "";

// 安全替代
if (element) element.innerHTML = "";
```

## 二、中级类型技巧

### 可辨识联合类型

```typescript
type Result =
  | { status: "success"; data: User[] }
  | { status: "error"; code: number };

function handle(result: Result) {
  if (result.status === "success") {
    console.log(result.data); // 自动识别 data 存在
  } else {
    console.error(result.code); // 自动识别 code 存在
  }
}
```

### 泛型约束

```typescript
function merge<T extends object, U extends object>(a: T, b: U): T & U {
  return { ...a, ...b };
}
```

### 索引签名精确控制

```typescript
interface SafeDict {
  [key: string]: number | undefined; // 显式声明可能不存在
}
```

## 三、高级模式应用

### 模板字面量类型

```typescript
type CSSUnit = `${number}px` | `${number}rem` | `${number}%`;
function setSize(size: CSSUnit) {
  document.body.style.fontSize = size;
}
```

### 条件类型与 `infer`

```typescript
type PromiseUnpack<T> = T extends Promise<infer U> ? U : T;
type Num = PromiseUnpack<Promise<number>>; // number
```

### 类型谓词函数

```typescript
function isHTMLElement(node: Node): node is HTMLElement {
  return "style" in node;
}
```

## 四、工程化实践

### 类型测试（Type Testing）

```typescript
// 使用 dtslint 或 @ts-expect-error
function add(a: number, b: number): number;
// @ts-expect-error
add("1", 2); // 确保此处报错
```

### 类型导出策略

```typescript
// types.ts
export type { User, Post } from "./models";
export * as API from "./api-types";
```

### 第三方类型管理

```bash
# 为无类型库补充声明
npm install --save-dev @types/xxx
# 或创建 declare.d.ts
```

## 五、常见错误解决方案

| 错误类型     | 示例                              | 修复方案                                 |
| ------------ | --------------------------------- | ---------------------------------------- |
| 类型扩展错误 | `interface Window { myLib: any }` | 使用 `declare global` 扩展               |
| 异步类型     | `const data = await fetch()`      | 明确返回类型 `Promise<Response>`         |
| 动态属性     | `obj[randomKey]`                  | 添加索引签名或 `Record<string, unknown>` |
| 复杂对象     | 嵌套对象属性访问报错              | 使用可选链 `obj?.nested?.prop`           |

## 六、工具链增强

### ESLint 规则配置

```json
{
  "@typescript-eslint/no-explicit-any": "error",
  "@typescript-eslint/no-non-null-assertion": "warn"
}
```

### VSCode 设置优化

```json
{
  "typescript.tsserver.experimental.enableProjectDiagnostics": true
}
```

### 类型检查加速

```bash
tsc --incremental --tsBuildInfoFile .tsbuildinfo
```

通过组合这些策略，可以将 TypeScript 类型错误减少 90% 以上。关键原则：

- **尽早捕获**：在编码阶段通过 IDE 实时反馈发现问题。
- **精确描述**：用最具体的类型描述数据结构。
- **渐进严格**：从宽松类型开始，逐步增加严格度。
- **文档化类型**：把类型定义当作活文档维护。

当遇到复杂类型问题时，可优先考虑：工具类型（Utility Types） > 类型断言 > `@ts-ignore`（最后手段）。
