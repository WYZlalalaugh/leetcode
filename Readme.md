# LeetCode Hot 100 · Python 题解

> 记录 Hot 100 的 Python 解法与思路，持续整理中。

---

## 目录

- [一、哈希](#一哈希)
  - [1. 字母异位词分组](#1-字母异位词分组)
  - [128. 最长连续序列](#128-最长连续序列)
- [二、双指针](#二双指针)
  - [11. 盛最多水的容器](#11-盛最多水的容器)
  - [15. 三数之和](#15-三数之和)
  - [283. 移动零](#283-移动零)
  - [42. 接雨水](#42-接雨水)
- [三、滑动窗口](#三滑动窗口)
  - [3. 无重复字符的最长子串](#3-无重复字符的最长子串)
  - [438. 找到字符串中所有字母异位词](#438-找到字符串中所有字母异位词)
- [四、前缀和](#四前缀和)
  - [560. 和为 K 的子数组](#560-和为-k-的子数组)

---

# 一、哈希

> 这一类题主要依赖哈希表 / 哈希集合来快速查找、去重和分组。

## 1. 字母异位词分组

### 题目描述

给你一个字符串数组，请你将 **字母异位词** 组合在一起。你可以按任意顺序返回结果列表。

字母异位词是由重新排列源单词的字母得到的新单词。

### 示例

**示例 1**

输入：

```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```

输出：

```python
[["bat"], ["nat", "tan"], ["ate", "eat", "tea"]]
```

解释：

- `strs` 中没有字符串可以通过重新排列变成 `bat`
- `nat` 和 `tan` 是字母异位词，因为它们可以互相重新排列
- `ate`、`eat` 和 `tea` 也是字母异位词

**示例 2**

输入：

```python
strs = [""]
```

输出：

```python
[[""]]
```

**示例 3**

输入：

```python
strs = ["a"]
```

输出：

```python
[["a"]]
```

### 提示

- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母

### 题解

#### 思路

使用哈希表分组，把排序后的字符串作为哈希表的 `key`，原字符串放进对应的列表中作为 `value`。

最后返回哈希表中的所有分组即可。

#### 代码

```python
from collections import defaultdict
from typing import List


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        groups = defaultdict(list)
        for s in strs:
            key = ''.join(sorted(s))
            groups[key].append(s)
        return list(groups.values())
```

#### 复杂度分析

- **时间复杂度：** `O(n × k log k)`，其中 `n` 是字符串数量，`k` 是字符串最大长度。需要对每个字符串排序。
- **空间复杂度：** `O(n × k)`，用于存储哈希表。

## 128. 最长连续序列

### 题目描述

给定一个未排序的整数数组 `nums`，找出数字连续的最长序列的长度。

这里的连续序列不要求元素在原数组中连续。

请你设计并实现时间复杂度为 **O(n)** 的算法解决此问题。

### 示例

**示例 1**

输入：

```python
nums = [100,4,200,1,3,2]
```

输出：

```python
4
```

解释：最长数字连续序列是 `[1, 2, 3, 4]`，长度为 `4`。

**示例 2**

输入：

```python
nums = [0,3,7,2,5,8,4,6,0,1]
```

输出：

```python
9
```

**示例 3**

输入：

```python
nums = [1,0,1,2]
```

输出：

```python
3
```

### 提示

- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`

### 题解

#### 思路

这题不能排序，因为排序的时间复杂度是 `O(n log n)`，不符合题目要求。

核心思路：

1. 先把 `nums` 中的数字放入哈希集合，这样可以 `O(1)` 判断某个数字是否存在。
2. 只从连续序列的起点开始向后扩展。

如果 `x - 1` 也在集合中，就说明 `x` 不是起点，直接跳过，这样可以避免重复计算。

> **注意：** 遍历时要遍历哈希集合，而不是原数组。否则在大量重复元素的情况下，可能退化为 `O(n^2)`。

#### 代码

```python
from typing import List


class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        st = set(nums)
        ans = 0
        for x in st:
            if x - 1 in st:
                continue
            y = x + 1
            while y in st:
                y += 1
            ans = max(ans, y - x)
        return ans
```

#### 复杂度分析

- **时间复杂度：** `O(n)`，每个元素最多被访问常数次。
- **空间复杂度：** `O(n)`，用于存储哈希集合。

---

# 二、双指针

> 这一类题通常通过左右指针配合，完成原地修改、区间扫描或有序处理。

## 11. 盛最多水的容器

### 题目描述

给定一个长度为 `n` 的整数数组 `height`。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])`。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。

### 示例

**示例 1**

输入：

```python
height = [1,8,6,2,5,4,8,3,7]
```

输出：

```python
49
```

解释：图中垂直线代表输入数组 `height`，此情况下容器能够容纳的最大值为 `49`。

**示例 2**

输入：

```python
height = [1,1]
```

输出：

```python
1
```

### 提示

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

### 题解

#### 思路

使用双指针，分别从数组两端向中间收缩。

当前容积由两部分决定：

- 两侧较短的那条线
- 两个指针之间的距离

因此，每次都移动较短的那一侧，才有机会得到更大的面积。

#### 代码

```python
from typing import List


class Solution:
    def maxArea(self, height: List[int]) -> int:
        l, r = 0, len(height) - 1
        ans = 0
        while l < r:
            area = min(height[l], height[r]) * (r - l)
            ans = max(ans, area)
            if height[l] <= height[r]:
                l += 1
            else:
                r -= 1
        return ans
```

#### 复杂度分析

- **时间复杂度：** `O(n)`
- **空间复杂度：** `O(1)`

## 15. 三数之和

### 题目描述

给你一个整数数组 `nums`，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k`，同时还满足 `nums[i] + nums[j] + nums[k] == 0`。

请你返回所有和为 `0` 且不重复的三元组。

### 示例

**示例 1**

输入：

```python
nums = [-1,0,1,2,-1,-4]
```

输出：

```python
[[-1,-1,2],[-1,0,1]]
```

解释：

- `nums[0] + nums[1] + nums[2] = 0`
- `nums[1] + nums[2] + nums[4] = 0`
- `nums[0] + nums[3] + nums[4] = 0`

不同的三元组是 `[-1,0,1]` 和 `[-1,-1,2]`。

**示例 2**

输入：

```python
nums = [0,1,1]
```

输出：

```python
[]
```

**示例 3**

输入：

```python
nums = [0,0,0]
```

输出：

```python
[[0,0,0]]
```

### 提示

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

### 题解

#### 思路

先对数组排序，然后固定一个数 `k`，再用双指针在剩余区间中查找另外两个数。

处理重复元素时需要注意：

- 若 `nums[k] > 0`，直接结束循环
- 若 `nums[k] == nums[k - 1]`，跳过当前固定值
- 双指针移动后，也要跳过重复值，避免重复答案

#### 代码

```python
from typing import List


class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        res = []
        n = len(nums)

        for k in range(n - 2):
            if nums[k] > 0:
                break
            if k > 0 and nums[k] == nums[k - 1]:
                continue

            i, j = k + 1, n - 1
            while i < j:
                s = nums[k] + nums[i] + nums[j]
                if s < 0:
                    i += 1
                    while i < j and nums[i] == nums[i - 1]:
                        i += 1
                elif s > 0:
                    j -= 1
                    while i < j and nums[j] == nums[j + 1]:
                        j -= 1
                else:
                    res.append([nums[k], nums[i], nums[j]])
                    i += 1
                    j -= 1
                    while i < j and nums[i] == nums[i - 1]:
                        i += 1
                    while i < j and nums[j] == nums[j + 1]:
                        j -= 1

        return res
```

#### 复杂度分析

- **时间复杂度：** `O(n^2)`
- **空间复杂度：** `O(1)`

## 283. 移动零

### 题目描述

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组末尾，同时保持非零元素的相对顺序。

要求：**必须在不复制数组的情况下原地对数组进行操作。**

### 示例

**示例 1**

输入：

```python
nums = [0,1,0,3,12]
```

输出：

```python
[1,3,12,0,0]
```

**示例 2**

输入：

```python
nums = [0]
```

输出：

```python
[0]
```

### 提示

- `1 <= nums.length <= 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

### 进阶

你能尽量减少完成的操作次数吗？

### 题解

#### 思路

使用双指针：

- `left` 指向当前已经处理好的非零序列末尾
- `right` 向右扫描未处理的元素

当 `nums[right] != 0` 时，将 `nums[left]` 与 `nums[right]` 交换，然后 `left += 1`。

这样可以保证：

- `left` 左边都是非零元素
- 非零元素的相对顺序保持不变

#### 代码

```python
from typing import List


class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        n = len(nums)
        left = 0
        right = 0
        while right < n:
            if nums[right] != 0:
                nums[left], nums[right] = nums[right], nums[left]
                left += 1
            right += 1
```

#### 复杂度分析

- **时间复杂度：** `O(n)`，每个元素最多遍历一次。
- **空间复杂度：** `O(1)`，只使用了常数额外空间。

## 42. 接雨水

### 题目描述

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后可以接多少雨水。

### 示例

**示例 1**

输入：

```python
height = [0,1,0,2,1,0,1,3,2,1,2,1]
```

输出：

```python
6
```

解释：上面是由数组 `[0,1,0,2,1,0,1,3,2,1,2,1]` 表示的高度图，在这种情况下，可以接 `6` 个单位的雨水（蓝色部分表示雨水）。

![接雨水示意图](rainwatertrap.png)

**示例 2**

输入：

```python
height = [4,2,0,3,2,5]
```

输出：

```python
9
```

### 提示

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

### 题解

#### 思路

使用双指针，分别从左右两端向中间收缩，并维护左右两侧的最高高度。

水位由较低一侧的最高高度决定：

- 如果当前高度小于这一侧的最高高度，就能积水
- 否则更新这一侧的最高高度

#### 代码

```python
from typing import List


class Solution:
    def trap(self, height: List[int]) -> int:
        if not height:
            return 0

        left, right = 0, len(height) - 1
        left_max = right_max = 0
        total = 0

        while left < right:
            if height[left] < height[right]:
                if height[left] >= left_max:
                    left_max = height[left]
                else:
                    total += left_max - height[left]
                left += 1
            else:
                if height[right] >= right_max:
                    right_max = height[right]
                else:
                    total += right_max - height[right]
                right -= 1

        return total
```

#### 复杂度分析

- **时间复杂度：** `O(n)`
- **空间复杂度：** `O(1)`

---

# 三、滑动窗口

> 这一类题通常通过维护一个动态区间来求解，常用于字符串、子数组和最长/最短区间问题。

## 3. 无重复字符的最长子串

### 题目描述

给定一个字符串 `s`，请你找出其中不含有重复字符的最长子串的长度。

### 示例

**示例 1**

输入：

```python
s = "abcabcbb"
```

输出：

```python
3
```

解释：无重复字符的最长子串是 `abc`，所以其长度为 `3`。`bca` 和 `cab` 也是正确答案。

**示例 2**

输入：

```python
s = "bbbbb"
```

输出：

```python
1
```

解释：无重复字符的最长子串是 `b`，所以其长度为 `1`。

**示例 3**

输入：

```python
s = "pwwkew"
```

输出：

```python
3
```

解释：无重复字符的最长子串是 `wke`，所以其长度为 `3`。`pwke` 是子序列，不是子串。

### 提示

- `0 <= s.length <= 5 * 10^4`
- `s` 由英文字母、数字、符号和空格组成

### 题解

#### 思路

使用滑动窗口维护一个不包含重复字符的区间。

- `left` 表示窗口左端
- `right` 向右扩展窗口
- 用集合记录当前窗口中的字符

当遇到重复字符时，就不断移动左端，直到窗口重新满足“不重复”的条件。

#### 代码

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if not s:
            return 0

        left = 0
        seen = set()
        ans = 0

        for right in range(len(s)):
            while s[right] in seen:
                seen.remove(s[left])
                left += 1
            seen.add(s[right])
            ans = max(ans, right - left + 1)

        return ans
```

#### 复杂度分析

- **时间复杂度：** `O(n)`
- **空间复杂度：** `O(min(n, 256))`

## 438. 找到字符串中所有字母异位词

### 题目描述

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的异位词的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

### 示例

**示例 1**

输入：

```python
s = "cbaebabacd", p = "abc"
```

输出：

```python
[0,6]
```

解释：起始索引等于 `0` 的子串是 `cba`，它是 `abc` 的异位词。起始索引等于 `6` 的子串是 `bac`，它是 `abc` 的异位词。

**示例 2**

输入：

```python
s = "abab", p = "ab"
```

输出：

```python
[0,1,2]
```

解释：起始索引等于 `0` 的子串是 `ab`，它是 `ab` 的异位词。起始索引等于 `1` 的子串是 `ba`，它是 `ab` 的异位词。起始索引等于 `2` 的子串是 `ab`，它是 `ab` 的异位词。

### 提示

- `1 <= s.length, p.length <= 3 * 10^4`
- `s` 和 `p` 仅包含小写字母

### 题解

#### 思路

因为 `p` 的异位词长度一定和 `p` 相同，所以可以在 `s` 上维护一个长度固定的滑动窗口。

用两个长度为 `26` 的数组分别记录：

- `p` 中每个字母出现的次数
- 当前窗口中每个字母出现的次数

每次窗口右移后比较两个数组是否相同，如果相同，说明当前窗口就是一个异位词。

#### 代码

```python
from typing import List


class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        s_len, p_len = len(s), len(p)
        if s_len < p_len:
            return []

        ans = []
        s_count = [0] * 26
        p_count = [0] * 26

        for i in range(p_len):
            s_count[ord(s[i]) - 97] += 1
            p_count[ord(p[i]) - 97] += 1

        if s_count == p_count:
            ans.append(0)

        for i in range(s_len - p_len):
            s_count[ord(s[i]) - 97] -= 1
            s_count[ord(s[i + p_len]) - 97] += 1
            if s_count == p_count:
                ans.append(i + 1)

        return ans
```

#### 复杂度分析

- **时间复杂度：** `O((n - m) × 26)`，其中 `n` 是 `s` 的长度，`m` 是 `p` 的长度。
- **空间复杂度：** `O(26)`，用于存储字符计数数组。

# 四、前缀和

> 这一类题通常通过维护前缀和，将区间求和转化为哈希查找。

## 560. 和为 K 的子数组

### 题目描述

给你一个整数数组 `nums` 和一个整数 `k`，请你统计并返回该数组中和为 `k` 的子数组的个数。

子数组是数组中元素的连续非空序列。

### 示例

**示例 1**

输入：

```python
nums = [1,1,1], k = 2
```

输出：

```python
2
```

**示例 2**

输入：

```python
nums = [1,2,3], k = 3
```

输出：

```python
2
```

### 提示

- `1 <= nums.length <= 2 * 10^4`
- `-1000 <= nums[i] <= 1000`
- `-10^7 <= k <= 10^7`

### 题解

#### 思路

看到“连续子数组求和”这类题时，通常会想到前缀和。

设 `prefix[j]` 表示前 `j` 个元素的和，那么对于任意子数组 `[i, j)`：

```python
prefix[j] - prefix[i] = k
```

也就是：

```python
prefix[i] = prefix[j] - k
```

所以我们只需要在遍历时，统计之前出现过多少个前缀和 `prefix[j] - k`，就能得到当前结尾的合法子数组个数。

#### 代码

```python
from collections import defaultdict
from typing import List


class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        count = defaultdict(int)
        count[0] = 1
        ans = 0
        prefix = 0

        for x in nums:
            prefix += x
            ans += count[prefix - k]
            count[prefix] += 1

        return ans
```

#### 复杂度分析

- **时间复杂度：** `O(n)`
- **空间复杂度：** `O(n)`




