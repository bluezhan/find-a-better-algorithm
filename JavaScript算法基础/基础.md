# JavaScript 算法学习路径与核心知识点

以下是 JavaScript 算法的系统性学习路径与核心知识点整理，结合最新技术实践与算法理论：

## 一、基础算法与实现

### 排序算法

#### 冒泡排序

通过相邻元素比较交换实现排序，时间复杂度 O(n²)：

```javascript
function bubbleSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
    }
  }
  return arr;
}
```

#### 快速排序

分治策略实现，平均时间复杂度 O(n log n)：

```javascript
function quickSort(arr) {
  if (arr.length <= 1) return arr;
  const pivot = arr.pop();
  return [
    ...quickSort(arr.filter((x) => x < pivot)),
    pivot,
    ...quickSort(arr.filter((x) => x >= pivot)),
  ];
}
```

### 查找算法

#### 二分查找

要求有序数组，时间复杂度 O(log n)：

```javascript
function binarySearch(arr, target) {
  let left = 0,
    right = arr.length - 1;
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;
    arr[mid] < target ? (left = mid + 1) : (right = mid - 1);
  }
  return -1;
}
```

### 递归应用

#### 斐波那契数列

经典递归案例（需注意尾递归优化）：

```javascript
function fibonacci(n, a = 0, b = 1) {
  return n === 0 ? a : fibonacci(n - 1, b, a + b);
}
```

## 二、进阶算法与数据结构

### 时间复杂度控制

优先选择 O(1)、O(log n)、O(n) 复杂度算法，避免 O(n²) 及以上复杂度。

#### 示例场景对比

| 数据规模 | O(n)算法耗时 | O(n²)算法耗时 |
| -------- | ------------ | ------------- |
| 1,000    | 1ms          | 1s            |
| 10,000   | 10ms         | 100s          |

### 数据结构应用

- **链表实现 LRU 缓存**：结合 `Map` 实现 O(1) 访问与插入。
- **堆结构应用**：优先队列实现 Dijkstra 算法中的最短路径选择。

### 分治与动态规划

- **归并排序**：典型分治算法实现。
- **背包问题**：动态规划解决最优化问题。

## 三、现代 JavaScript 特性优化

### 内存管理机制

- 理解 V8 引擎的分代垃圾回收机制（新生代 Scavenge 算法与老生代标记清除算法）。
- 避免闭包滥用导致内存泄漏。

### ES6+特性应用

- 使用 `Map`/`Set` 替代传统对象实现高效查找。
- 利用展开运算符简化数组操作（如快速排序实现）。

## 四、学习资源推荐

- **系统性教材**：《学习 JavaScript 数据结构与算法（第 3 版）》完整覆盖基础到高阶内容。
- **可视化辅助**：腾讯云社区《图解 JavaScript 算法》帮助理解抽象概念。
- **算法训练平台**：LeetCode JavaScript 题库（结合 ES6 特性解题）。

通过以上路径，建议按「基础实现 → 复杂度分析 → 数据结构结合 → 真实场景应用」的顺序逐步深入，重点通过代码实践强化对递归、分治等核心思想的理解。
