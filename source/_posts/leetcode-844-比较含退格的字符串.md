---
title: leetcode 844.比较含退格的字符串
date: 2020-10-20 23:24:28
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/leetcode.jpg
banner_img: https://gitee.com/pitcoft/blogimg/raw/master/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 栈
- 双指针
copyright: true
---
### 题目内容

给定 `S` 和 `T` 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。 `#` 代表退格字符。

**注意**：如果对空文本输入退格字符，文本继续为空。

#### 示例 1：
> 输入：S = "ab#c", T = "ad#c"
> 输出：true
> 解释：S 和 T 都会变成 “ac”。

#### 示例 2：
> 输入：S = "ab##", T = "c#d#"
> 输出：true
> 解释：S 和 T 都会变成 “”。

#### 示例 3：
> 输入：S = "a##c", T = "#a#c"
> 输出：true
> 解释：S 和 T 都会变成 “c”。

#### 示例 4：
> 输入：S = "a#c", T = "b"
> 输出：false
> 解释：S 会变成 “c”，但 T 仍然是 “b”。

#### 提示：
`1 <= S.length <= 200`
`1 <= T.length <= 200`
`S` 和 `T` 只含有小写字母以及字符` '#'`。


### 方法一：字符串重构（栈）
#### 思路分析：
当字符串碰见退格符`#`的时，将退格符与前一个字符串一并删除，最终返回删除后的字符串并比较两个字符串是否相等，当遍历字符串时：
- 如果是一般的字符，则压栈。
- 如果是退格符`#`，则把栈顶元素弹出。
- 遍历结束把栈元素用`join`函数连接成字符串

#### 复杂度分析：
时间复杂度：$O(M+N)$
空间复杂度：$O(M+N)$

#### 代码：
```python
class Solution(object):
    def backspaceCompare(self, S, T):
        def inputStr(str):
            stack = list()
            for i in str:
                if i != '#':
                    stack.append(i)
                elif stack != []:
                    stack.pop()
            return ''.join(stack[:])
        return inputStr(S) == inputStr(T)
```
### 方法二：
