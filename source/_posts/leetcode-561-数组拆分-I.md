---
title: 刷题笔记-leetcode 561.数组拆分I
date: 2020-10-27 23:01:01
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/40946355dabb3d8f6e0c8e1f3ca9d11e%20(1).jpg
banner_img: https://gitee.com/pitcoft/blogimg/raw/master/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 数组
- 排序
copyright: true
---
### 题目地址
<https://leetcode-cn.com/problems/array-partition-i/>

### 题目内容

给定长度为 **2n** 的数组, 你的任务是将这些数分成 **n** 对, 例如 **(a1, b1), (a2, b2), ..., (an, bn)** ，使得从1 到 n 的 **min(ai, bi)** 总和最大。

#### 示例：

> **输入** : [1,4,3,2]
>
> ** 输出** : 4
> ** 解释**  : n 等于 2, 最大总和为 4 = min(1, 2) + min(3, 4).

**提示：**

	1. n 是正整数,范围在 [1, 10000].
	2. 数组中的元素范围在 [-10000, 10000].

### 方法：数组排序
#### 思路分析：
题目需求是将长度2n的数组拆成n对，在每个队里取最小值，然后相加取得一个最大值。

例如：题目中的`[1,4,3,2]`，拆成2对，与最小数`1`匹配的数，`1`是一定可以取到的，与最大数`4`匹配，取到的一定是另外的一个数。

- 所以要达到收益最大化，必须为最小数匹配第二小的数；最大数匹配第二大的数。

**具体操作：**

- 做数组的原地快速排序。
- 取下标为偶数的值求和即可。

#### 复杂度分析：

`sorted`函数内部实现机制为：`Timsort`

时间复杂度：$O(nlog(n))$，`Timsort`算法的时间复杂度。
空间复杂度：$O(n)$

#### 代码：
```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        sum = 0
        nums.sort()
        for i,item in enumerate(nums):
            if i%2 == 0: # 取下标为偶数的值
                sum += item # 求和
        return sum
```
#### 精简代码：

```python
class Solution:
    def arrayPairSum(self, nums) -> int:
        return sum(sorted(nums)[::2]) # 对数组排序，隔2取一个数并求和
```
#### 总结
- 与最大与第二大匹配就可以保留第二大的数字（目的就是尽量保留大的数）