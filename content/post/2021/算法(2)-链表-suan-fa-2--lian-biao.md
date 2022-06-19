---
title: 算法(2)-链表
date: 2022-01-04 15:15:26.676
updated: 2022-01-12 12:48:14.098
categories: [算法]
tags: [算法]
---

# 算法(2)-链表

## Introduction

「What」链表是一种递归的数据结构，它或者为NULL，或者指向一个节点(node)的引用，该节点含有一个元素和指向另一条链表的引用。

链表节点的定义：

```java
class Node {
  	Item item;
  	Node next;
}
```

链表的几种类型：

1. 单链表：单链表的节点只能指向下一个节点。
2. 双链表：每个节点有两个指针域，一个指向下一个节点，一个指向上一个节点。
3. 循环链表：即链表首尾相连。可以解决如约瑟夫环问题。

**这里补充一个思维：**

链表也兼具递归结构，树不过是链表的衍生，因此链表也兼具*前序遍历*和*后序遍历*。

```java
void travese(ListNode head) {
  	// 前序
  	travese(head.next);
  	// 后序
}
```

该方式的意义在于，若想正序打印链表val，则通过前序的位置，而想倒序打印则通过后序的位置。该做法的**核心逻辑**：就是将链表节点入方法栈中，然后再取出来。

## 数据结构问题

### 链表设计

> #### [707. 设计链表](https://leetcode-cn.com/problems/design-linked-list/)
>
> 设计链表的实现。您可以选择使用单链表或双链表。单链表中的节点应该具有两个属性：`val` 和 `next`。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。如果要使用双向链表，则还需要一个属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点都是 0-index 的。
>
> 在链表类中实现这些功能：
>
> - get(index)：获取链表中第 `index` 个节点的值。如果索引无效，则返回`-1`。
> - addAtHead(val)：在链表的第一个元素之前添加一个值为 `val` 的节点。插入后，新节点将成为链表的第一个节点。
> - addAtTail(val)：将值为 `val` 的节点追加到链表的最后一个元素。
> - addAtIndex(index,val)：在链表中的第 `index` 个节点之前添加值为 `val` 的节点。如果 `index` 等于链表的长度，则该节点将附加到链表的末尾。如果 `index` 大于链表长度，则不会插入节点。如果`index`小于0，则在头部插入节点。
> - deleteAtIndex(index)：如果索引 `index` 有效，则删除链表中的第 `index` 个节点。

**思路**

- 这里采用双链表的实现方式，为了避免索引的时候代码重复，复用了`getNode()`方法。

**实现**

```java
class MyLinkedList {
    class ListNode {
        int val;
        ListNode prev, next;
        public ListNode(int val) {
            this.val = val;
        }
    }

    int size;
    ListNode head,tail;

    public MyLinkedList() {
        size = 0;
        head = new ListNode(0);
        tail = new ListNode(0);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int index) {
        ListNode node = getNode(index);
        return node == null ? -1 : node.val;
    }

    private ListNode getNode(int index) {
        if(index < 0 || index >= size){
            return null;
        }
        ListNode cur = head;
        if(index < (size - 1) / 2){
            for(int i = 0; i <= index; i++) {
                cur = cur.next;
            }
        } else {
            cur = tail;
            for(int i = 0; i <= size - index - 1; i++) {
                cur = cur.prev;
            }
        }
        return cur;
    }
    
    public void addAtHead(int val) {
        ListNode cur = head;
        ListNode newNode = new ListNode(val);
        newNode.prev = cur;
        newNode.next = cur.next;
        cur.next.prev = newNode;
        cur.next = newNode;
        size++;
    }
    
    public void addAtTail(int val) {
        ListNode cur = tail;
        ListNode newNode = new ListNode(val);
        newNode.next = cur;
        newNode.prev = cur.prev;
        cur.prev.next = newNode;
        cur.prev = newNode;
        size++;
    }
    
    public void addAtIndex(int index, int val) {
        if(index > size) {
            return;
        }
        if(index == size) {
            addAtTail(val);
            return;
        } else if(index <= 0) {
            addAtHead(val);
            return;
        }
        ListNode cur = getNode(index);
        ListNode node = new ListNode(val);
        node.prev = cur.prev;
        node.next = cur;
        cur.prev.next = node;
        cur.prev = node;
        size++;
    }
    
    public void deleteAtIndex(int index) {
        if(index >= size || index < 0) {
            return;
        }
        ListNode node = getNode(index);
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node = null;
        size--;
    }
}
```

### 单链表实现栈和队列

> 单链表实现栈。

**思路**

1. 需要满足LIFO的特性，且数据结构为单链表。
2. 因此采用头插，头删的方式进行实现。

**实现**

```java
public class StackBySingleList {
    private class Node {
        int val;
        Node next;

        public Node(int val) {
            this.val = val;
        }
    }

    Node head;

    public StackBySingleList() {
        head = new Node(0);
    }

    public void push(int val) {
        Node node = new Node(val);
        node.next = head.next;
        head.next = node;
    }

    public int pop() {
        if (isEmpty()) {
            return -1;
        }
        Node node = head.next;
        int res = node.val;
        head.next = node.next;
        // 手动清除，减少GC开销
        node = null;
        return res;
    }

    public boolean isEmpty() {
        return head.next == null;
    }
}

```

> 单链表实现队列。

**思路**

1. 与单链表实现栈不同，而单链表只有head指针，没法指向尾部，因此使用一个指向链表尾部的指针tail。
2. 插入的时候，插入到tail的后面，删除的时候，删除head的后面。

**实现**

```java
public class QueueBySingleList {
    private class Node {
        int val;
        Node next;

        public Node(int val) {
            this.val = val;
        }
    }

    Node head;
    Node tail;

    public QueueBySingleList() {
        head = new Node(0);
        tail = head;
    }

    public void offer(int val) {
        Node node = new Node(val);
        node.next = tail.next;
        tail.next = node;
        tail = node;
    }

    private int poll() {
        if(isEmpty()) {
            return -1;
        }
        Node node = head.next;
        if(tail == node) {
            // 避免tail随之一起丢失
            tail = head;
        }
        int res = node.val;
        head.next = node.next;
        node = null;
        return res;
    }

    public boolean isEmpty() {
        return tail == head;
    }
}
```

## 经典问题

### 环形链表

环形链表的问题在于两点：

1. 判断链表是否有环

2. 如果有环，如何找到该环的入口

**判断链表是否有环：**采用快慢指针法

1. 定义两个指针fast = slow = head，fast每次移动两个节点，slow每次移动一个节点
2. 若fast与slow相遇则说明有环，那为什么有环呢？
   - 若没环，fast不可能与slow相遇，fast会提前到链尾结束循环。
   - fast指针一定先入环，且两个指针相遇也一定是在环中相遇。
   - 在环中相遇的原理，就类比操场上，A以一定速度前进，B以A的两倍速度前进，那么一定会有一个时候，B会凭借速度差与A相遇

**如果有环，该如何找到环入口？**：

![142环形链表2](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/code-interview/how_to_find_cycle_inLinkedList-20220104134301728.png)

1. 如上图，假设头节点到环形入口的节点数为x，入环口到相遇节点的节点数为y，相遇点到入口的节点数为z
2. 若相遇，则slow指针走过的节点数为`x+y`，fast走过的节点数为`x + y + n(y + z)`，n为fast在环内走了n圈才相遇
3. 而fast指针速度是slow的两倍，可以得出等式：`(x + y) * 2 = x + y + n(y + z)`化简的到`x + y = n (y + z)`
4. 因为要找环的入口，所有要求得的是x的值，通过以上可以得到公式：`x = (n - 1)(y + z) + z`，这里将n(y + z)单独提出一个y+z出来，因为y+z代表一圈，同时这里n一定是大于等于1的，因为fast至少需要多走一圈才能相遇
5. 这时候那n为1的情况举例可以得到：`x = z`

以上的具体代码如下：

```java
    /**
     * 判断链表是否有环
     * @param head
     * @return
     */
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }
        ListNode fast = head, slow = head;
        // 空链表、单节点链表一定不会有环
        while (fast != null && fast.next != null) {
            fast = fast.next.next; // 快指针，一次移动两步
            slow = slow.next;      // 慢指针，一次移动一步
            if (fast == slow) {   // 快慢指针相遇，表明有环
                return true;
            }
        }
        return false; // 正常走到链表末尾，表明没有环
    }

    /**
     * 返回入环节点
     * @param head
     * @return
     */
    public ListNode detectCycle(ListNode head) {
        // 找到环节点
        if(head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        boolean hasCycle = false;
        while(fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if(fast == slow) {
                hasCycle = true;
                break;
            }
        }
        if(hasCycle) {
            ListNode p = head;
            while(p != slow) {
                p = p.next;
                slow = slow.next;
            }
            return p;
        } else {
            return null;
        }
    }
```

### 链表反转问题

这是十分典型的问题，面试中也十分常见。以下整理几种常见题型和反转的方式。

#### 反转链表

> #### [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)
>
> 给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

**思路**

1. 提供单链表进行反转（该题是反转整个链表），选择遍历链表的方式，递归还是迭代。
2. 根据索引节点方式的不同，反转的方式也不同。
   1. 通过创建一个dummyNode，来依次交替dummyNode.next与cur的位置从而达到链表的整个反转（该类可以适用于链表的部分反转）。
   2. 创建一个pre指针，依次遍历链表，改变next指向上一个节点，从而达到反转。
   3. 递归的方式，不用一个for循环达到题目要求。（递归需要堆栈，空间O(n)，效率并不高）

**实现**

Method(1):

```java
public ListNode reverseList(ListNode head) {
  ListNode pre = null;
  for(ListNode cur = head; cur != null;) {
    ListNode tmp = cur.next;
    cur.next = pre;
    pre = cur;
    cur = tmp;
  }
  return pre;
}
```

Method(2):

```java
public ListNode reverseList(ListNode head) {
  if(head == null) {
    return null;
  }
  ListNode dummy = new ListNode(0);
  dummy.next = head;
  for(ListNode cur = head; cur.next != null;) {
    ListNode tmp = cur.next;
    cur.next = tmp.next;
    tmp.next = dummy.next;
    dummy.next = tmp;
  }
  return dummy.next;
}
```

- 这里可以发现cur指针我们无需进行移动，它会随着周围节点的变动自动向后推动。

Method(3):

```java
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode last = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return last;
    }
```

- 递归第一步：定义函数行为，该函数的行为就是将整个链表反转并返回反转后的head节点。
- base case：链表如果只有一个节点，直接返回即可。
- last就是反转后的链表head节点，因此return last。
- head.next.next = head.next在函数中将链表的下一个节点指向上一个节点。
- head.next = null，因为链表末端要指向null。

> #### [92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
>
> 难度中等1113
>
> 给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

**思路**

- 不同与上面，该反转为反转链表的一部分[left, right]，注意left==1就是head了。
- 具体做法也是先遍历到指定位置开始反转，并计算反转个数。

**实现**

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if(head == null || left == right) {
            return head;
        }
        ListNode dummy = new ListNode(0);
        ListNode pre = dummy;
        dummy.next = head;
        int step = right - left;
        for(int i=1; i<left; i++) {
            pre = pre.next;
        }
        for(ListNode cur = pre.next; step != 0 && cur.next != null; step--) {
            ListNode tmp = cur.next;
            cur.next = tmp.next;
            tmp.next = pre.next;
            pre.next = tmp;
        }
        return dummy.next;
    }
}
```

- 这里注意，若出现[3,5] 1,2的情况，head将会变动，返回不了完整的链表，因此需要pre指针。

#### K个一组翻转链表

> #### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)
>
> 难度困难1413
>
> 给你一个链表，每 *k* 个节点一组进行翻转，请你返回翻转后的链表。
>
> *k* 是一个正整数，它的值小于或等于链表的长度。
>
> 如果节点总数不是 *k* 的整数倍，那么请将最后剩余的节点保持原有顺序。
>
> **进阶：**
>
> - 你可以设计一个只使用常数额外空间的算法来解决此问题吗？
> - **你不能只是单纯的改变节点内部的值**，而是需要实际进行节点交换。

**思路**

1. 很明显，需要多次翻转链表，题目**具有递归的性质**。
2. 问题在于翻转前k个节点后，后面的链表要如何处理？
3. 具体算法流程：(递归方式)
   1. 先翻转以head开头的k个元素。
   2. 将第k+1个元素作为head，递归调用`reverseKGroup()`。
   3. 将上述两个结果连接起来。
4. 需要注意的是，如果最后元素个数不足k个，则保持不变（base case）。

**实现**

Method(1):

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null || k <= 1) {
            return head;
        }
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode a, b;
        a = b = head;
        for(int i=0; i<k; i++) {
            if(b == null) {
                // 不足k个
                return head;
            }
            b = b.next;
        }
        // 翻转前k个
        ListNode newHead = reverse(a, b);
        // 递归调用
        a.next = reverseKGroup(b, k);
        return newHead;
    }

    private ListNode reverse(ListNode a, ListNode b) {
        ListNode pre = null;
        for(ListNode cur=a; cur!=b;) {
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
}
```

Method(2): 迭代的方式

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode(0);
        ListNode pre = new ListNode(0);
        pre.next = head;
        int len = 0;
      	// 计算出有多少元素
        for (ListNode cur = head; cur != null; cur = cur.next) {
            len++;
        }
        if (len < k) {
            return head;
        }
        ListNode cur = head;
        for (int i = 0; i < len / k; i++) {
          	//（1）
            for (int j = k; j > 1; j--) {
                ListNode tmp = cur.next;
                cur.next = tmp.next;
                tmp.next = pre.next;
                pre.next = tmp;
            }
          	//（2）
            if (dummy.next == null) {
                dummy.next = pre.next;
            }
            pre = cur;
            cur = cur.next;
        }
        return dummy.next;
    }
}
```

（1）注意j>1，因为实际迭代次数=k-1次

（2）注意，这里为了保存链表头，解决第一次翻转后连接后面链表的问题。

### 相交链表

> #### [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)
>
> 难度简单1487
>
> 给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 `null` 。

**思路**

1. 假设链表A、B，设相交从head到达交点的距离为ax，bx，交点后到tail的距离则都为y。
2. 让指针a、b走过链表A、B，若有交点则走过的距离为ax+y+bx==bx+y+ax，则表示有交点，若没有交点则走过的距离则不同。

**实现**

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null) {
            return null;
        }
        ListNode a, b;
        for(a=headA, b=headB; a != b;) {
            a = a == null ? headB : a.next;
            b = b == null ? headA : b.next;
        }
        return a;
    }
}
```

### 合并两个有序链表

> #### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
>
> 难度简单2129
>
> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**实现** 

Method1:

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(0);
        ListNode p, q, cur;
        for(p=list1, q=list2, cur=dummy; p!=null && q!=null; cur = cur.next) {
            if(p.val <= q.val) {
                cur.next = p;
                p = p.next;
            } else {
                cur.next = q;
                q = q.next;
            }
        }
      	cur.next = p != null ? p : q;
        return dummy.next;
    }
}
```

Method2递归:

```java
class Solution {
		public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        ListNode head = null;
        if (l1.val <= l2.val){
            head = l1;
            head.next = mergeTwoLists(l1.next, l2);
        } else {
            head = l2;
            head.next = mergeTwoLists(l1, l2.next);
        }
        return head;
    }
}
```

### 合并K个升序链表

> #### [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)
>
> 难度困难1677
>
> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。
>
> 提示：
>
> k == lists.length
> 0 <= k <= 10^4
> 0 <= lists[i].length <= 500
> -10^4 <= lists[i][j] <= 10^4
> lists[i] 按 升序 排列
> lists[i].length 的总和不超过 10^4

**思路**

1. Method1:分而治之，将两两链表都通过上面方法`mergeTwoLists()`合并。
2. Method2:空间换时间的方式，将链表存入优先队列PriorityQueue中。

**实现**

Method1:

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        int n;
        if(lists == null || (n = lists.length) == 0) {
            return null;
        }
        ListNode pre = lists[0];
        for(int i=1; i<n; i++) {
            pre = mergeTwoLists(pre, lists[i]);
        }
        return pre;
    }
}
```

- 时间O(n^2)空间O(1)

## 面试常见

### 删除排序链表的重复元素

> #### [82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
>
> 难度中等774
>
> 存在一个按升序排列的链表，给你这个链表的头节点 `head` ，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中 **没有重复出现** 的数字。
>
> 返回同样按升序排列的结果链表。

> #### [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
>
> 难度简单706
>
> 存在一个按升序排列的链表，给你这个链表的头节点 `head` ，请你删除所有重复的元素，使每个元素 **只出现一次** 。
>
> 返回同样按升序排列的结果链表。

**思路**

1. Method1:HashSet，若存在则跳过，不存在则添加的结果链表中（时间O(n)，空间O(n)）。
2. Method2:类似于排序的方式，每遍历一个节点，将后续存在重复的节点删除（时间O(n^2)，空间O(1)）。
3. Method3:递归的方式：
   1. 终止条件：head指向链表只剩一个元素的时候。
   2. 返回值：返回已经去重的头节点。

**实现**

*83题的实现：*

```java
// Method1
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        Set<Integer> set = new HashSet<>();
        ListNode pre = head;
        set.add(head.val);
        for(ListNode cur = pre.next; cur != null;) {
            if(set.contains(cur.val)) {
                pre.next = cur.next;
            } else {
                pre = cur;
                set.add(cur.val);
            }
            cur = cur.next;
        }
        return head;
    }
}
```

```java
// Method2
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        for(ListNode cur=head; cur != null;) {
            while(cur.next != null && cur.next.val == cur.val) {
                cur.next = cur.next.next;
            }
            cur = cur.next;
        }
        return head;
    }
}
```

```java
// Method3
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        head.next = deleteDuplicates(head.next);
        if(head.val == head.next.val) {
            head = head.next;
        }
        return head;
    }
}
```

*82题实现：最大区别就是删除所有重复，因为头节点可能会被删除，其在这基础上创建空节点进行上面同样的思路即可。*

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        for(ListNode cur=head, pre=dummy; cur!=null && cur.next!=null;) {
            boolean isDep = false;
            while(cur.next != null && cur.next.val == cur.val) {
                cur = cur.next;
                isDep = true;
            }
            if(isDep) {
                pre.next = cur.next;
            } else {
                pre = cur;
            }
            cur = cur.next;
        }
        return dummy.next;
    }
}
```

### 删除链表的倒数第N个节点

> #### [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
>
> 难度中等1735
>
> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**思路**

1. 利用栈或其他容器收集节点。
2. 快慢指针思想，快指针fast先走n步，当fast.next==null，slow.next刚好指向删除节点。

**实现**

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if(head == null || n == 0) {
            return head;
        }
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode fast, slow;
        for(fast=dummy; n-- > 0;) {
            fast = fast.next;
        }
        for(slow=dummy; fast.next != null; ) {
            slow = slow.next;
            fast = fast.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

### 回文链表

> #### [剑指 Offer II 027. 回文链表](https://leetcode-cn.com/problems/aMhZSa/)
>
> 难度简单23
>
> 给定一个链表的 **头节点** `head` **，**请判断其是否为回文链表。
>
> 如果一个链表是回文，那么链表节点序列从前往后看和从后往前看是相同的。

**思路**

1. 寻找回文的核心办法是从**中心向两端扩散或两端向中间蔓延**，而题目给的是单链表没法倒着遍历，无法使用双指针。
2. Method1:最简单的办法就是，将原始链表反转成一条新的链表，然后比较两条链表是否相同（这里不展示该种方式）。
   - 这里可以进行优化，通过快慢指针，找到中点n/2+1，反转一半链表，再通过双指针（注意这里的奇偶中点位置）。
3. Mehtod2:借助二叉树的后序遍历方式，不需显式反转也可以倒序遍历（这里具体查看\#Introduction）。

**实现**

Method2:

```java
class Solution {
    ListNode left;

    public boolean isPalindrome(ListNode head) {
        left = head;
        return traverse(head);
    }

    private boolean traverse(ListNode right) {
        if(right == null) {
            return true;
        }
        boolean res = traverse(right.next);
        // 后序逻辑
        res = res && left.val == right.val;
        left = left.next;
        return res;
    }
}
```

- 时间和空间的效率都是O(n)

Method1优化:

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        if(head == null) {
            return true;
        }
        ListNode slow, fast;
        slow = fast = head;
        for(;fast != null && fast.next != null; ) {
            fast = fast.next.next;
            slow = slow.next;
        }
        for(ListNode cur=reverse(slow); cur != null;) {
            if(head.val != cur.val) {
                return false;
            }
            cur = cur.next;
            head = head.next;
        }
        return true;
    }

    private ListNode reverse(ListNode head) {
        ListNode pre = null;
        for(ListNode cur = head; cur != null;) {
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
}
```

- 时间为O(n)，空间为O(1)，已经属于最优了。

## 题目补充

### 求相交链表的节点

```Java
/**
 * Description:
 * 求相交的链表
 * 设交集链表长c,链表1除交集的长度为a，链表2除交集的长度为b，有
 * <p>
 * a + c + b = b + c + a
 * 若无交集，则a + b = b + a
 * <p>
 * 两个结点不断的去对方的轨迹中寻找对方的身影，只要二人有交集，就终会相遇
 * <p>
 * 两个链表，找出它们的第一个公共节点。
 *
 * @author:edgarding
 * @date:2021/3/4
 **/
public class IntersectionNode {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode h1 = headA, h2 = headB;
        while (h1 != h2) {
            h1 = h1 == null ? headB : h1.next;
            h2 = h2 == null ? headA : h2.next;
        }
        return h1;
    }
}
```

### 奇偶链表

```Java
    /**
     * 奇偶链表
     * 给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。
     * <p>
     * 请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。
     *
     * @param head
     * @return
     */
    public ListNode oddEvenList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        // head 为奇链表头节点 o为尾节点
        ListNode o = head;
        // p 为偶链表的头节点 e为尾节点
        ListNode p = head.next, e = p;
        for (; o.next != null && e.next != null; ) {
            o.next = e.next;
            o = o.next;
            e.next = o.next;
            e = e.next;
        }
        o.next = p;
        return head;
    }
```

### 合并链表

```Java
public class MergeKLists {

    static class ListNode {
        public int val;
        public ListNode next;

        public ListNode(int val) {
            this.val = val;
        }
    }

    public ListNode mergeKLists(ListNode[] lists) {
        // 分治
        int n = lists.length;
        if (n == 0) {
            return null;
        } else if (n == 1) {
            return lists[0];
        } else if (n == 2) {
            return mergeTwoLists(lists[0], lists[1]);
        }

        int mid = (n >> 1);
        ListNode[] l1 = new ListNode[mid];
        ListNode[] l2 = new ListNode[n - mid];
        for (int i = 0; i < mid; i++) {
            l1[i] = lists[i];
        }
        for (int i = mid, j = 0; j < l2.length; i++, j++) {
            l2[j] = lists[i];
        }
        // 详细思想参考 归并排序
        return mergeTwoLists(mergeKLists(l1), mergeKLists(l2));
    }

    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }
        ListNode head = null;
        if (l1.val <= l2.val) {
            head = l1;
            head.next = mergeTwoLists(l1.next, l2);
        } else {
            head = l2;
            head.next = mergeTwoLists(l1, l2.next);
        }
        return head;
    }

    /**
     * 巧妙办法 -》 利用最小堆
     *
     * @param lists
     * @return
     */
    public ListNode mergeKLists2(ListNode[] lists) {
        int n = lists.length;
        if (n == 0) {
            return null;
        } else if (n == 1) {
            return lists[0];
        }

        PriorityQueue<ListNode> pq = new PriorityQueue<>(new Comparator<ListNode>() {
            @Override
            public int compare(ListNode o1, ListNode o2) {
                return o1.val - o2.val;
            }
        });
        for (ListNode list : lists) {
            pq.add(list);
        }
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        while (!pq.isEmpty()) {
            ListNode node = pq.poll();
            cur.next = node;
            cur = cur.next;
            if (node.next != null) {
                pq.add(node.next);
            }
        }
        return dummy.next;
    }
}
```

### 分割链表

给你一个头结点为 head 的单链表和一个整数 k ，请你设计一个算法将链表分隔为 k 个连续的部分。

每部分的长度应该尽可能的相等：任意两部分的长度差距不能超过 1 。这可能会导致有些部分为 null 。

这 k 个部分应该按照在链表中出现的顺序排列，并且排在前面的部分的长度应该大于或等于排在后面的长度。

返回一个由上述 k 部分组成的数组。

```Java
class Solution {
		public ListNode[] splitListToParts(ListNode head, int k) {
        // 任意两部分长度差不能超过1
        // 前面部分 >= 后面部分
        int n = 0;
        ListNode cur = root;
        while(cur != null){
            n++;
            cur = cur.next;
        }
        // 多出来的长度
        int mod = n % k;
        // 每一个的长度
        int size = n / k;
        ListNode [] res = new ListNode[k];
        cur  = root;
        for (int i = 0; i < k && cur != null; i++){
            res[i] = cur;
            int cursize = size + (mod-- > 0 ? 1 : 0);
            for (int j = 0; j < cursize - 1; j++){
                cur = cur.next;
            }
            ListNode next = cur.next;
            cur.next = null;
            cur = next;
        }
        return res;

    }
}
```

### 排序链表

```Java
class Solution {
    public ListNode sortList(ListNode head) {
        return head == null ? null : mergeSort(head);
    }

    private ListNode mergeSort(ListNode head) {
        if (head.next == null) {
            return head;
        }
        ListNode p = head, q = head, pre = null;
        while (q != null && q.next != null) {
            pre = p;
            p = p.next;
            q = q.next.next;
        }
        pre.next = null;
        ListNode l = mergeSort(head);
        ListNode r = mergeSort(p);
        return merge(l, r);
    }

    ListNode merge(ListNode l, ListNode r) {
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while (l != null && r != null) {
            if (l.val <= r.val) {
                cur.next = l;
                cur = cur.next;
                l = l.next;
            } else {
                cur.next = r;
                cur = cur.next;
                r = r.next;
            }
        }
        if (l != null) {
            cur.next = l;
        }
        if (r != null) {
            cur.next = r;
        }
        return dummyHead.next;
    }
}
```

### 重排链表

```Java
    /**
     * 重排链表
     * 给定一个单链表L：L0→L1→…→Ln-1→Ln ，
     * 将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…
     * 
     * 你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
     *
     * @param head
     */
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) {
            return;
        }
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        // 使得slow位于中间靠左边
        ListNode fast = dummy, slow = dummy;
        for (; fast != null && fast.next != null; fast = fast.next.next, slow = slow.next) {
        }
        ;
        ListNode last = slow.next;
        slow.next = null;
        last = reverseList(last);
        // 归并
        for (ListNode cur = head; last != null; ) {
            ListNode tmp = cur.next;
            ListNode tmp2 = last.next;
            cur.next = last;
            last.next = tmp;
            cur = tmp;
            last = tmp2;
        }
    }
```