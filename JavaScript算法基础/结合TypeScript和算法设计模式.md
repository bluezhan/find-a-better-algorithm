# 结合 TypeScript 和算法设计模式

结合 TypeScript 的静态类型特性与算法设计模式，可通过以下方式实现高效且类型安全的代码设计：

## 一、策略模式（Strategy Pattern）

**应用场景**：动态切换算法实现（如排序、搜索等）  
**TypeScript 实现**：

```typescript
interface SortStrategy<T> {
  sort(arr: T[]): T[];
}

class QuickSort<T> implements SortStrategy<T> {
  sort(arr: T[]): T[] {
    // 快速排序实现
    return [...arr].sort((a, b) => (a > b ? 1 : -1));
  }
}

class Context<T> {
  constructor(private strategy: SortStrategy<T>) {}
  executeSort(arr: T[]): T[] {
    return this.strategy.sort(arr);
  }
}

// 使用
const ctx = new Context<number>(new QuickSort());
ctx.executeSort([3, 1, 2]); // [1, 2, 3]
```

**优势**：通过接口强制实现类遵守算法契约，泛型支持多数据类型。

---

## 二、模板方法模式（Template Method）

**应用场景**：定义算法骨架，子类实现具体步骤（如 DFS/BFS 框架）  
**TypeScript 实现**：

```typescript
abstract class GraphTraversal {
  abstract visit(node: GraphNode): void;

  traverse(root: GraphNode) {
    const visited = new Set<GraphNode>();
    this._traverse(root, visited);
  }

  private _traverse(node: GraphNode, visited: Set<GraphNode>) {
    if (visited.has(node)) return;
    visited.add(node);
    this.visit(node); // 由子类实现具体操作
    node.neighbors.forEach((neighbor) => this._traverse(neighbor, visited));
  }
}

class PrintTraversal extends GraphTraversal {
  visit(node: GraphNode) {
    console.log(node.val);
  }
}
```

**特点**：抽象类保证算法结构稳定，子类通过方法覆盖扩展行为。

---

## 三、装饰器模式（Decorator）

**应用场景**：动态增强算法功能（如缓存、日志）  
**TypeScript 实现**：

```typescript
function memoize<T extends (...args: any[]) => any>(fn: T): T {
  const cache = new Map<string, ReturnType<T>>();
  return function (this: any, ...args: Parameters<T>): ReturnType<T> {
    const key = JSON.stringify(args);
    if (!cache.has(key)) cache.set(key, fn.apply(this, args));
    return cache.get(key)!;
  } as T;
}

// 使用
const fibonacci = memoize((n: number): number =>
  n <= 1 ? n : fibonacci(n - 1) + fibonacci(n - 2)
);
```

**优势**：通过高阶函数和泛型实现类型安全的装饰器。

---

## 四、工厂模式（Factory）

**应用场景**：创建复杂算法对象（如不同树结构的生成）  
**TypeScript 实现**：

```typescript
interface TreeFactory<T> {
  createNode(val: T): TreeNode<T>;
}

class BinaryTreeFactory<T> implements TreeFactory<T> {
  createNode(val: T): TreeNode<T> {
    return { val, left: null, right: null };
  }
}

class AVLTreeFactory<T> extends BinaryTreeFactory<T> {
  createNode(val: T): TreeNode<T> {
    return { ...super.createNode(val), height: 1 };
  }
}
```

**特点**：通过工厂接口统一创建逻辑，子类定制化实现。

---

## 五、观察者模式（Observer）

**应用场景**：算法状态变更通知（如排序进度更新）  
**TypeScript 实现**：

```typescript
interface SortObserver<T> {
  onProgress(arr: T[]): void;
}

class Sorter<T> {
  private observers: SortObserver<T>[] = [];

  addObserver(observer: SortObserver<T>) {
    this.observers.push(observer);
  }

  async bubbleSort(arr: T[]) {
    for (let i = 0; i < arr.length; i++) {
      await this.notify(arr); // 模拟进度更新
      // ...排序逻辑
    }
  }

  private async notify(arr: T[]) {
    this.observers.forEach((obs) => obs.onProgress([...arr]));
  }
}
```

**优势**：利用 TypeScript 接口确保观察者方法签名一致。

---

## 最佳实践建议

- **类型优先设计**：使用 `interface`/`type` 明确定义算法契约。
- **泛型扩展性**：通过 `<T>` 支持多数据类型算法。
- **严格模式启用**：在 `tsconfig.json` 中设置 `"strict": true` 捕获类型错误。
- **避免 `any` 类型**：为算法参数和返回值指定具体类型。
- **装饰器优化**：利用 TS 装饰器增强算法功能而不修改原逻辑。

通过结合设计模式与 TypeScript 类型系统，可构建出既灵活又安全的算法实现方案。
