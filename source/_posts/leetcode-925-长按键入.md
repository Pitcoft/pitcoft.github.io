---
title: 刷题笔记-leetcode 925.长按键入
date: 2020-10-30 22:00:55
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/ad10b126218a464bc4154fcb3f156bef%20(1).jpg
banner_img: https://gitee.com/pitcoft/blogimg/raw/master/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 字符串
- 双指针
copyright: true
---
### 题目地址
<https://leetcode-cn.com/problems/long-pressed-name/>

### 题目内容

你的朋友正在使用键盘输入他的名字 `name`。偶尔，在键入字符 `c` 时，按键可能会被长按，而字符可能被输入 1 次或多次。

你将会检查键盘输入的字符 `typed`。如果它对应的可能是你的朋友的名字（其中一些字符可能被长按），那么就返回 `True`。

#### 示例 1：
> **输入**：name = "alex", typed = "aaleex"
> **输出**：true
> **解释**：'alex' 中的 'a' 和 'e' 被长按。

#### 示例 2：
> **输入**：name = "saeed", typed = "ssaaedd"
> **输出**：false
> **解释**：'e' 一定需要被键入两次，但在 typed 的输出中不是这样。

#### 示例 3：
> **输入**：name = "leelee", typed = "lleeelee"
> **输出**：true

#### 示例 4：
> **输入**：name = "laiden", typed = "laiden"
> **输出**：ture
> **解释**：长按名字中的字符并不是必要的。

#### 提示：
1. `name.length <= 1000`
2. `typed.length <= 1000`
3. `name` 和 `typed` 的字符都是小写字母。


### 方法一：双指针
#### 思路分析：
题意为`name`为正常输入，`typed`可能存在某个字符长按连续键入多次。即需求为判断`typed`是长按连续键入还是错误键入。

可以用双指针分别遍历`name`和`typed`，判断两者是否符合条件，具体思路为：

- 定义`p`，`q`两个指针，分别指向`name`和`typed`字符串的首个字符。
- 当两者指针指向同一个字符时，说明字符匹配，`p`，`q`都往后移动一个字符。
- 当两者指针指向字符不匹配，分两种情况：
	- 如果`q`指针指向字符**等于**`q`指针后移一位指向的字符，表示该字符是**连续键入**。
	- 如果`q`指针指向字符**不等于**`q`指针后移一位指向的字符，表示该字符是**错误键入**。
- 最后判断当`j=0`时即跳出循环时，`typed`已经遍历完，而`name`仍未遍历完，两者必不相等，直接返回`False`。

#### 复杂度分析：
时间复杂度：$O(M+N)$，分别遍历`name`和`typed`两个字符串。
空间复杂度：$O(1)$，常数个辅助变量。

#### 代码：
```python
class Solution:
    def isLongPressedName(self, name: str, typed: str) -> bool:
        m, n = len(name), len(typed)
        # 定义两个指针，分别指向name和typed的首字符
        i, j = 0, 0
        # 遍历typed到尾字符
        while j < n:
            # 两个指针同时指向字符相等，两指针后移
            if i < m and name[i] == typed[j]:
                i += 1
                j += 1
            # j = 0时不满足首字符相等，name和typed首字符不相等，直接返回False
            # j > 0时指针指向字符等于下一个字符，j指针后移，继续判断
            # j > 0时指针指向字符不等于下一个字符，代表错误输入了字符，直接返回False
            elif j > 0 and typed[j] == typed[j-1]:
                j += 1
            else:
                return False
        # 最后跳出循环(代表typed已经遍历完了)，判断name是否遍历完，完则True，否则返回False。
        return i == m
```
### 方法二：单指针
#### 思路分析：
题目说明name和typed均为小写字母，设置一个单指针，指向`typed`首字符，用`i`表示name字符的索引，具体步骤：

- 遍历`typed`，字符串`type`与`name`对应字符相等，则`i`加一，继续遍历`typed`。
- 当字符串`type`与`name`对应字符**不相等**，则：
	- 当前字符与前一个字符**相等**，表示该字符是**连续键入**，继续往后遍历。
	- 当前字符与前一个字符**不相等**，表示该字符是**错误键入**，直接返回False。
- 最后`typed`遍历结束时，判断`name`是否走完，若`name`仍未遍历完，两者必不相等，直接返回`False`。

> 例子：name = "pitcoft", typed = "pit"

**注意边界问题**：

> 例子：name = "a", typed = "aaa"

- **问题**：如果按以上执行此例子，会报错，原因是第一个字符匹配后，`i = 1`，导致下一次判断`name[1]`不存在。
- **解决办法**：在`name`后面加上一个辅助字符串`“$”`，这样`i = 1`时能读取到`“$”`进而满足`type = name[i-1]`。

#### 复杂度分析：

时间复杂度：$O(M+N)$，分别遍历`S`和`T`两个字符串
空间复杂度：$O(1)$

#### 代码：

```python
class Solution:
    def isLongPressedName(self, name: str, typed: str) -> bool:
        i = 0 
        name += "$"
        for type in typed:
            if type == name[i]:# 当前字符相等，后移继续判断
                i += 1
            elif type != name[i-1]:# 当前字符与前一位不等，直接返回False，相等则继续判断
                return False
        return i == len(name) - 1 #减一是因为加了辅助的$
```