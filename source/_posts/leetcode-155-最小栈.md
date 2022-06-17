---
title: 刷题笔记-leetcode 155.最小栈
date: 2020-10-31 23:30:13
index_img: https://pitcoft-1251621975.cos.ap-hongkong.myqcloud.com/img/44796907dffa07a472349d5f22b949b8 (1).jpg
banner_img: https://pitcoft-1251621975.cos.ap-hongkong.myqcloud.com/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 栈
- 设计
- 辅助栈
copyright: true
---
### 题目地址
<https://leetcode-cn.com/problems/min-stack/>

### 题目内容

设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

- `push(x)` —— 将元素 x 推入栈中。
- `pop()` —— 删除栈顶的元素。
- `top()` —— 获取栈顶元素。
- `getMin()` —— 检索栈中的最小元素。

#### 示例：

> **输入：**
> ["MinStack","push","push","push","getMin","pop","top","getMin"]
> [[],[-2],[0],[-3],[],[],[],[]]
> 
> **输出：**
> [null,null,null,null,-3,null,0,-2]
> 
> **解释：**
> MinStack minStack = new MinStack();
> minStack.push(-2);
> minStack.push(0);
> minStack.push(-3);
> minStack.getMin();   --> 返回 -3.
> minStack.pop();
> minStack.top();      --> 返回 0.
> minStack.getMin();   --> 返回 -2.

**提示：**

- `pop`、`top` 和 `getMin` 操作总是在 **非空栈** 上调用。

### 方法一：快慢指针（一次遍历）
#### 思路分析：
题目需求是寻找倒数第n个节点，所以我们可以设置两个指针`slow`和`fast`对链表进行遍历，`fast`先走n步的，当`fast`走到链表末尾时，`slow`正好为待删除节点的前驱节点。

**具体操作：**

- 初始`slow`和`fast`指针均指向哑节点。
- 先使用`fast`指针遍历n次链表，即`fast`指针与`slow`指针相隔n-1个节点。
- 再同时使用`fast`指针和`slow`指针遍历链表，当`fast`遍历到链表的末尾时（即`fast.next`为空指针），`slow`为倒数第n个节点的前驱节点（即`slow.next`为待删除节点）。

#### 复杂度分析：
时间复杂度：$O(1)$
空间复杂度：$O(N)$

#### 代码：
```python
class MinStack:

    def __init__(self):
        self.stack = []
        self.assist = [] # 定义辅助栈

    def push(self, x: int) -> None:
        self.stack.append(x)
        if not self.assist or x <= self.assist[-1]: # 始终压最小的数
            self.assist.append(x)

    def pop(self) -> None:
        if self.stack.pop() == self.assist[-1]: # 执行stack.pop 判断是否与辅助栈最小值相等
            self.assist.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.assist[-1]
```
### 方法二：递归迭代
#### 思路分析：
通过递归的函数`head.next= self.removeNthFromEnd(head.next, n)`从后往前计数，每次向前加一，如果`count != n`时，返回该节点`return head`，继续往前递归，当`count == n`时，返回该节点的`next`给递归的函数（也就是跳过待删除函数，传递的是待删除的函数的`next`）。
#### 复杂度分析：

时间复杂度：$O(L)$，`L`为该链表长度。
空间复杂度：$O(1)$，一次遍历。

#### 代码：

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        global count # 递归时count要赋值
        if head is None:
            count = 0
            return None
        else:
            head.next= self.removeNthFromEnd(head.next, n) # 递归调用
            count += 1 # 回溯时节点的计数
            return head.next if count ==n else head # 删除节点
```