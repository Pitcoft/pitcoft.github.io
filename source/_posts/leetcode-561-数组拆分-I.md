---
title: 刷题笔记-leetcode 561.数组拆分I
date: 2020-10-27 23:01:01
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/leetcode.jpg
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
> ** 输出**  : 4
> ** 解释**  : n 等于 2, 最大总和为 4 = min(1, 2) + min(3, 4).

**提示：**

	1. n 是正整数,范围在 [1, 10000].
	2. 数组中的元素范围在 [-10000, 10000].

### 方法一：快慢指针（一次遍历）
#### 思路分析：
题目需求是寻找倒数第n个节点，所以我们可以设置两个指针`slow`和`fast`对链表进行遍历，`fast`先走n步的，当`fast`走到链表末尾时，`slow`正好为待删除节点的前继节点。

**具体操作：**

- 初始`slow`和`fast`指针均指向哑节点。
- 先使用`fast`指针遍历n次链表，即`fast`指针与`slow`指针相隔n-1个节点。
- 再同时使用`fast`指针和`slow`指针遍历链表，当`fast`遍历到链表的末尾时（即`fast.next`为空指针），`slow`为倒数第n个节点的前驱节点（即`slow.next`为待删除节点）。

#### 复杂度分析：
时间复杂度：$O(L)$，`L`为该链表长度。
空间复杂度：$O(1)$，一次遍历。

#### 代码：
```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(0) # 设置哑节点
        dummy.next = head
        slow, fast = dummy, dummy # 快慢指针指向哑节点
        # 快指针先走n步
        for i in range(n):
            fast = fast.next
        # 快慢指针同时走，直到fast指针到链表尾部，slow为待删除节点前继节点
        while fast.next:
            slow = slow.next
            fast = fast.next
        # 删除节点
        slow.next = slow.next.next
        return dummy.next
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