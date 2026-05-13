# LeetCode Hot 100 - Python 题解

---

## 哈希

### 1. 字母异位词分组

## 题目描述

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

字母异位词是由重新排列源单词的字母得到的一个新单词。

## 示例

### 示例 1

**输入:**
```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```

**输出:**
```python
[["bat"],["nat","tan"],["ate","eat","tea"]]
```

**解释:**
- 在 strs 中没有字符串可以通过重新排列来形成 "bat"
- 字符串 "nat" 和 "tan" 是字母异位词，因为它们可以重新排列以形成彼此
- 字符串 "ate"、"eat" 和 "tea" 是字母异位词，因为它们可以重新排列以形成彼此

### 示例 2

**输入:**
```python
strs = [""]
```

**输出:**
```python
[[""]]
```

### 示例 3

**输入:**
```python
strs = ["a"]
```

**输出:**
```python
[["a"]]
```

## 提示

- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母

## 题解

### 思路

用哈希表分组，把排序后的字符串当作哈希表的 key，排序前的字符串加到对应的列表中（哈希表的 value）。

最后把哈希表的所有 value 加到一个列表中返回。

### 代码

```python
from collections import defaultdict
from typing import List


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        d = defaultdict(list)
        for s in strs:
            sorted_s = ''.join(sorted(s))
            d[sorted_s].append(s)
        return list(d.values())
```

### 复杂度分析

- **时间复杂度:** O(n × k log k)，其中 n 是字符串数量，k 是字符串的最大长度。需要对每个字符串进行排序。
- **空间复杂度:** O(n × k)，用于存储哈希表。


---

### 2. 最长连续序列

## 题目描述

给定一个未排序的整数数组 `nums`，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 **O(n)** 的算法解决此问题。

## 示例

### 示例 1

**输入:**
```python
nums = [100,4,200,1,3,2]
```

**输出:**
```python
4
```

**解释:** 最长数字连续序列是 `[1, 2, 3, 4]`。它的长度为 4。

### 示例 2

**输入:**
```python
nums = [0,3,7,2,5,8,4,6,0,1]
```

**输出:**
```python
9
```

### 示例 3

**输入:**
```python
nums = [1,0,1,2]
```

**输出:**
```python
3
```

## 提示

- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`

## 题解

### 思路

首先，本题是不能排序的，因为排序的时间复杂度是 O(n log n)，不符合题目 O(n) 的要求。

**核心思路：** 对于 `nums` 中的元素 x，以 x 为起点，不断查找下一个数 x+1, x+2, ... 是否在 `nums` 中，并统计序列的长度。

为了做到 O(n) 的时间复杂度，需要两个关键优化：

1. 把 `nums` 中的数都放入一个哈希集合中，这样可以 O(1) 判断数字是否在 `nums` 中。
2. 如果 x-1 在哈希集合中，则不以 x 为起点。因为以 x-1 为起点计算出的序列长度，一定比以 x 为起点计算出的序列长度要长！这样可以避免大量重复计算。

> ⚠️ **注意：** 遍历元素的时候，要遍历哈希集合，而不是 `nums`！如果 `nums=[1,1,1,...,1,2,3,4,5,...]`（前一半都是 1），遍历 `nums` 的做法会导致每个 1 都跑一个 O(n) 的循环，总的循环次数是 O(n²)，会超时。

### 代码

```python
from typing import List


class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        st = set(nums)  # 把 nums 转成哈希集合
        ans = 0
        for x in st:  # 遍历哈希集合
            if x - 1 in st:  # 如果 x 不是序列的起点，直接跳过
                continue
            # x 是序列的起点
            y = x + 1
            while y in st:  # 不断查找下一个数是否在哈希集合中
                y += 1
            # 循环结束后，y-1 是最后一个在哈希集合中的数
            ans = max(ans, y - x)  # 从 x 到 y-1 一共 y-x 个数
        return ans
```

### 复杂度分析

- **时间复杂度:** O(n)，虽然看起来有嵌套循环，但每个元素最多被访问两次（一次在外部循环，一次在内部 while 循环），所以总时间复杂度是 O(n)。
- **空间复杂度:** O(n)，用于存储哈希集合。


