### React 中使用数据不可变性优化渲染的案例

---

#### **核心原理**

数据不可变性（Immutable Data）通过确保每次数据修改都返回 **新对象**，帮助 React 快速判断数据是否变化，避免不必要的渲染。以下通过 `Immutable.js` 和 `immer` 分别给出实际案例。

---

### 案例 1：使用 `Immutable.js` 优化深层嵌套数据

#### **场景**

渲染一个大型列表，列表中每个元素包含多层嵌套属性（如用户信息、订单记录）。

```jsx
import React, { useState } from "react";
import { List, Map } from "immutable";

// 初始化不可变数据
const initialData = List([
  Map({ id: 1, name: "Alice", orders: List([{ product: "Book", price: 20 }]) }),
  Map({ id: 2, name: "Bob", orders: List([{ product: "Pen", price: 5 }]) }),
]);

function UserList() {
  const [users, setUsers] = useState(initialData);

  // 更新某个用户的订单（深层次修改）
  const updateOrder = (userId, newOrder) => {
    setUsers((prevUsers) =>
      prevUsers.map((user) =>
        user.get("id") === userId
          ? user.update("orders", (orders) => orders.push(Map(newOrder)))
          : user
      )
    );
  };

  // 使用 React.memo + Immutable.js 防止子组件无效渲染
  const UserItem = React.memo(({ user }) => {
    console.log("UserItem rendered:", user.get("id"));
    return (
      <div>
        <h3>{user.get("name")}</h3>
        <ul>
          {user.get("orders").map((order, index) => (
            <li key={index}>
              {order.get("product")} - ${order.get("price")}
            </li>
          ))}
        </ul>
      </div>
    );
  });

  return (
    <div>
      {users.map((user) => (
        <UserItem key={user.get("id")} user={user} />
      ))}
      <button
        onClick={() => updateOrder(1, { product: "Laptop", price: 1000 })}
      >
        Add Order for Alice
      </button>
    </div>
  );
}
```

**优化效果**：

- 只有被修改的用户项会触发重新渲染（其他用户保持引用不变）。
- `React.memo` 结合不可变数据，通过浅比较快速跳过未变化的组件。

---

### 案例 2：使用 `immer` 简化不可变更新逻辑

#### **场景**

管理一个包含复杂状态的表单（多级对象、数组操作）。

```jsx
import React, { useCallback } from "react";
import { produce } from "immer";

function Form() {
  const [formData, setFormData] = useState({
    title: "",
    author: { name: "", email: "" },
    tags: ["react", "immer"],
  });

  // 使用 immer 的 produce 函数更新嵌套对象
  const handleNameChange = useCallback((newName) => {
    setFormData(
      produce((draft) => {
        draft.author.name = newName; // 直接修改 draft，无需手动展开
      })
    );
  }, []);

  // 使用 immer 更新数组
  const addTag = useCallback((newTag) => {
    setFormData(
      produce((draft) => {
        draft.tags.push(newTag);
      })
    );
  }, []);

  return (
    <div>
      <input
        value={formData.author.name}
        onChange={(e) => handleNameChange(e.target.value)}
      />
      <ul>
        {formData.tags.map((tag, index) => (
          <li key={index}>{tag}</li>
        ))}
      </ul>
      <button onClick={() => addTag("new-tag")}>Add Tag</button>
    </div>
  );
}
```

**优化效果**：

- 语法简洁：直接修改 `draft` 对象，`immer` 自动生成新状态。
- 避免手动使用 `...` 展开运算符，减少代码错误。

---

### 案例 3：结合 `useReducer` 和 `immer` 管理复杂状态

```jsx
import React, { useReducer } from "react";
import { produce } from "immer";

const initialState = {
  items: [],
  filter: { category: "all", sortBy: "date" },
};

function reducer(state, action) {
  return produce(state, (draft) => {
    switch (action.type) {
      case "ADD_ITEM":
        draft.items.push(action.item);
        break;
      case "UPDATE_FILTER":
        draft.filter = { ...draft.filter, ...action.payload };
        break;
    }
  });
}

function TodoApp() {
  const [state, dispatch] = useReducer(reducer, initialState);

  // 使用 immer 后，dispatch 操作更直观
  const addItem = () => {
    dispatch({ type: "ADD_ITEM", item: { id: Date.now(), text: "New Item" } });
  };

  return (
    <div>
      <button onClick={addItem}>Add Item</button>
      <div>{state.items.length} items</div>
    </div>
  );
}
```

---

### **如何选择工具？**

| 场景                | 推荐工具                  | 原因                                     |
| ------------------- | ------------------------- | ---------------------------------------- |
| 超大数据量/高频更新 | `Immutable.js`            | 持久化数据结构优化性能，适合深层嵌套数据 |
| 常规表单/中等复杂度 | `immer`                   | 语法简单，接近原生 JS，开发效率高        |
| Redux 状态管理      | `immer` + `redux-toolkit` | 官方集成，简化 Reducer 逻辑              |

---

### **关键实践原则**

1. **避免直接修改状态**：始终通过 `setState` 或 `dispatch` 更新。
2. **隔离可变操作**：将数据修改逻辑集中在 `produce` 或 `Immutable.js` 的 API 中。
3. **结合性能优化 API**：
   - 使用 `React.memo` + 不可变数据跳过子组件渲染。
   - 使用 `useMemo` 缓存计算结果，依赖项为不可变数据。

通过强制数据不可变性，React 应用可以更精准地控制渲染，显著提升大规模数据场景下的性能。
