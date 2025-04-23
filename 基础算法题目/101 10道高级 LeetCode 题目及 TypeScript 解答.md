# 高级 LeetCode 题目及 TypeScript 解答

以下是 10 道难度稍高的 LeetCode 风格题目及其 TypeScript 解答，涵盖树、回溯、双指针等进阶主题：

## 1. 二叉树的锯齿形层序遍历

**题目**：返回二叉树的节点值的锯齿形层序遍历（先从左到右，下一层从右到左，交替进行）。

**示例**：

```text
输入：
  3
   / \
  9  20
  /  \
   15   7
输出：[[3], [20, 9], [15, 7]]
```

**TypeScript 解答**：

```typescript
interface TreeNode {
  val: number;
  left: TreeNode | null;
  right: TreeNode | null;
}

const zigzagLevelOrder = (root: TreeNode | null): number[][] => {
  if (!root) return [];
  const result: number[][] = [];
  const queue: TreeNode[] = [root];
  let reverse = false;

  while (queue.length) {
    const levelSize = queue.length;
    const level: number[] = [];
    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift()!;
      level.push(node.val);
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
    result.push(reverse ? level.reverse() : level);
    reverse = !reverse;
  }
  return result;
};
```

---

## 2. 从前序与中序遍历序列构造二叉树

**题目**：根据前序和中序遍历结果构造二叉树（假设无重复元素）。

**示例**：

```text
输入：preorder = [3, 9, 20, 15, 7], inorder = [9, 3, 15, 20, 7]
输出：树结构同第 1 题
```

**TypeScript 解答**：

```typescript
const buildTree = (preorder: number[], inorder: number[]): TreeNode | null => {
  const map = new Map<number, number>();
  inorder.forEach((val, idx) => map.set(val, idx));

  const build = (
    preStart: number,
    inStart: number,
    inEnd: number
  ): TreeNode | null => {
    if (preStart >= preorder.length || inStart > inEnd) return null;

    const rootVal = preorder[preStart];
    const rootIdx = map.get(rootVal)!;
    const root: TreeNode = { val: rootVal, left: null, right: null };

    const leftSize = rootIdx - inStart;
    root.left = build(preStart + 1, inStart, rootIdx - 1);
    root.right = build(preStart + 1 + leftSize, rootIdx + 1, inEnd);

    return root;
  };

  return build(0, 0, inorder.length - 1);
};
```

---

## 3. 组合总和

**题目**：给定无重复元素的数组 `candidates` 和目标数 `target`，找出所有和为 `target` 的组合（可重复使用元素）。

**示例**：

```text
输入：candidates = [2, 3, 6, 7], target = 7
输出：[[2, 2, 3], [7]]
```

**TypeScript 解答**：

```typescript
const combinationSum = (candidates: number[], target: number): number[][] => {
  const result: number[][] = [];

  const backtrack = (start: number, path: number[], sum: number) => {
    if (sum === target) {
      result.push([...path]);
      return;
    }
    if (sum > target) return;

    for (let i = start; i < candidates.length; i++) {
      path.push(candidates[i]);
      backtrack(i, path, sum + candidates[i]);
      path.pop();
    }
  };

  backtrack(0, [], 0);
  return result;
};
```

---

## 4. 旋转图像

**题目**：将 `n×n` 矩阵顺时针旋转 90 度（必须原地修改）。

**示例**：

```text
输入：
[[1, 2, 3],
 [4, 5, 6],
 [7, 8, 9]]
输出：
[[7, 4, 1],
 [8, 5, 2],
 [9, 6, 3]]
```

**TypeScript 解答**：

```typescript
const rotate = (matrix: number[][]): void => {
  const n = matrix.length;
  // 先转置矩阵
  for (let i = 0; i < n; i++) {
    for (let j = i; j < n; j++) {
      [matrix[i][j], matrix[j][i]] = [matrix[j][i], matrix[i][j]];
    }
  }
  // 再翻转每一行
  matrix.forEach((row) => row.reverse());
};
```

---

## 5. 岛屿数量

**题目**：给定由 `'1'`（陆地）和 `'0'`（水）组成的二维网格，计算岛屿的数量（相连的 `1` 视为一个岛屿）。

**示例**：

```text
输入：
11110
11010
11000
00001
输出：2
```

**TypeScript 解答**：

```typescript
const numIslands = (grid: string[][]): number => {
  if (!grid.length) return 0;
  const rows = grid.length,
    cols = grid[0].length;
  let count = 0;

  const dfs = (i: number, j: number): void => {
    if (i < 0 || j < 0 || i >= rows || j >= cols || grid[i][j] !== "1") return;
    grid[i][j] = "0"; // 标记为已访问
    dfs(i + 1, j);
    dfs(i - 1, j);
    dfs(i, j + 1);
    dfs(i, j - 1);
  };

  for (let i = 0; i < rows; i++) {
    for (let j = 0; j < cols; j++) {
      if (grid[i][j] === "1") {
        dfs(i, j);
        count++;
      }
    }
  }
  return count;
};
```

---

## 6. 最长递增子序列

**题目**：找到数组中最长严格递增子序列的长度。

**示例**：

```text
输入：nums = [10, 9, 2, 5, 3, 7, 101, 18]
输出：4（序列为 [2, 3, 7, 101]）
```

**TypeScript 解答**：

```typescript
const lengthOfLIS = (nums: number[]): number => {
  const dp: number[] = new Array(nums.length).fill(1);
  let max = 1;

  for (let i = 1; i < nums.length; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[i] > nums[j]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
        max = Math.max(max, dp[i]);
      }
    }
  }

  return max;
};
```

---

## 7. 验证二叉搜索树

**题目**：判断二叉树是否是有效的二叉搜索树（左子树所有节点小于根，右子树所有节点大于根）。

**示例**：

```text
输入：
    5
   / \
  1   4
     / \
    3   6
输出：false（4 的右子树 3 小于根 5）
```

**TypeScript 解答**：

```typescript
const isValidBST = (root: TreeNode | null): boolean => {
  const validate = (
    node: TreeNode | null,
    lower: number = -Infinity,
    upper: number = Infinity
  ): boolean => {
    if (!node) return true;
    if (node.val <= lower || node.val >= upper) return false;
    return (
      validate(node.left, lower, node.val) &&
      validate(node.right, node.val, upper)
    );
  };

  return validate(root);
};
```

---

## 8. 括号生成

**题目**：生成所有包含 `n` 对括号的有效组合。

**示例**：

```text
输入：n = 3
输出：["((()))", "(()())", "(())()", "()(())", "()()()"]
```

**TypeScript 解答**：

```typescript
const generateParenthesis = (n: number): string[] => {
  const result: string[] = [];

  const backtrack = (s: string, open: number, close: number) => {
    if (s.length === 2 * n) {
      result.push(s);
      return;
    }
    if (open < n) backtrack(s + "(", open + 1, close);
    if (close < open) backtrack(s + ")", open, close + 1);
  };

  backtrack("", 0, 0);
  return result;
};
```

---

## 9. 环形链表 II

**题目**：返回链表中环的起始节点，若无环返回 `null`（要求空间复杂度为 O(1)）。

**示例**：

```text
输入：head = 3 → 2 → 0 → -4（尾节点连接到索引 1 的节点）
输出：节点 2
```

**TypeScript 解答**：

```typescript
interface ListNode {
  val: number;
  next: ListNode | null;
}

const detectCycle = (head: ListNode | null): ListNode | null => {
  let slow = head,
    fast = head;

  while (fast?.next) {
    slow = slow!.next;
    fast = fast.next.next;

    if (slow === fast) {
      let ptr = head;
      while (ptr !== slow) {
        ptr = ptr!.next;
        slow = slow!.next;
      }
      return ptr;
    }
  }

  return null;
};
```

---

## 10. 最小路径和

**题目**：在 `m×n` 的网格中找从左上角到右下角的路径，使得路径上的数字总和最小（每次只能向下或向右移动）。

**示例**：

```text
输入：
[[1, 3, 1],
 [1, 5, 1],
 [4, 2, 1]]
输出：7（路径 1 → 3 → 1 → 1 → 1）
```

**TypeScript 解答**：

```typescript
const minPathSum = (grid: number[][]): number => {
  const m = grid.length,
    n = grid.length;
  const dp: number[][] = Array.from({ length: m }, () => new Array(n).fill(0));
  dp = grid;

  for (let i = 1; i < m; i++) dp[i] = dp[i - 1] + grid[i];
  for (let j = 1; j < n; j++) dp[j] = dp[j - 1] + grid[j];

  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
    }
  }
  return dp[m - 1][n - 1];
};
```

---

这些题目涉及动态规划、树操作、回溯算法等进阶技巧，且全部使用 TypeScript 类型系统强化代码安全性，适合提升对算法和类型系统的综合运用能力。
