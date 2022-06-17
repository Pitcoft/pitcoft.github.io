---
title: 刷题笔记-leetcode 844.比较含退格的字符串
date: 2020-10-20 23:24:28
index_img: https://pitcoft-1251621975.cos.ap-hongkong.myqcloud.com/img/ff1baa1afd115d0ebb4788508b27ef8f%20(1).jpg
banner_img: https://pitcoft-1251621975.cos.ap-hongkong.myqcloud.com/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 栈
- 双指针
copyright: true
---
### 题目地址
<https://leetcode-cn.com/problems/backspace-string-compare/>

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
时间复杂度：$O(M+N)$，分别遍历`S`和`T`两个字符串
空间复杂度：$O(M+N)$

#### 代码：
```python
class Solution(object):
    def backspaceCompare(self, S, T):
        def inputStr(str):
            stack = list()
            for i in str:
                # 判断为普通字符则压栈
                if i != '#':
                    stack.append(i)
                # 为#且栈不为空则栈顶出栈
                elif stack != []:
                    stack.pop()
            return ''.join(stack[:])
        return inputStr(S) == inputStr(T)
```
### 方法二：双指针
#### 思路分析：
当遇见`#`字符时，会删除其前一个字符，也就是说，`#`的影响仅限于删除它的前一个字符而不会影响到其他字符，因此我们可以用逆序遍历整个字符串，当存在`#`字符时，就可以判断是否要删除前一个字符。具体步骤：
1. 定义双指针，分别指向`S`和`T`两个字符串的末尾。
2. 定义变量count，记录`#`出现的次数（也就是待删除元素的数量）。

 遍历字符串的每一个字符，如果该字符：
- 为`#`字符，则记录的变量count加1
- 为普通字符，则：
	- count = 0时，表示该字符不需要删除。
	- count > 0时，该字符删除，并且count减1。

按以上逆序遍历两个字符串，会碰见以下三种情况：
- count为0，且两个指针同时指向普通字符，判断两字符是否相等，不等返回False，相等继续判断。
```python
if p >= 0 and q >= 0:
	if S[p] != T[q]:
		return False
```
- 当一个字符串遍历完成，代表该字符串为“ ”，条件为`p < 0 or q < 0`，并且另一个字符串仍未遍历完（也就是已经跳出循环，处于在等待判断普通字符是否与另一个字符串相等的阶段），这时候两个字符串是不可能相等的（一个空，另一个的字符串在等待判断）,直接返回`False`。
```python
elif p >= 0 or q >= 0:
	return False
```
- 两个字符串遍历完成，则`p`与`q`都 < 0，代表字符串都为“ ”。返回`True`。

#### 复杂度分析：

时间复杂度：$O(M+N)$，分别遍历`S`和`T`两个字符串
空间复杂度：$O(1)$

#### 代码：

```python
class Solution:
    def backspaceCompare(self, S: str, T: str) -> bool:
        # 定义双指针
        p, q = len(S) - 1, len(T) - 1
        # 定义变量记录 '#' 的数量
        count_S, count_T = 0, 0
        while p >= 0 or q >= 0:
            # 遍历字符串 S
            while p >= 0:
                # 字符为 '#'，count_S 加 1，p指针左移
                if S[p] == '#':
                    count_S += 1
                    p -= 1
                # 字符为普通字符，count_S>0，删除该字符，count_S，p左移继续遍历
                elif count_S > 0:
                    count_S -= 1
                    p -= 1
                # 字符为普通字符，count_S=0，跳出循环，等待与另一个字符串判断
                else:
                    break
            # 遍历字符串 T
            while q >= 0:
                if T[q] == '#':
                    count_T += 1
                    q -= 1
                elif count_T > 0:
                    count_T -= 1
                    q -= 1
                else:
                    break
            # 判断字符串两个未遍历完，跳出循环时，两个指针对应的字符是否相等
            if p >= 0 and q >= 0:
                if S[p] != T[q]:
                    return False
            # 只有一个字符串遍历完，另一个跳出循环
            elif p >= 0 or q >= 0:
                return False
            p -= 1
            q -= 1
        return True
```