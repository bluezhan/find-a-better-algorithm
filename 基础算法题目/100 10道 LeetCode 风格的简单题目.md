# LeetCode 风格简单题目及解答

以下是 10 道 LeetCode 风格的简单题目及其 JavaScript 解答，涵盖字符串操作、链表、数组处理、动态规划等高频考点。

---

## 1. 字符串中的第一个唯一字符

**题目**：找到字符串中第一个不重复的字符，返回其索引（不存在则返回 -1）。

**示例**：

```text
输入：s = "leetcode"
输出：0（'l' 是第一个唯一字符）
```

**解答**：

```javascript
const firstUniqChar = (s) => {
  const map = {};
  for (const char of s) {
    map[char] = (map[char] || 0) + 1;
  }
  for (let i = 0; i < s.length; i++) {
    if (map[s[i]] === 1) return i;
  }
  return -1;
};
```

---

## 2. 合并两个有序数组（变种）

**题目**：合并两个升序数组 `arr1` 和 `arr2`，结果仍为升序（直接返回新数组）。

**示例**：

```text
输入：arr1 = [1,3,5], arr2 = [2,4,6]
输出：[1,2,3,4,5,6]
```

**解答**：

```javascript
const mergeSortedArrays = (arr1, arr2) => {
  const merged = [];
  let i = 0,
    j = 0;
  while (i < arr1.length && j < arr2.length) {
    merged.push(arr1[i] < arr2[j] ? arr1[i++] : arr2[j++]);
  }
  return merged.concat(arr1.slice(i)).concat(arr2.slice(j));
};
```

---

## 3. 验证回文链表

**题目**：判断单链表是否为回文结构（进阶：时间 O(n)，空间 O(1)）。

**示例**：

```text
输入：1 → 2 → 2 → 1
输出：true
```

**解答**（快慢指针反转后半部分）：

```javascript
const isPalindromeLinkedList = (head) => {
  let slow = head,
    fast = head,
    prev = null;

  // 快慢指针找中点并反转前半部分
  while (fast && fast.next) {
    fast = fast.next.next;
    const next = slow.next;
    slow.next = prev;
    prev = slow;
    slow = next;
  }

  // 奇数节点时跳过中间节点
  if (fast) slow = slow.next;

  // 比较前后两半
  while (prev && slow) {
    if (prev.val !== slow.val) return false;
    prev = prev.next;
    slow = slow.next;
  }

  return true;
};
```

---

## 4. 二进制求和

**题目**：给定两个二进制字符串 `a` 和 `b`，返回它们的二进制和。

**示例**：

```text
输入：a = "1010", b = "1011"
输出："10101"
```

**解答**：

```javascript
const addBinary = (a, b) => {
  let carry = 0,
    result = "";
  let i = a.length - 1,
    j = b.length - 1;

  while (i >= 0 || j >= 0 || carry) {
    const sum = (+a[i--] || 0) + (+b[j--] || 0) + carry;
    result = (sum % 2) + result;
    carry = Math.floor(sum / 2);
  }

  return result;
};
```

---

## 5. 最后一个单词的长度

**题目**：返回字符串中最后一个单词的长度（单词由空格分隔）。

**示例**：

```text
输入：s = "Hello World "
输出：5
```

**解答**：

```javascript
const lengthOfLastWord = (s) => {
  let len = 0,
    i = s.length - 1;

  // 跳过末尾空格
  while (i >= 0 && s[i] === " ") i--;

  // 计算最后一个单词的长度
  while (i >= 0 && s[i--] !== " ") len++;

  return len;
};
```

---

## 6. 移动零

**题目**：将数组中的所有零移动到末尾，保持非零元素的相对顺序。

**示例**：

```text
输入：[0,1,0,3,12]
输出：[1,3,12,0,0]
```

**解答**：

```javascript
const moveZeroes = (nums) => {
  let nonZeroIndex = 0;

  // 将非零元素移动到前面
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] !== 0) nums[nonZeroIndex++] = nums[i];
  }

  // 填充零
  for (let i = nonZeroIndex; i < nums.length; i++) {
    nums[i] = 0;
  }
};
```

---

## 7. 同构字符串

**题目**：判断两个字符串 `s` 和 `t` 是否同构（字符映射需一一对应）。

**示例**：

```text
输入：s = "egg", t = "add"
输出：true
```

**解答**：

```javascript
const isIsomorphic = (s, t) => {
  const mapS = new Map();
  const mapT = new Map();

  for (let i = 0; i < s.length; i++) {
    if (mapS.get(s[i]) !== mapT.get(t[i])) return false;
    mapS.set(s[i], i);
    mapT.set(t[i], i);
  }

  return true;
};
```

---

## 8. 快乐数

**题目**：判断一个数是否为快乐数（反复替换为各位平方和，最终变为 1）。

**示例**：

```text
输入：19
输出：true
解释：1² + 9² = 82 → 8² + 2² = 68 → 6² + 8² = 100 → 1² + 0² + 0² = 1
```

**解答**（快慢指针检测循环）：

```javascript
const isHappy = (n) => {
  const getNext = (num) => {
    let sum = 0;
    while (num > 0) {
      const digit = num % 10;
      sum += digit * digit;
      num = Math.floor(num / 10);
    }
    return sum;
  };

  let slow = n,
    fast = getNext(n);
  while (fast !== 1 && slow !== fast) {
    slow = getNext(slow);
    fast = getNext(getNext(fast));
  }

  return fast === 1;
};
```

---

## 9. 反转链表

**题目**：反转单链表并返回新的头节点。

**示例**：

```text
输入：1 → 2 → 3 → 4 → null
输出：4 → 3 → 2 → 1 → null
```

**解答**：

```javascript
const reverseList = (head) => {
  let prev = null,
    curr = head;

  while (curr) {
    const next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }

  return prev;
};
```

---

## 10. 最大子序和

**题目**：找到整数数组中连续子数组的最大和。

**示例**：

```text
输入：[-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：子数组 [4,-1,2,1] 的和最大，为 6。
```

**解答**（贪心算法）：

```javascript
const maxSubArray = (nums) => {
  let max = nums[0],
    current = nums[0];

  for (let i = 1; i < nums.length; i++) {
    current = Math.max(nums[i], current + nums[i]);
    max = Math.max(max, current);
  }

  return max;
};
```

---

这些题目涵盖了字符串操作、链表、数组处理、动态规划等高频考点，适合巩固基础算法和编码能力。
