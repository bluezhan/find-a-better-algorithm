以下是 15 道偏向 React 技术的前端面试题及详细答案：

---

### **1. React 类组件和函数组件的区别是什么？Hooks 解决了哪些问题？**

**答案：**

- **类组件**：使用 ES6 class 定义，有生命周期方法、状态管理（this.state）和实例方法。
- **函数组件**：纯函数形式，无状态和生命周期（Hooks 出现前），代码更简洁。
- **Hooks 的作用**：
  - 允许函数组件使用状态（useState）和生命周期（useEffect）。
  - 解决类组件逻辑复用困难（如 HOC 和 Render Props 的嵌套问题）。
  - 避免类组件中 this 绑定问题，简化代码结构。

---

### **2. 什么是虚拟 DOM（Virtual DOM）？它如何提升性能？**

**答案：**

- **虚拟 DOM**：是真实 DOM 的轻量级 JavaScript 对象表示。
- **工作原理**：
  1. 状态变化时生成新虚拟 DOM。
  2. 通过 Diff 算法对比新旧虚拟 DOM 的差异。
  3. 仅更新真实 DOM 中需要修改的部分。
- **性能提升**：减少直接操作真实 DOM 的次数，批量更新避免重绘/重排。

---

### **3. React 中 key 的作用是什么？为什么不要用索引（index）作为 key？**

**答案：**

- **key 的作用**：帮助 React 识别列表中元素的唯一性，优化 Diff 算法效率。
- **避免使用索引**：当列表顺序变化（如新增/删除项）时，索引会改变，导致 React 误判元素是否需要复用，可能引发渲染错误或性能问题。

---

### **4. 什么是受控组件（Controlled Component）和非受控组件（Uncontrolled Component）？**

**答案：**

- **受控组件**：表单数据由 React 状态控制（如通过`value`和`onChange`绑定）。
  ```jsx
  <input value={value} onChange={(e) => setValue(e.target.value)} />
  ```
- **非受控组件**：表单数据由 DOM 自身管理（通过`ref`直接获取值）。
  ```jsx
  const inputRef = useRef();
  <input ref={inputRef} defaultValue="初始值" />;
  ```

---

### **5. 解释 React 的 Context API 及其使用场景。**

**答案：**

- **Context API**：用于跨层级组件传递数据，避免逐层 props 传递。
- **使用步骤**：
  1. 创建 Context：`const MyContext = React.createContext(defaultValue);`
  2. 提供数据：`<MyContext.Provider value={value}>`
  3. 消费数据：`useContext(MyContext)`或`<MyContext.Consumer>`
- **场景**：主题切换、用户登录状态、多语言等全局数据共享。

---

### **6. React Hooks 中的 useEffect 有哪些使用注意事项？**

**答案：**

- **依赖数组**：第二个参数传入依赖项数组，空数组表示仅在挂载/卸载时执行。
- **清理函数**：返回一个函数用于清理副作用（如取消订阅、清除定时器）。
- **避免无限循环**：确保依赖项正确，避免在 useEffect 内部修改依赖项导致重复执行。

---

### **7. React 如何实现代码分割（Code Splitting）？**

**答案：**

- **React.lazy + Suspense**：
  ```jsx
  const LazyComponent = React.lazy(() => import("./LazyComponent"));
  function App() {
    return (
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    );
  }
  ```
- **动态 import()语法**：结合 Webpack 等打包工具自动分割代码。

---

### **8. 解释 React 中的高阶组件（HOC）及其常见用途。**

**答案：**

- **HOC**：接收组件作为参数并返回新组件的函数，用于逻辑复用。
- **示例**：权限控制、日志记录、样式注入。
  ```jsx
  function withLogging(WrappedComponent) {
    return function (props) {
      useEffect(() => {
        console.log("Component mounted");
      }, []);
      return <WrappedComponent {...props} />;
    };
  }
  ```

---

### **9. React 中的错误边界（Error Boundaries）是什么？如何实现？**

**答案：**

- **错误边界**：捕获子组件树中的 JavaScript 错误并展示降级 UI。
- **实现方式**：使用生命周期方法`static getDerivedStateFromError`和`componentDidCatch`。
  ```jsx
  class ErrorBoundary extends Component {
    state = { hasError: false };
    static getDerivedStateFromError() {
      return { hasError: true };
    }
    componentDidCatch(error, info) {
      logError(error, info);
    }
    render() {
      return this.state.hasError ? <FallbackUI /> : this.props.children;
    }
  }
  ```

---

### **10. React 性能优化有哪些常见手段？**

**答案：**

- **React.memo**：缓存函数组件，避免不必要的渲染。
- **useMemo/useCallback**：缓存值和函数，减少重复计算。
- **PureComponent**：类组件中自动实现浅比较的 shouldComponentUpdate。
- **虚拟化长列表**：使用 react-window 或 react-virtualized。
- **生产模式构建**：移除开发模式警告和调试代码。

---

### **11. 解释 React Fiber 架构的核心目标。**

**答案：**

- **目标**：实现增量渲染（Incremental Rendering），将渲染任务拆分为小任务块，可中断和恢复。
- **优势**：解决同步渲染导致的卡顿问题，支持优先级调度（如动画优先）。

---

### **12. 什么是 React 合成事件（Synthetic Event）？**

**答案：**

- **合成事件**：React 对浏览器原生事件的跨浏览器包装，提供统一 API。
- **特点**：
  - 事件委托到根容器，减少内存占用。
  - 自动处理事件池（event pooling），需用`e.persist()`保留事件引用。

---

### **13. Redux 的核心原则是什么？如何在 React 中连接 Redux？**

**答案：**

- **核心原则**：
  - 单一数据源（Store）。
  - 状态只读（通过 Action 修改）。
  - 纯函数 Reducer 处理状态变更。
- **连接 React**：
  - 使用`react-redux`库的`Provider`包裹根组件。
  - 通过`useSelector`和`useDispatch`访问状态和派发 Action。

---

### **14. React Router 的实现原理是什么？**

**答案：**

- **原理**：基于浏览器 History API（pushState/replaceState）或 Hash 模式。
- **核心组件**：
  - `<BrowserRouter>`：使用 HTML5 History API。
  - `<Route>`：定义路径和组件的映射。
  - `<Link>`：导航链接，阻止默认跳转行为。
  - `useNavigate`：编程式导航。

---

### **15. React Server Components（RSC）是什么？解决了什么问题？**

**答案：**

- **定义**：在服务端渲染的组件，可直接访问后端资源（如数据库），不发送客户端 JS 代码。
- **优势**：
  - 减少客户端包体积。
  - 提升首屏加载速度。
  - 支持服务端直接获取数据，避免客户端请求链。
- **限制**：不能使用状态和生命周期，仅用于静态内容渲染。

---

以上问题覆盖了 React 的核心概念、性能优化、状态管理及生态工具，适合中高级前端面试考察。
