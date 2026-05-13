# LeetCode Hot 100 - Python 题解

> 持续整理中，当前收录哈希、双指针等题型。

## 目录

- [哈希](#哈希)
  - [1. 字母异位词分组](#1-字母异位词分组)
  - [2. 最长连续序列](#2-最长连续序列)
- [双指针](#双指针)
  - [3. 移动零](#3-移动零)

## 哈希

### 1. 字母异位词分组

#### 题目描述

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

字母异位词是由重新排列源单词的字母得到的一个新单词。

#### 示例

**示例 1**

输入：

```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```

输出：

```python
[["bat"],["nat","tan"],["ate","eat","tea"]]
```

解释：
- 在 `strs` 中没有字符串可以通过重新排列来形成 `bat`
- `nat` 和 `tan` 是字母异位词，因为它们可以重新排列以形成彼此
- `ate`、`eat` 和 `tea` 是字母异位词，因为它们可以重新排列以形成彼此

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

#### 提示

- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母

#### 题解

##### 思路

使用哈希表分组，把排序后的字符串当作哈希表的 `key`，原字符串加入对应的列表作为 `value`。

最后返回哈希表中的所有分组即可。

##### 代码

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

##### 复杂度分析

- **时间复杂度：** `O(n × k log k)`，其中 `n` 是字符串数量，`k` 是字符串最大长度。需要对每个字符串排序。
- **空间复杂度：** `O(n × k)`，用于存储哈希表。

### 2. 最长连续序列

#### 题目描述

给定一个未排序的整数数组 `nums`，找出数字连续的最长序列的长度。

这里的连续序列不要求元素在原数组中连续。

请你设计并实现时间复杂度为 **O(n)** 的算法解决此问题。

#### 示例

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

#### 提示

- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`

#### 题解

##### 思路

这题不能排序，因为排序的时间复杂度是 `O(n log n)`，不符合题目要求。

核心思路是：

1. 先把 `nums` 中的数字放入哈希集合，这样可以 `O(1)` 判断某个数字是否存在。
2. 只从“连续序列的起点”开始向后扩展。

如果 `x - 1` 也在集合中，就说明 `x` 不是起点，直接跳过，这样可以避免重复计算。

> **注意：** 遍历时要遍历哈希集合，而不是原数组。否则在大量重复元素的情况下，可能退化为 `O(n^2)`。

##### 代码

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

##### 复杂度分析

- **时间复杂度：** `O(n)`，每个元素最多被访问常数次。
- **空间复杂度：** `O(n)`，用于存储哈希集合。

## 双指针

### 3. 移动零

#### 题目描述

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组末尾，同时保持非零元素的相对顺序。

要求：**必须在不复制数组的情况下原地对数组进行操作。**

#### 示例

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

#### 提示

- `1 <= nums.length <= 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

#### 进阶

你能尽量减少完成的操作次数吗？

#### 题解

##### 思路

使用双指针：

- `left` 指向当前已经处理好的非零序列的末尾
- `right` 向右扫描未处理的元素

当 `nums[right] != 0` 时，将 `nums[left]` 与 `nums[right]` 交换，然后 `left += 1`。

这样可以保证：

- `left` 左边都是非零元素
- 非零元素的相对顺序保持不变

##### 代码

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

##### 复杂度分析

- **时间复杂度：** `O(n)`，每个元素最多遍历一次。
- **空间复杂度：** `O(1)`，只使用了常数额外空间。
