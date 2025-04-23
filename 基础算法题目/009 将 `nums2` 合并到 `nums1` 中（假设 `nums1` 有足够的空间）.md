# 9. 合并两个有序数组

**题目**：将 `nums2` 合并到 `nums1` 中（假设 `nums1` 有足够的空间）。

**示例**：

```text
输入：nums1 = [1,2,3,0,0,0], m = 3; nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```

**解答**：

```javascript
const merge = (nums1, m, nums2, n) => {
  let p1 = m - 1,
    p2 = n - 1,
    p = m + n - 1;
  while (p2 >= 0) {
    nums1[p--] = p1 >= 0 && nums1[p1] > nums2[p2] ? nums1[p1--] : nums2[p2--];
  }
};
```
