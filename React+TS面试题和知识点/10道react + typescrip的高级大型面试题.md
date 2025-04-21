以下是 10 道 React + TypeScript 高级大型面试题及参考答案：

---

### 1. 如何实现类型安全的高阶组件（HOC）并保留被包装组件的 Prop 类型？

**答案：**

```typescript
import React, { ComponentType } from "react";

type WithAuthProps = { isAuthenticated: boolean };

const withAuth = <P extends object>(WrappedComponent: ComponentType<P>) => {
  return (
    props: Omit<P, keyof WithAuthProps> & { isAuthenticated: boolean }
  ) => {
    return <WrappedComponent {...(props as P)} />;
  };
};

// 使用
interface UserProfileProps extends WithAuthProps {
  userName: string;
}

const UserProfile: React.FC<UserProfileProps> = ({
  userName,
  isAuthenticated,
}) => <div>{isAuthenticated ? userName : "Guest"}</div>;

export default withAuth(UserProfile);
```

关键点：使用泛型保留原始 props 类型，结合 Omit 和交叉类型处理注入的 props

---

### 2. 如何设计大型应用的类型声明结构？

**答案：**
推荐方案：

1. 使用声明合并扩展全局类型
2. 按功能模块划分类型目录（types/modules）
3. 使用命名空间组织复杂类型
4. 区分全局类型（global.d.ts）和模块局部类型
5. 使用类型导出/导入管理类型依赖
6. 结合 Zod 或 io-ts 实现运行时类型校验

---

### 3. 如何实现类型安全的 Context API + useReducer 架构？

**答案：**

```typescript
type AppState = { count: number };
type Action = { type: "INCREMENT" } | { type: "DECREMENT" };

const initialState: AppState = { count: 0 };

const reducer = (state: AppState, action: Action): AppState => {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    case "DECREMENT":
      return { count: state.count - 1 };
    default:
      return state;
  }
};

type ContextType = {
  state: AppState;
  dispatch: React.Dispatch<Action>;
};

const AppContext = React.createContext<ContextType>({} as ContextType);

// Provider组件实现
const AppProvider: React.FC = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
};
```

关键点：严格定义 Action 类型、使用泛型 Context、确保 dispatch 类型安全

---

### 4. 如何优化大型列表渲染性能并保证类型安全？

**方案：**

1. 使用 React.memo + 正确类型注解

```typescript
interface ListItemProps {
  id: number;
  content: string;
}
const MemoizedListItem = React.memo<ListItemProps>(({ id, content }) => (
  <div key={id}>{content}</div>
));
```

2. 虚拟化列表（react-window + 类型定义）
3. 使用 useCallback 处理事件处理器
4. 不可变数据更新策略
5. 分页/无限滚动类型定义

---

### 5. 如何实现类型安全的动态路由配置？

**答案：**

```typescript
type RouteConfig = {
  path: string;
  component: React.ComponentType;
  permissions?: string[];
  // 扩展路由元数据
  meta?: {
    title: string;
    requiresAuth: boolean;
  };
};

const routes: RouteConfig[] = [
  {
    path: "/dashboard",
    component: DashboardPage,
    meta: { title: "Dashboard", requiresAuth: true },
  },
  // ...
];

// 类型安全的路由渲染
const Router = () => (
  <BrowserRouter>
    <Switch>
      {routes.map((route) => (
        <Route
          key={route.path}
          path={route.path}
          render={(props) => {
            if (route.meta?.requiresAuth && !isAuthenticated) {
              return <Redirect to="/login" />;
            }
            return <route.component {...props} />;
          }}
        />
      ))}
    </Switch>
  </BrowserRouter>
);
```

---

### 6. 如何设计可扩展的表单管理系统？

**方案：**

1. 使用 React Hook Form + TypeScript
2. 定义通用表单字段类型：

```typescript
type FormField<T> = {
  name: keyof T;
  label: string;
  component: "input" | "select" | "textarea";
  validation?: ZodSchema; // 结合Zod
};
```

3. 创建动态表单生成器组件
4. 实现表单上下文管理
5. 集成验证库（Yup/Zod）类型声明

---

### 7. 如何实现类型安全的国际化方案？

**答案：**

1. 定义强类型翻译资源：

```typescript
type Locale = "en" | "zh";
type TranslationKeys = {
  welcome: string;
  buttons: {
    submit: string;
  };
};

const resources: Record<Locale, TranslationKeys> = {
  en: { welcome: "Welcome", buttons: { submit: "Submit" } },
  zh: { welcome: "欢迎", buttons: { submit: "提交" } },
};
```

2. 创建类型化的翻译 Hook：

```typescript
const useTranslation = () => {
  const [locale, setLocale] = useState<Locale>("en");
  const t = useCallback(
    (key: keyof TranslationKeys) => resources[locale][key],
    [locale]
  );
  return { t, setLocale };
};
```

---

### 8. 如何处理复杂组件树的类型传递问题？

**方案：**

1. 使用 ComponentPropsWithRef 获取组件 props 类型
2. 结合 typeof 获取默认 props 类型
3. 使用类型工具：

```typescript
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};
```

4. 创建类型化的组件复合模式：

```typescript
interface CompoundComponent extends React.FC<MainProps> {
  Header: React.FC<HeaderProps>;
  Body: React.FC<BodyProps>;
}
```

---

### 9. 如何实现类型安全的微前端架构？

**关键点：**

1. 定义共享类型库（独立 npm 包）
2. 使用模块联邦的类型同步
3. 跨应用通信的类型定义：

```typescript
type MicroFEProps<T> = {
  componentName: string;
  props: T;
  onEvent?: (event: MicroFEEvent) => void;
};

type MicroFEEvent = {
  type: "NAVIGATE" | "DATA_UPDATE";
  payload: unknown;
};
```

4. 容器应用的类型安全加载机制

---

### 10. 如何设计类型安全的 GraphQL 集成方案？

**答案：**

1. 使用 Apollo Client + TypeScript
2. 自动生成类型定义（GraphQL Code Generator）
3. 类型安全的 Hooks 使用：

```typescript
const GET_USER = gql`
  query GetUser($id: ID!) {
    user(id: $id) {
      id
      name
    }
  }
`;

const { data } = useQuery<{ user: User }, { id: string }>(GET_USER, {
  variables: { id: "123" },
});
```

4. 定义自定义 Scalar 类型
5. 实现类型安全的缓存更新策略

---

这些问题覆盖了 React+TS 大型应用开发中的类型系统设计、架构模式、性能优化和工程化实践等高级主题，能够有效考察候选人的深度技术能力。
