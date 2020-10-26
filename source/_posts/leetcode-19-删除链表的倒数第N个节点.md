---
title: 刷题笔记-leetcode 19.删除链表的倒数第N个节点
date: 2020-10-26 23:09:58
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/leetcode.jpg
banner_img: https://gitee.com/pitcoft/blogimg/raw/master/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
math: true
categories:
- leetcode
tags:
- 链表
- 双指针
copyright: true
---
### 题目地址
<https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/>

### 题目内容

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

#### 示例：

> 给定一个链表: 1->2->3->4->5, 和 n = 2.
> 当删除了倒数第二个节点后，链表变为 1->2->3->5.

**说明：**
	给定的 n 保证是有效的。
**进阶：**
	你能尝试使用一趟扫描实现吗？

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
### 方法二：递归
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
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(0)
        dummy.next = head
        slow, fast = dummy, dummy
        for i in range(n):
            fast = fast.next
        while fast.next:
            slow = slow.next
            fast = fast.next
        slow.next = slow.next.next
        return dummy.next
```