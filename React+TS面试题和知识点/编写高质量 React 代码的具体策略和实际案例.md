以下是编写高质量 React 代码的具体策略和实际案例，涵盖组件设计、状态管理、性能优化和可维护性等多个维度：

---

### 一、**组件设计策略**

#### **1. 原子化组件设计**

- **策略**：将组件拆分为最小的可复用单元（原子 → 分子 → 组织 → 页面）。
- **案例**：一个按钮组件如何通过 Props 控制样式和行为。

  ```jsx
  // 原子组件：Button
  const Button = ({ variant = "primary", onClick, children }) => {
    const baseStyle = "px-4 py-2 rounded";
    const variants = {
      primary: "bg-blue-500 text-white",
      secondary: "bg-gray-200 text-black",
    };
    return (
      <button className={`${baseStyle} ${variants[variant]}`} onClick={onClick}>
        {children}
      </button>
    );
  };

  // 使用场景
  <Button variant="secondary" onClick={handleClick}>
    Cancel
  </Button>;
  ```

- **注意**：避免在原子组件中混入业务逻辑。

---

#### **2. 容器组件与展示组件分离**

- **策略**：容器组件负责数据获取和逻辑，展示组件仅负责 UI 渲染。
- **案例**：用户列表的容器和展示分离。

  ```jsx
  // 容器组件：UserListContainer
  const UserListContainer = () => {
    const [users, setUsers] = useState([]);
    useEffect(() => {
      fetchUsers().then((data) => setUsers(data));
    }, []);
    return <UserList users={users} />;
  };

  // 展示组件：UserList (纯UI)
  const UserList = ({ users }) => (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
  ```

- **优势**：逻辑与 UI 解耦，便于测试和复用。

---

### 二、**状态管理策略**

#### **1. 状态提升与 Context 分层**

- **策略**：共享状态提升到最近的公共父组件，复杂场景使用分层 Context。
- **案例**：使用 Context 实现主题切换。

  ```jsx
  // 主题 Context
  const ThemeContext = createContext("light");
  const ThemeProvider = ({ children }) => {
    const [theme, setTheme] = useState("light");
    return (
      <ThemeContext.Provider value={{ theme, setTheme }}>
        {children}
      </ThemeContext.Provider>
    );
  };

  // 子组件消费 Context
  const ThemedButton = () => {
    const { theme, setTheme } = useContext(ThemeContext);
    return (
      <Button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Toggle Theme
      </Button>
    );
  };
  ```

- **注意**：避免单一 Context 存储过多数据，按业务拆分多个 Context。

---

#### **2. 使用 Zustand 或 Redux Toolkit 管理全局状态**

- **策略**：复杂跨组件状态使用轻量级状态管理库。
- **案例**：Zustand 实现购物车状态。

  ```jsx
  // store/cartStore.js
  import create from "zustand";

  const useCartStore = create((set) => ({
    items: [],
    addItem: (item) => set((state) => ({ items: [...state.items, item] })),
    clearCart: () => set({ items: [] }),
  }));

  // 组件中使用
  const AddToCartButton = ({ product }) => {
    const addItem = useCartStore((state) => state.addItem);
    return <Button onClick={() => addItem(product)}>Add to Cart</Button>;
  };
  ```

- **优势**：避免 Prop Drilling，状态更新自动触发组件重渲染。

---

### 三、**性能优化策略**

#### **1. 记忆化（Memoization）**

- **策略**：使用 `useMemo` 缓存计算结果，`useCallback` 缓存函数。
- **案例**：优化复杂计算和子组件 Props。

  ```jsx
  const ExpensiveComponent = ({ list }) => {
    // 仅当 list 变化时重新计算
    const sortedList = useMemo(
      () => list.sort((a, b) => a.value - b.value),
      [list]
    );

    // 缓存函数避免子组件不必要的重渲染
    const handleClick = useCallback(() => {
      console.log("Item clicked");
    }, []);

    return <Child list={sortedList} onClick={handleClick} />;
  };

  // Child 组件用 React.memo 避免无意义渲染
  const Child = React.memo(({ list, onClick }) => (
    <ul>
      {list.map((item) => (
        <li key={item.id} onClick={onClick}>
          {item.name}
        </li>
      ))}
    </ul>
  ));
  ```

---

#### **2. 虚拟化长列表**

- **策略**：使用 `react-window` 或 `react-virtualized` 渲染大型数据集。
- **案例**：渲染 10000 行数据的列表。

  ```jsx
  import { FixedSizeList as List } from "react-window";

  const BigList = ({ data }) => (
    <List height={600} itemCount={data.length} itemSize={35} width="100%">
      {({ index, style }) => <div style={style}>{data[index].name}</div>}
    </List>
  );
  ```

- **效果**：仅渲染可视区域元素，内存占用降低 90%+。

---

### 四、**代码可维护性策略**

#### **1. 目录结构按功能模块组织**

- **策略**：避免按技术角色（components、hooks）拆分，改为按业务功能划分。
  ```
  src/
    features/
      cart/
        components/  # Cart组件专属的UI
        hooks/       # 购物车相关逻辑
        types.ts     # 类型定义
        index.ts     # 统一导出
    shared/
      ui/           # 通用Button、Input等
      utils/        # 工具函数
  ```
- **优势**：修改功能时只需关注单一目录，减少文件跳转。

---

#### **2. 强制类型安全与文档化**

- **策略**：使用 TypeScript + JSDoc 注释关键逻辑。
- **案例**：定义组件 Props 类型和文档。

  ```tsx
  interface UserCardProps {
    /** 用户姓名 */
    name: string;
    /** 用户头像URL */
    avatar: string;
    /** 点击事件回调 */
    onClick?: () => void;
  }

  const UserCard: React.FC<UserCardProps> = ({ name, avatar, onClick }) => (
    <div onClick={onClick}>
      <img src={avatar} alt={name} />
      <h3>{name}</h3>
    </div>
  );
  ```

- **工具**：使用 Storybook 生成可视化文档。

---

### 五、**React 18/19 新特性实践**

#### **1. 并发模式优化用户体验**

- **策略**：使用 `useTransition` 标记非紧急更新。
- **案例**：搜索输入时延迟渲染结果。

  ```jsx
  const [query, setQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  const handleSearch = (e) => {
    const value = e.target.value;
    setQuery(value); // 紧急更新：立即显示输入值
    startTransition(() => {
      // 非紧急更新：延迟更新搜索结果
      setSearchResults(filterResults(value));
    });
  };

  return (
    <div>
      <input value={query} onChange={handleSearch} />
      {isPending ? <Spinner /> : <Results list={searchResults} />}
    </div>
  );
  ```

---

#### **2. React 19 服务端组件**

- **策略**：服务端组件直接获取数据，减少客户端 JS 体积。
- **案例**：服务端获取文章内容。

  ```jsx
  // Article.server.js - 服务端组件
  async function Article({ id }) {
    const article = await fetchArticleFromDB(id); // 直接访问数据库
    return (
      <article>
        <h1>{article.title}</h1>
        <p>{article.content}</p>
        <CommentSection /> {/* 客户端组件 */}
      </article>
    );
  }

  // CommentSection.client.js - 客户端组件
  function CommentSection() {
    const [comments, setComments] = useState([]);
    // 客户端获取动态数据
    useEffect(() => {
      fetchComments().then(setComments);
    }, []);
    return <div>{/* 渲染评论 */}</div>;
  }
  ```

---

### 六、**自动化测试策略**

#### **1. 使用 React Testing Library 测试组件行为**

- **策略**：测试用户交互而非实现细节。
- **案例**：测试登录表单提交。

  ```jsx
  test("提交表单后调用登录API", async () => {
    const mockLogin = jest.fn();
    render(<LoginForm onLogin={mockLogin} />);

    // 用户输入和点击
    userEvent.type(screen.getByLabelText("用户名"), "test");
    userEvent.type(screen.getByLabelText("密码"), "123456");
    userEvent.click(screen.getByRole("button", { name: "登录" }));

    // 验证是否调用正确的参数
    await waitFor(() => {
      expect(mockLogin).toHaveBeenCalledWith({
        username: "test",
        password: "123456",
      });
    });
  });
  ```

---

### 总结

高质量 React 代码的核心在于：

1. **组件设计**：原子化、职责分离
2. **状态管理**：合理分层，避免冗余
3. **性能优化**：记忆化、虚拟化、并发模式
4. **可维护性**：类型安全、模块化目录、自动化测试
5. **拥抱新特性**：服务端组件、Actions API 等

结合具体案例实践这些策略，能显著提升代码的可读性、性能和长期维护效率。
