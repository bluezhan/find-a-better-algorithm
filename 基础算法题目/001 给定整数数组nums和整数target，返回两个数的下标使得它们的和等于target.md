# 两数之和

‌ **题目** ‌：给定整数数组 nums 和整数 target，返回两个数的下标使得它们的和等于 target。  
**示例**：

```text
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
```

‌ **答案** ‌：

```javascript
const twoSum = (nums, target) => {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const complement = target - nums[i];
    if (map.has(complement)) return [map.get(complement), i];
    map.set(nums[i], i);
  }
};
```

这里是设置一个空的`Map`, 然后对比是否存在符合的数字，而不是一开始就是全部放进去。
