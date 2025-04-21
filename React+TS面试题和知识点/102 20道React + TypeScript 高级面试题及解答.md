### React + TypeScript 高级面试题及解答

以下是 20 道高级 React + TypeScript 面试题，每道题都包含详细解答、解决思路以及示例代码。

---

### **1. 如何在 React 中使用 TypeScript 定义组件的 Props 和 State？**

#### 解答：

在 React 中，使用 TypeScript 定义组件的 Props 和 State 时，可以通过接口或类型别名来定义它们的结构。

#### 示例代码：

```typescript
// 定义 Props 和 State 接口
interface MyComponentProps {
  title: string;
  count?: number; // 可选属性
}

interface MyComponentState {
  isVisible: boolean;
}

// 使用类组件
class MyComponent extends React.Component<MyComponentProps, MyComponentState> {
  state: MyComponentState = {
    isVisible: true,
  };

  render() {
    const { title, count } = this.props;
    const { isVisible } = this.state;

    return (
      <div>
        <h1>{title}</h1>
        {isVisible && <p>Count: {count ?? 0}</p>}
      </div>
    );
  }
}

// 使用函数组件
const MyFunctionalComponent: React.FC<MyComponentProps> = ({
  title,
  count,
}) => {
  return (
    <div>
      <h1>{title}</h1>
      <p>Count: {count ?? 0}</p>
    </div>
  );
};
```

---

### **2. 如何在 React 中使用 TypeScript 定义 Context？**

#### 解答：

使用 `React.createContext` 创建 Context 时，可以通过泛型指定 Context 的类型。

#### 示例代码：

```typescript
// 定义 Context 的类型
interface ThemeContextType {
  theme: "light" | "dark";
  toggleTheme: () => void;
}

// 创建 Context
const ThemeContext = React.createContext<ThemeContextType | undefined>(
  undefined
);

// 使用 Context
const ThemeProvider: React.FC = ({ children }) => {
  const [theme, setTheme] = React.useState<"light" | "dark">("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// 消费 Context
const ThemeConsumerComponent: React.FC = () => {
  const context = React.useContext(ThemeContext);

  if (!context) {
    throw new Error(
      "ThemeConsumerComponent must be used within a ThemeProvider"
    );
  }

  return (
    <div>
      <p>Current Theme: {context.theme}</p>
      <button onClick={context.toggleTheme}>Toggle Theme</button>
    </div>
  );
};
```

---

### **3. 如何在 React 中使用 TypeScript 定义自定义 Hook？**

#### 解答：

自定义 Hook 是一个以 `use` 开头的函数，可以通过泛型定义输入和输出的类型。

#### 示例代码：

```typescript
// 定义 Hook 的返回值类型
interface UseCounterReturn {
  count: number;
  increment: () => void;
  decrement: () => void;
}

// 自定义 Hook
const useCounter = (initialValue: number = 0): UseCounterReturn => {
  const [count, setCount] = React.useState(initialValue);

  const increment = () => setCount((prev) => prev + 1);
  const decrement = () => setCount((prev) => prev - 1);

  return { count, increment, decrement };
};

// 使用自定义 Hook
const CounterComponent: React.FC = () => {
  const { count, increment, decrement } = useCounter(10);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
};
```

---

### **4. 如何在 React 中使用 TypeScript 定义高阶组件 (HOC)？**

#### 解答：

高阶组件是一个接受组件并返回新组件的函数，可以通过泛型定义传递的 Props 类型。

#### 示例代码：

```typescript
// 定义 HOC 的类型
function withLogger<P>(WrappedComponent: React.ComponentType<P>): React.FC<P> {
  return (props: P) => {
    console.log("Props:", props);
    return <WrappedComponent {...props} />;
  };
}

// 使用 HOC
interface MyComponentProps {
  name: string;
}

const MyComponent: React.FC<MyComponentProps> = ({ name }) => {
  return <div>Hello, {name}!</div>;
};

const MyComponentWithLogger = withLogger(MyComponent);

// 渲染
<MyComponentWithLogger name="John" />;
```

---

### **5. 如何在 React 中使用 TypeScript 定义 ForwardRef？**

#### 解答：

使用 `React.forwardRef` 时，可以通过泛型定义传递的 ref 和 props 的类型。

#### 示例代码：

```typescript
// 定义组件的 Props
interface InputProps {
  placeholder?: string;
}

// 使用 forwardRef
const Input = React.forwardRef<HTMLInputElement, InputProps>((props, ref) => {
  return <input ref={ref} {...props} />;
});

// 使用 ForwardRef 组件
const ParentComponent: React.FC = () => {
  const inputRef = React.useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <Input ref={inputRef} placeholder="Type here..." />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
};
```

---

### **6. 如何在 React 中使用 TypeScript 定义动态组件？**

#### 解答：

动态组件可以通过泛型定义组件的 Props 类型，并使用 `React.ComponentType` 动态渲染。

#### 示例代码：

```typescript
// 定义动态组件的 Props
interface DynamicComponentProps {
  component: React.ComponentType<any>;
  props: any;
}

// 动态组件
const DynamicComponent: React.FC<DynamicComponentProps> = ({
  component: Component,
  props,
}) => {
  return <Component {...props} />;
};

// 使用动态组件
const HelloComponent: React.FC<{ name: string }> = ({ name }) => (
  <div>Hello, {name}!</div>
);

<DynamicComponent component={HelloComponent} props={{ name: "John" }} />;
```

---

### **7. 如何在 React 中使用 TypeScript 定义组件的默认 Props？**

#### 解答：

可以通过 `defaultProps` 或直接在函数参数中设置默认值。

#### 示例代码：

```typescript
interface MyComponentProps {
  name: string;
  age?: number;
}

const MyComponent: React.FC<MyComponentProps> = ({ name, age = 18 }) => {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
    </div>
  );
};

// 或者使用 defaultProps
MyComponent.defaultProps = {
  age: 18,
};
```

---

### **8. 如何在 React 中使用 TypeScript 定义异步数据加载？**

#### 解答：

可以使用 `useEffect` 和 `useState` 结合 TypeScript 定义异步数据加载。

#### 示例代码：

```typescript
interface User {
  id: number;
  name: string;
}

const UserList: React.FC = () => {
  const [users, setUsers] = React.useState<User[]>([]);
  const [loading, setLoading] = React.useState<boolean>(true);

  React.useEffect(() => {
    const fetchUsers = async () => {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/users"
      );
      const data: User[] = await response.json();
      setUsers(data);
      setLoading(false);
    };

    fetchUsers();
  }, []);

  if (loading) {
    return <p>Loading...</p>;
  }

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};
```

---

### **9. 如何在 React 中使用 TypeScript 定义事件处理函数？**

#### 解答：

在 React 中，事件处理函数的类型可以通过 `React.SyntheticEvent` 或具体的事件类型（如 `React.MouseEvent`）来定义。

#### 示例代码：

```typescript
const ButtonComponent: React.FC = () => {
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log("Button clicked:", event.currentTarget);
  };

  return <button onClick={handleClick}>Click Me</button>;
};
```

---

### **10. 如何在 React 中使用 TypeScript 定义表单组件？**

#### 解答：

表单组件的事件类型通常是 `React.ChangeEvent` 或 `React.FormEvent`。

#### 示例代码：

```typescript
interface FormState {
  username: string;
  email: string;
}

const FormComponent: React.FC = () => {
  const [formState, setFormState] = React.useState<FormState>({
    username: "",
    email: "",
  });

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = event.target;
    setFormState((prev) => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    console.log("Form submitted:", formState);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={formState.username}
        onChange={handleChange}
        placeholder="Username"
      />
      <input
        name="email"
        value={formState.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <button type="submit">Submit</button>
    </form>
  );
};
```

---

### **11. 如何在 React 中使用 TypeScript 定义组件的子组件类型？**

#### 解答：

可以通过 `React.ReactNode` 或 `React.ReactElement` 定义子组件的类型。

#### 示例代码：

```typescript
interface ParentProps {
  children: React.ReactNode;
}

const ParentComponent: React.FC<ParentProps> = ({ children }) => {
  return <div>{children}</div>;
};

// 使用
<ParentComponent>
  <p>This is a child component</p>
</ParentComponent>;
```

---

### **12. 如何在 React 中使用 TypeScript 定义组件的泛型？**

#### 解答：

可以通过泛型定义组件的 Props 类型，适用于高复用组件。

#### 示例代码：

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

const List = <T>({ items, renderItem }: ListProps<T>): React.ReactElement => {
  return <ul>{items.map(renderItem)}</ul>;
};

// 使用
const App: React.FC = () => {
  const numbers = [1, 2, 3];
  return (
    <List items={numbers} renderItem={(item) => <li key={item}>{item}</li>} />
  );
};
```

---

### **13. 如何在 React 中使用 TypeScript 定义 Portals？**

#### 解答：

Portals 是 React 提供的一种将子节点渲染到 DOM 树中其他位置的方法。

#### 示例代码：

```typescript
interface PortalProps {
  children: React.ReactNode;
  container: HTMLElement;
}

const Portal: React.FC<PortalProps> = ({ children, container }) => {
  return ReactDOM.createPortal(children, container);
};

// 使用
const App: React.FC = () => {
  const modalRoot = document.getElementById("modal-root")!;
  return (
    <Portal container={modalRoot}>
      <div>This is rendered in a portal!</div>
    </Portal>
  );
};
```

---

### **14. 如何在 React 中使用 TypeScript 定义 Error Boundary？**

#### 解答：

Error Boundary 是通过类组件实现的，可以使用 TypeScript 定义 Props 和 State。

#### 示例代码：

```typescript
interface ErrorBoundaryState {
  hasError: boolean;
}

class ErrorBoundary extends React.Component<{}, ErrorBoundaryState> {
  state: ErrorBoundaryState = { hasError: false };

  static getDerivedStateFromError(): ErrorBoundaryState {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error("Error caught:", error, info);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

---

### **15. 如何在 React 中使用 TypeScript 定义动画组件？**

#### 解答：

可以结合 `React.CSSProperties` 和动画库（如 `framer-motion`）定义动画组件。

#### 示例代码：

```typescript
interface AnimatedBoxProps {
  style?: React.CSSProperties;
}

const AnimatedBox: React.FC<AnimatedBoxProps> = ({ style }) => {
  return (
    <div style={{ ...style, transition: "all 0.3s ease" }}>I am animated!</div>
  );
};
```

---

### **16. 如何在 React 中使用 TypeScript 定义懒加载组件？**

#### 解答：

使用 `React.lazy` 和 `React.Suspense` 实现懒加载。

#### 示例代码：

```typescript
const LazyComponent = React.lazy(() => import("./LazyComponent"));

const App: React.FC = () => {
  return (
    <React.Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </React.Suspense>
  );
};
```

---

### **17. 如何在 React 中使用 TypeScript 定义 Reducer？**

#### 解答：

使用 `useReducer` 时，可以通过泛型定义状态和动作的类型。

#### 示例代码：

```typescript
interface State {
  count: number;
}

type Action = { type: "increment" } | { type: "decrement" };

const reducer = (state: State, action: Action): State => {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error("Unknown action");
  }
};

const Counter: React.FC = () => {
  const [state, dispatch] = React.useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>Increment</button>
      <button onClick={() => dispatch({ type: "decrement" })}>Decrement</button>
    </div>
  );
};
```

---

### **18. 如何在 React 中使用 TypeScript 定义 WebSocket 连接？**

#### 解答：

可以使用 `useEffect` 和 `WebSocket` API。

#### 示例代码：

```typescript
const WebSocketComponent: React.FC = () => {
  React.useEffect(() => {
    const socket = new WebSocket("ws://example.com/socket");

    socket.onmessage = (event) => {
      console.log("Message received:", event.data);
    };

    return () => {
      socket.close();
    };
  }, []);

  return <div>WebSocket Example</div>;
};
```

---

### **19. 如何在 React 中使用 TypeScript 定义动画过渡？**

#### 解答：

可以结合 `CSSTransition` 或 `framer-motion`。

#### 示例代码：

```typescript
import { motion } from "framer-motion";

const AnimatedComponent: React.FC = () => {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    >
      I am animated!
    </motion.div>
  );
};
```

---

### **20. 如何在 React 中使用 TypeScript 定义多语言支持 (i18n)？**

#### 解答：

可以结合 `react-i18next` 和 TypeScript。

#### 示例代码：

```typescript
import { useTranslation } from "react-i18next";

const MultiLangComponent: React.FC = () => {
  const { t } = useTranslation();

  return <div>{t("welcome_message")}</div>;
};
```

---

以上是完整的 20 道 React + TypeScript 高级面试题及解答！
