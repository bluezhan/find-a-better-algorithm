# 高效地使用 TypeScript 编写算法

高效使用 TypeScript 编写算法需要结合类型系统的优势与算法设计的核心思想。以下是系统化的实践指南：

## 一、类型系统赋能算法设计

### 精准定义数据结构

```typescript
// 二叉树节点
interface TreeNode<T> {
  val: T;
  left: TreeNode<T> | null;
  right: TreeNode<T> | null;
}

// 图节点
type GraphNode<T> = {
  val: T;
  neighbors: GraphNode<T>[];
};
```

### 泛型提升复用性

```typescript
function binarySearch<T>(
  arr: T[],
  target: T,
  compare: (a: T, b: T) => number
): number {
  let left = 0,
    right = arr.length - 1;
  while (left <= right) {
    const mid = left + ((right - left) >> 1);
    const cmp = compare(arr[mid], target);
    if (cmp === 0) return mid;
    cmp < 0 ? (left = mid + 1) : (right = mid - 1);
  }
  return -1;
}
```

### 类型守卫优化逻辑

```typescript
function isMatrix(arr: unknown): arr is number[][] {
  return (
    Array.isArray(arr) &&
    arr.every((row) => Array.isArray(row) && row.every(Number.isFinite))
  );
}
```

## 二、算法实现最佳实践

### DFS/BFS 模板化

```typescript
// 矩阵DFS（方向向量）
const directions = [
  [-1, 0],
  [1, 0],
  [0, -1],
  [0, 1],
];
function dfs(matrix: number[][], i: number, j: number, visited: boolean[][]) {
  if (
    i < 0 ||
    j < 0 ||
    i >= matrix.length ||
    j >= matrix.length ||
    visited[i][j]
  )
    return;
  visited[i][j] = true;
  for (const [dx, dy] of directions) {
    dfs(matrix, i + dx, j + dy, visited);
  }
}
```

### 动态规划类型标注

```typescript
function knapsack(
  weights: number[],
  values: number[],
  capacity: number
): number {
  const dp: number[][] = Array.from({ length: weights.length + 1 }, () =>
    new Array(capacity + 1).fill(0)
  );

  for (let i = 1; i <= weights.length; i++) {
    for (let w = 1; w <= capacity; w++) {
      dp[i][w] =
        weights[i - 1] <= w
          ? Math.max(
              dp[i - 1][w],
              values[i - 1] + dp[i - 1][w - weights[i - 1]]
            )
          : dp[i - 1][w];
    }
  }
  return dp[weights.length][capacity];
}
```

### 回溯算法类型约束

```typescript
function permute<T>(nums: T[]): T[][] {
  const res: T[][] = [];
  const backtrack = (path: T[], used: boolean[]) => {
    if (path.length === nums.length) {
      res.push([...path]);
      return;
    }
    for (let i = 0; i < nums.length; i++) {
      if (used[i]) continue;
      used[i] = true;
      path.push(nums[i]);
      backtrack(path, used);
      path.pop();
      used[i] = false;
    }
  };
  backtrack([], new Array(nums.length).fill(false));
  return res;
}
```

## 三、性能优化技巧

### 记忆化搜索类型声明

```typescript
const memo = new Map<string, number>();
function fib(n: number): number {
  if (n <= 1) return n;
  const key = `${n}`;
  if (memo.has(key)) return memo.get(key)!;
  const res = fib(n - 1) + fib(n - 2);
  memo.set(key, res);
  return res;
}
```

### 堆/优先队列实现

```typescript
class PriorityQueue<T> {
  private heap: T[];
  constructor(private compare: (a: T, b: T) => number) {
    this.heap = [];
  }
  push(val: T) {
    this.heap.push(val);
    this.bubbleUp();
  }
  private bubbleUp() {
    let idx = this.heap.length - 1;
    while (idx > 0) {
      const parent = Math.floor((idx - 1) / 2);
      if (this.compare(this.heap[idx], this.heap[parent]) >= 0) break;
      [this.heap[idx], this.heap[parent]] = [this.heap[parent], this.heap[idx]];
      idx = parent;
    }
  }
}
```

## 四、工程化建议

### 单元测试类型校验

```typescript
// 使用jest测试排序算法
describe("quickSort", () => {
  it("should sort number array", () => {
    const arr: number[] = [3, 1, 4, 2];
    const result = quickSort(arr);
    expect(result).toEqual([1, 2, 3, 4]);
    // 类型断言
    expectType<number[]>(result);
  });
});
```

### 算法复杂度注释

```typescript
/**
 * 双指针法解决两数之和
 * 时间复杂度：O(n)
 * 空间复杂度：O(1)
 */
function twoSum(nums: number[], target: number): [number, number] {
  let left = 0,
    right = nums.length - 1;
  while (left < right) {
    const sum = nums[left] + nums[right];
    if (sum === target) return [left, right];
    sum < target ? left++ : right--;
  }
  return [-1, -1];
}
```

## 五、常见问题解决方案

### 大数处理（使用 bigint）

```typescript
function bigNumberAdd(a: string, b: string): string {
  return (BigInt(a) + BigInt(b)).toString();
}
```

### 矩阵运算类型安全

```typescript
type Matrix = number[][];
function matrixMultiply(a: Matrix, b: Matrix): Matrix {
  if (a.length !== b.length) throw new Error("Dimension mismatch");
  const res: Matrix = Array(a.length)
    .fill(0)
    .map(() => Array(b.length).fill(0));
  // ...乘法实现
  return res;
}
```

通过结合 TypeScript 的类型系统与算法设计模式，可以获得以下优势：

- 在编码阶段捕获数组越界、类型不匹配等错误
- 通过泛型实现算法复用，避免重复代码
- 类型注释作为文档，提升代码可维护性
- 编译器优化帮助识别性能热点（如不必要的类型转换）

建议配合 VS Code 的 TypeScript 插件实时检查类型错误，并使用 `ts-node` 快速验证算法实现。
