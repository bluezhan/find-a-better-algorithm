关于 React.FC 的使用争议，综合搜索结果中的技术讨论和官方态度，以下是关键要点分析：

---

### 一、React.FC 被官方建议弃用的背景

1. **官方态度变化**  
   React 团队在 2019 年的[create-react-app PR#8177](https://github.com/facebook/create-react-app/pull/8177)中移除了官方模板中的 React.FC，认为其存在设计缺陷且收益有限。  
   核心问题：`React.FC`通过`PropsWithChildren`隐式添加`children`属性，导致类型定义与实际使用不一致。

2. **社区实践趋势**  
   主流开源项目（如 Next.js、Vite 模板）已逐步弃用 React.FC，改用更简洁的函数声明方式。

---

### 二、React.FC 的主要缺陷

1. **隐式 children 属性问题**

   ```typescript
   type PropsWithChildren<P> = P & { children?: ReactNode };
   ```

   - **问题**：即使组件不需要`children`，外部仍可传入该属性，导致类型定义与实际逻辑不匹配。
   - **示例**：
     ```tsx
     const MyComponent: React.FC<{ title: string }> = ({ title }) => (
       <h1>{title}</h1>
     );
     // 外部仍可传入children，但组件未使用
     <MyComponent title="Test">Child</MyComponent>; // 无意义但合法
     ```

2. **与 defaultProps 的冲突**

   - **类组件兼容性问题**：`React.FC`组件的`defaultProps`会被 TypeScript 忽略，需手动合并类型。
     ```tsx
     interface Props {
       name?: string;
     }
     const MyComp: React.FC<Props> = ({ name }) => <div>{name}</div>;
     MyComp.defaultProps = { name: "Guest" }; // TS不会推断默认值类型
     ```

3. **代码冗余与灵活性不足**
   - **显式返回类型**：`React.FC`强制定义返回类型为`ReactElement | null`，而普通函数可通过类型推导简化代码。
   - **Hooks 兼容性**：使用`React.FC`时需额外注意 Hooks 类型，而普通函数更灵活。

---

### 三、替代方案及最佳实践

1. **普通函数组件声明**

   ```tsx
   interface Props {
     title: string;
     children?: ReactNode; // 按需显式声明
   }
   const MyComponent = ({ title, children }: Props) => (
     <div>
       <h1>{title}</h1>
       {children}
     </div>
   );
   ```

   - **优势**：精准控制 props 类型，避免隐式属性污染。

2. **处理默认属性**  
   使用 ES6 默认参数替代`defaultProps`：

   ```tsx
   interface Props {
     name?: string;
   }
   const MyComp = ({ name = "Guest" }: Props) => <div>{name}</div>;
   ```

3. **复杂场景增强类型**
   - 使用`ComponentProps`工具类型：
     ```tsx
     type ButtonProps = React.ComponentProps<"button"> & {
       variant: "primary" | "secondary";
     };
     ```
   - 需要`children`时显式引入`PropsWithChildren`：
     ```tsx
     import { PropsWithChildren } from "react";
     type Props = PropsWithChildren<{ title: string }>;
     ```

---

### 四、迁移建议

1. **逐步替换现有代码**  
   使用工具库如[`codemod-replace-react-fc-typescript`](https://github.com/gndelia/codemod-replace-react-fc-typescript)自动化迁移。

2. **团队规范统一**  
   在项目 Lint 规则中禁用`React.FC`（如通过 ESLint 规则`ban-types`）。

---

### 总结

尽管`React.FC`仍被部分遗留项目使用，但其设计缺陷已导致官方和社区逐渐弃用。通过普通函数组件+显式类型声明，开发者能获得更精准的类型安全性和代码简洁性。对于新项目，建议直接采用替代方案；旧项目可按需逐步迁移。
