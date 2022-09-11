---
title: 算法(1)-栈和队列
date: 2022-01-04 15:14:26.21
updated: 2022-01-04 15:14:26.21
categories: [算法]
tags: [算法]
---

# 算法(1)-栈与队列

## Introduction

**栈与队列特点：**

- 栈stack：LIFO后进先出
- 队列queue：FIFO先进先出

**API定义：**

```java
public interface Queue<Item> implements Iterable<Item> {
		void enqueue(Item item);
  	Item dequeue();
  	boolean isEmpty();
  	int size();
}
```

```java
public interface Stack<Item> implements Iterable<Item> {
  	void push(Item item);
  	Item pop();
  	boolean isEmpty();
  	int size();
}
```

以上为简单的API定义，队列多用于涉及公平性的问题上如排队、软件的处理队列等，其基本原则就是公平，而栈的特性也十分常见如浏览器的回退、读邮件的顺序等，元素压入的顺序与处理的顺序正好相反。

## 常见技巧

#### 使用递归函数逆序操作一个栈

> 即实现栈中的元素逆序，只不过通过递归的方式实现。实现只能通过栈来实现。

**实现**

```java
public static int getAndRemoveLastElement(Deque<Integer> stack) {
  	int res = stack.pop();
  	if(stack.isEmpty()) {
      	return res;
    } else {
      	int last = getAndRemoveLastElement(stack);
      	stack.push(res);
      	return last;
    }
}
```

以上为实现该问题的主要函数，返回获取并删除栈底的元素。

### 用一个栈实现另一个栈的排序

> 一个元素类型为整数的栈，按照栈顶到栈底从大到小的顺序排序，只能申请一个栈。

**思路**

1. 排序的栈为stack，申请的栈为helper，在stack上操作pop()的元素记做cur
2. 若cur<=helper.peek()，则helper.push(cur)
3. 若cur>helper.peek()，则将所有helper元素逐一压入stack，直到cur<=helper.peek()，再将cur压入helper

**实现**

```java
public static void sortStackByStack(Deque<Integer> stack) {
  	Deque<Integer> hepler = new LinkedList<>();
  	while(!stack.isEmpty()) {
      	int cur = stack.pop();
      	while(!helper.isEmpty() && cur > helper.peek()) {
          	stack.push(helper.pop());
        }
      	helper.push(cur);
    }
  	while(!helper.isEmpty()) {
      	stack.push(helper.pop());
    }
}
```

## 经典问题

### 最小栈

> #### [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)
>
> 难度简单1124
>
> 设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。
>
> - `push(x)` —— 将元素 x 推入栈中。
> - `pop()` —— 删除栈顶的元素。
> - `top()` —— 获取栈顶元素。
> - `getMin()` —— 检索栈中的最小元素。
>
> **提示：**
>
> - `pop`、`top` 和 `getMin` 操作总是在 **非空栈** 上调用。

**思路**

使用两个栈，一个正常存储元素，另一个与之相对存储当前的最小元素。

1. stackDate：存储正常元素，stackMin：存储最小元素
2. 插入操作：stackMin进行比较插入，则可以达到当前栈顶的元素是stackData的最小元素
3. 删除操作：一起pop()即可

**实现**

```java
class MinStack {
    Deque<Integer> stackData, stackMin;
    public MinStack() {
        stackData = new LinkedList<>();
        stackMin = new LinkedList<>();
    }
    
    public void push(int val) {
        stackData.push(val);
        if(stackMin.isEmpty()) {
           stackMin.push(val); 
        } else {
            stackMin.push(stackMin.peek() < val ? stackMin.peek() : val);
        }
    }
    
    public void pop() {
        if(!stackData.isEmpty()) {
            stackData.pop();
            stackMin.pop();
        }
    }
    
    public int top() {
        return stackData.peek();
    }
    
    public int getMin() {
        return stackMin.peek();
    }
}
```

- 因为多为增删操作，采用链式存储
- 为什么使用Deque？JDK中Stack的方法被`sychronized`修饰，因此效率不高。
- 所有操作：空间：O(n) 时间：O(1)

### 两个栈组成的队列

> #### [232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
>
> 难度简单525
>
> 请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）：
>
> 实现 `MyQueue` 类：
>
> - `void push(int x)` 将元素 x 推到队列的末尾
> - `int pop()` 从队列的开头移除并返回元素
> - `int peek()` 返回队列开头的元素
> - `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`
>
> **说明：**
>
> - 你只能使用标准的栈操作 —— 也就是只有 `push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
> - 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
>
> **进阶：**
>
> - 你能否实现每个操作均摊时间复杂度为 `O(1)` 的队列？换句话说，执行 `n` 个操作的总时间复杂度为 `O(n)` ，即使其中一个操作可能花费较长时间。

**思路**

根据栈的特点LIFO，可以将两个栈的特点结合实现队列的FIFO。

1. 使用两个栈，压入栈stackIn、弹出栈stackOut
2. push()：只放入stackIn中
3. pop() | peek()：若stackOut为空，则将所有stackIn数据压入stackOut，通过stackOut进行返回

**实现**

```java
class MyQueue {
    Deque<Integer> stackIn, stackOut;
    public MyQueue() {
        stackIn = new LinkedList<>();
        stackOut = new LinkedList<>();
    }
    
    public void push(int x) {
        stackIn.push(x);
    }
    
    public int pop() {
        put();
        return stackOut.pop();
    }
    
    public int peek() {
        put();
        return stackOut.peek();
    }

    private void put() {
        if(stackOut.isEmpty()) {
            for(;!stackIn.isEmpty();) {
                stackOut.push(stackIn.pop());
            }
        }
    }
    
    public boolean empty() {
        return stackIn.isEmpty() && stackOut.isEmpty();
    }
}
```

- 要点：如果stackOut不为空，则绝对不能将stackIn的数据倾倒到stackOut，会破坏元素顺序。

### 逆波兰表达式求值

> #### [150. 逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
>
> 难度中等439
>
> 根据[ 逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437)，求表达式的值。
>
> 有效的算符包括 `+`、`-`、`*`、`/` 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。
>
>  
>
> **说明：**
>
> - 整数除法只保留整数部分。
> - 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。
>
> 提示：
>
> 1 <= tokens.length <= 104
> tokens[i] 要么是一个算符（"+"、"-"、"*" 或 "/"），要么是一个在范围 [-200, 200] 内的整数
>
>
> 逆波兰表达式：
>
> 逆波兰表达式是一种后缀表达式，所谓后缀就是指算符写在后面。
>
> 平常使用的算式则是一种中缀表达式，如 ( 1 + 2 ) * ( 3 + 4 ) 。
> 该算式的逆波兰表达式写法为 ( ( 1 2 + ) ( 3 4 + ) * ) 。
> 逆波兰表达式主要有以下两个优点：
>
> 去掉括号后表达式无歧义，上式即便写成 1 2 + 3 4 + * 也可以依据次序计算出正确结果。
> 适合用栈操作运算：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中。

**思路**

1. 将操作数入栈，遇到运算符弹出，进行计算后压入栈。
2. 最后结果为栈中的最后一个数。

**实现**

```java
class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new LinkedList<>();
        for(String str : tokens) {
            if(!("+".equals(str) || "-".equals(str)
            || "*".equals(str) || "/".equals(str))) {
                stack.push(Integer.valueOf(str));
            } else {
                int b = stack.pop();
                int a = stack.pop();
                switch(str) {
                    case "+": stack.push(a + b); break;
                    case "-": stack.push(a - b); break;
                    case "*": stack.push(a * b); break;
                    case "/": stack.push(a / b); break;
                }
            }
        }
        return stack.pop();
    }
}
```

## 经典结构

### 单调栈 Monotonic Stack

> #### [496. 下一个更大元素 I](https://leetcode-cn.com/problems/next-greater-element-i/)
>
> 难度简单621
>
> `nums1` 中数字 `x` 的 **下一个更大元素** 是指 `x` 在 `nums2` 中对应位置 **右侧** 的 **第一个** 比 `x` 大的元素。
>
> 给你两个 **没有重复元素** 的数组 `nums1` 和 `nums2` ，下标从 **0** 开始计数，其中`nums1` 是 `nums2` 的子集。
>
> 对于每个 `0 <= i < nums1.length` ，找出满足 `nums1[i] == nums2[j]` 的下标 `j` ，并且在 `nums2` 确定 `nums2[j]` 的 **下一个更大元素** 。如果不存在下一个更大元素，那么本次查询的答案是 `-1` 。
>
> 返回一个长度为 `nums1.length` 的数组 `ans` 作为答案，满足 `ans[i]` 是如上所述的 **下一个更大元素** 。

**思路**

1. 从后往前将元素压入栈stack，记当前元素为cur，这里的栈记录右侧比当前元素第一个大的元素
2. 若cur>stack.peek()，stack.pop()直到cur <= stack.peek()或stack.isEmpty()
3. 利用HashMap记录每个位置

**实现**

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        Deque<Integer> stack = new ArrayDeque<Integer>();
        for (int i = nums2.length - 1; i >= 0; --i) {
            int num = nums2[i];
            while (!stack.isEmpty() && num >= stack.peek()) { 
              // 小的元素出栈 毕竟被大的元素给挡住
                stack.pop();
            }
            map.put(num, stack.isEmpty() ? -1 : stack.peek());
            stack.push(num);
        }
        int[] res = new int[nums1.length];
        for (int i = 0; i < nums1.length; ++i) {
            res[i] = map.get(nums1[i]);
        }
        return res;
    }
}
```

- 要点：for循环要从后往前扫描元素，因为我们借助的是栈元素，倒着入栈，其实就是正着出栈。

**变体**

> 如何处理循环数组？
>
> #### [503. 下一个更大元素 II](https://leetcode-cn.com/problems/next-greater-element-ii/)
>
> 难度中等539
>
> 给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

1. 计算机内存是线性的，因此循环的实现主要通过‘%’来实现。
2. 回到该问题，如何解决循环的问题呢？可以将原始数组翻倍，将原始数组后再接一个原始数组，这样不仅可以与右边比较，还可以与左边比较。

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] ans = new int[n];
        Deque<Integer> stack = new LinkedList<>();
        for(int i=2*n-1; i>=0; i--) {
            int num = nums[i % n];
            while(!stack.isEmpty() && num >= stack.peek()) {
                stack.pop();
            }
            ans[i % n] = stack.isEmpty() ? -1 : stack.peek(); 
            stack.push(num);
        }
        return ans;
    }
}
```

**小结**

单调栈实际就是栈，通过一些巧妙逻辑，使得每次新元素入栈后，栈内的元素都保持单调（单调增或减）。其用途并不广泛，只适用一类问题 Next Greater Element。

### 单调队列 Monotonic Queue

> #### [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)
>
> 难度困难1332
>
> 给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。
>
> 返回滑动窗口中的最大值。
>
> **提示：**
>
> - `1 <= nums.length <= 105`
> - `-104 <= nums[i] <= 104`
> - `1 <= k <= nums.length`

- 该题难度在于，窗口的值是动态调整的，无法知道何时窗口的最大值是否还在，如需要确定则就需要进行窗口的遍历，因此算出最大值的效率就不是O(1)了。

**思路**

1. 需要一个能动态记录当前窗口最大值的API。
2. 单调队列的API相比队列，也仅是多了max()，来获取当前队列的最大值。
3. 为了方便进行头部和尾部的插入删除，因此选用双链表是最合适的。
4. 为了保证单调性，且核心需求是O(1)时间获取当前队列最大值，在设计上主要通过push(val)，若val>队尾元素，则删除，直到队尾元素>=val放入，保证了队列中从队头到队尾单调递减。

**实现**

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] ans = new int[n - k + 1];
        MonotonicQueue queue = new MonotonicQueue();
        for(int i=0,j=0; i<n; i++) {
            if(i < k - 1) {
                // 填充k - 1个数到队列中
                queue.offer(nums[i]);
            } else {
                // 维持只有k个元素
                queue.offer(nums[i]);
                ans[j++] = queue.max();
                queue.poll(nums[i - k + 1]); // 移除最旧的元素
            }
        }
        return ans;
    }

    class MonotonicQueue {
        Deque<Integer> queue;
        MonotonicQueue() {
            queue = new LinkedList<>();
        }
        // 整个单调队列的重点
        void offer(int val) {
            while(!queue.isEmpty() && queue.getLast() < val) {
                queue.removeLast();
            }
            queue.offerLast(val);
        }

        void poll(int val) {
            if(val == queue.getFirst()) {
                queue.removeFirst();
            }
        }
        // 获取当前队列的最大值
        int max() {
            return queue.getFirst();
        }
    }
}
```

- 虽然进行max()操作时O(1)，但是整体的复杂度仍是O(n)，因为进行offer()的时候仍需要遍历。

**小结**

单调队列同单调栈一样，通过一些逻辑，使得每次新元素入队后，队内元素保持单调性（单调递增或单调递减）。该数据结构用途在于可以解决**滑动窗口**的一些问题如“滑动窗口最大值”等。

最后，分清“优先队列”和“单调队列”，单调队列添加元素时靠删除元素保证单调性，而优先队列通过二叉堆来进行自动排序。





 