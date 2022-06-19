---
title: 算法(0)-base
date: 2021-11-17 13:39:19.18
updated: 2022-01-12 12:55:06.414
categories: [算法]
tags: [算法]
---

# 算法(0)-base

## Introduction

该文主要总结一些有关算法的基础，其具体的分类等在之后进行，这也是对自己刷题这么久来的一次系统化整理。该篇主要进行一些前置知识的整理和常见问题。具体类型查看后面系列，该文不涉及dp类等较难问题。

其代码将具体存于：[code-collection](https://github.com/EdgarDing77/code-collection)

**更新说明：**

- 2022/1/2：开始刷题笔记，具体处于 interview-note 模块中更具有方向性的进行练习，提高自己。
  - 分类依据《程序员代码面试指南》，进行章节划分
  - 题库参考：
    - codetop.cn
    - 《程序员代码面试指南》
    - 《labuladong的算法小抄》
    - 《算法》第四版
    - …

## 基础

### 时间复杂度

![img](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/algorithm/computational_complexity.png)

「What」时间复杂度是一个函数，定性描述该算法的运行时间。

> 假设算法的问题规模为n，那么操作单元数量便用函数f(n)来表示，随着数据规模n的增大，算法执行时间的增长率和f(n)的增长率相同，这称作为算法的渐近时间复杂度，简称时间复杂度，记为 O(f(n))。

时间复杂度的衡量通过**大O表示法Big O notation**，即O(n),O($n^2$)等方式。大O用于表示算法的*最坏情况下复杂度*，即对任意数据输入的运行时间上界。而时间复杂度都是忽略常数项系数的，因为一般情况下都是默认数据规模足够的大，基于这样的事实，给出的算法时间复杂的的一个排行如下所示：

```
O(1)常数阶 < O(logn)对数阶 < O(n)线性阶 < O(n^2)平方阶 < O(n^3)(立方阶) < O(2^n) (指数阶)
```

**「tip」**因为输入数据的不同，对于程序的运算时间也是有着很大影响的，当然对于正常来说如快排复杂度为O(nlogn)都是代表一般情况，而若是最坏情况就到了O($n^2$)，即不是严格的上界。因此面试中提的算法的时间复杂度指平均情况复杂度。

### 空间复杂度

「What」空间复杂度是对算法在运行过程中占用内存大小的量度，记做S(n)=O(f(n)。

空间复杂度(Space Complexity)记作S(n) 依然使用大O来表示。利用程序的空间复杂度，可以对程序运行中需要多少内存有个预先估计。

### 时间复杂度计算

时间复杂度计算以`O(2*n^2 + 10*n + 1000)`为例：

1. 去掉常数项，因为常数项不会随着n而增加而增加操作次数
2. 完成(1)后，`O(n^2 + n)`，最后仅保留最高项得到`O(n^2)`

对于递归复杂度 = 递归次数 \* 每次递归中的操作次数

### O(logn)是以什么为底？

logn是忽略底数的描述：

- $O(log_2n)$ = $log_2{10} * O(log_{10}n)$
- $log_i^n$ = $O(log_ij * log_jn)$ 其中$log_ij$是常数需要呗湖绿
- 这也是$O(log_in) -> O(logn)$缘由

> 假如有两个算法的时间复杂度，分别是log以2为底n的对数和log以10为底n的对数，那么这里如果还记得高中数学的话，应该不难理解`以2为底n的对数 = 以2为底10的对数 * 以10为底n的对数`。
>
> 而以2为底10的对数是一个常数，在上文已经讲述了我们计算时间复杂度是忽略常数项系数的。
>
> 抽象一下就是在时间复杂度的计算过程中，log以i为底n的对数等于log 以j为底n的对数，所以忽略了i，直接说是logn。

### 数据的存储方式

数据的存储方式只有两种：数组（顺序存储）和链表（链式存储）是数据结构的基础，而如栈、队列等都是在这之上的上层建筑。

| 存储方式 | 优点                             | 缺点                               |
| -------- | -------------------------------- | ---------------------------------- |
| 数组     | 根据索引可以直接访问所需任意节点 | 在初始化的时候就需要知道元素的数量 |
| 链表     | 所需的空间大小与元素个数成正比   | 需要引用遍历来访问任意元素         |

**二者的优缺点如下**：

**数组**由于是紧凑连续存储,可以随机访问，通过索引快速找到对应元素O(1)，而且相对节约存储空间。但正因为连续存储，内存空间必须一次性分配够，所以说数组如果要扩容，需要重新分配一块更大的空间，再把数据全部复制过去，时间复杂度 O(N)；而且你如果想在数组中间进行插入和删除，每次必须搬移后面的所有数据以保持连续，时间复杂度 O(N)。

**链表**因为元素不连续，而是靠指针指向下一个元素的位置，所以不存在数组的扩容问题；如果知道某一元素的前驱和后驱，操作指针即可删除该元素或者插入新元素，时间复杂度 O(1)。但是正因为存储空间不连续，你无法根据一个索引算出对应元素的地址，所以不能随机访问；而且由于每个元素必须存储指向前后元素位置的指针，会消耗相对更多的储存空间。

### 递归和迭代

|          | **定义**                                                     | **优点**                                                     | **缺点**                                                     |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 递归     | 程序调用自身的编程技巧称为递归                               | 1）大问题化为小问题,可以极大的减少代码量；2）用有限的语句来定义对象的无限集合.；3）代码更简洁清晰，可读性更好 | 1）递归调用函数,浪费空间；2）递归太深容易造成堆栈的溢出；    |
| 迭代     | 利用变量的原值推算出变量的一个新值，迭代就是A不停的调用B.    | 1）迭代效率高，运行时间只因循环次数增加而增加；2）没什么额外开销，空间上也没有什么增加， | 1） 不容易理解；2） 代码不如递归简洁；3） 编写复杂问题时困难。 |
| 二者关系 | 1） 递归中一定有迭代,但是迭代中不一定有递归,大部分可以相互转换。2） 能用迭代的不用递归,递归调用函数,浪费空间,并且递归太深容易造成堆栈的溢出./*相对*/ |                                                              |                                                              |

## 理论知识

刷题分类：

- 数组
- 链表
- 哈希表
- 字符串
- 双指针法
- 栈与队列
  - 单调栈
  - 单调队列
- 二叉树
- 回溯算法
- 贪心算法
- 动态规划

### 数组

**「What」**数组是存放在连续内存空间上的相同类型的数据的集合。

**特点：**

- 数组下标都是从0开始的
- 数组内存空间的地址是连续的

因为数组的内存空间是连续的，所以在进行删除或者增加元素的时候，就难免要移动其他元素的位置，且元素也是不能删除的，只能是覆盖。

### 链表

**「What」**链表是一种通过指针串联在一起的线性结构，每一个节点由两个部分组成，一个是数据域，一个是指针域（存放指向下一个节点的指针）。

**链表类型：**

- 单链表
- 双链表
- 循环链表：头尾指针相连

链表节点的定义：

```java
public class Node {
		int val;
  	Node next;
  	Node prev; // 若是双链表
}
```

**链表操作：**

- 删除节点：将删除节点的前一个节点指向删除节点的下一个节点；对于C++需要手动释放内存，但对于Java、Python有内存回收机制的就无需手动释放了。
- 添加节点：改变添加位置的节点指向即可

### 树

#### 二叉树

**「What」**每个结点至多有两个分支（即不存在分支度大于2）的树结构。通常分支被称为“左子树”和“右子树”，分支具有左右次序，不可被随意颠倒。

二叉树结点数可以为0，称空二叉树。

常见定义方式：

```Java
public class TreeNode {
    public int val;
    public TreeNode left;
    public TreeNode right;

    public TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }

    public TreeNode(int val) {
        this.val = val;
        left = null;
        right = null;
    }
}
```

**本身性质：**

- 结点数
- 深度

**特点：**

- 二叉树的第*i*层至多有$2^{i-1}$个结点
- 深度为*k*（k≥0）的二叉树至多总共有$2^{k+1}-1$个结点（定义根结点所在的深度为*k0=0）*，而总结拥有结点的数符合的，称“满二叉树”
- 在一颗二叉树中，若除最后一层外的其余层都是满的，并且要么最后一层是满的，要么右边结点缺少若干结点，称“完全二叉树”，具有n个结点完全二叉树的深度为$log2n+1$，且结点数≥$2^{k-1}$

二叉树通常作为数据结构应用，典型用法是对节点定义一个标记函数，将一些值与每个节点相关系。这样标记的二叉树就可以实现[二叉搜索树](https://zh.wikipedia.org/wiki/二元搜尋樹)和[二叉堆](https://zh.wikipedia.org/wiki/二元堆積)，并应用于高效率的搜索和排序。

**满二叉树性质：**

对于深度为k（k≥1）的满二叉树：

- 共有$2^k-1$个结点
- 结点个数一定为奇数（仅有根结点（k=1层）为一个结点）
- 第*i*层有$2^{i-1}$结点
- 有$2^{k-1}$个叶子

#### 二叉搜索树

**「What」**二叉搜索树（Binary Search Tree，BST）也称为二叉查找树、有序二叉树或者排序二叉树，指**一颗空树**或者具有以下性质的二叉树：

1. 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
2. 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
3. 任意节点的左、右子树也分别为二叉查找树；

BST的优势在于查找、插入的时间复杂度较低为$O(logn)$。BST是基础线性数据结构，用于构建更为抽象的数据结构。

搜索、插入、删除的复杂度等于树高，期望$O(logn)$，最坏为$O(n)$（数列有序，树会退化成线性表）。

改进版的二叉树可以使得树高为$O(logn)$，从降低最坏效率，如AVL树。

#### AVL树

**「What」**AVL（Adelson-Velsky and Landis Tree）定义：一种自平衡二叉树，在AVL树中，任一节点对应的两棵子树的最大高度差为1，因此它也被称为**高度平衡树**。查找、插入和删除在平均和最坏情况下的[时间复杂度](https://zh.wikipedia.org/wiki/时间复杂度)都是$O(logn)$，增加和删除元素的操作可能需要借助由一次或多次树旋转，来实现树的重新平衡。

**节点平衡因子**：由左子树的高度减去它的右子树高度（绝对值）≤1则被认为是平衡的。

#### 红黑树

**「What」**红黑树（Red-Black Tree）的定义：是一种**自平衡二叉查找树**，有着良好的最坏情况运行时间，并且在实践中高效：可以在$O(logn)$时间内完成查找、插入和删除，这里的*n*是树中的元素数目。红黑树的每个节点不同于AVL树，都是带有颜色属性的二叉查找树，颜色为红色或黑色。

**性质：**

1. 节点为red或black
2. 根为black
3. 所有叶子都是black（叶子是NIL节点）
4. 每个red节点必须有两个black的子节点（从每个叶子到根的所有路径上不能有两个red节点）
5. 从任一节点到每个叶子节点的所有叶子的所有简单路径都包含相同的black节点

![Image](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/algorithm/red-black-tree.png)

这些约束确保了红黑树的关键特性：从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这个树大致上是平衡的。因为操作比如插入、删除和查找某个值的最坏情况时间都要求与树的高度成比例，这个在高度上的理论上限允许红黑树在最坏情况下都是高效的，而不同于普通的二叉查找树。

### 回溯

**「What」**回溯其实就是递归，其本质是穷举，列出所有可能找出所需要的解。

回溯一般可以解决以下问题：

- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等问题

**「tip」**组合无序，排列有序。

所有回溯法解决的问题都可以抽象成树形结构，因为回溯法解决的就是在集合中递归查找子集，集合的大小就构成了递归的宽度即树的宽度，数据量的大小构成了递归的深度树的深度。

#### 回溯模版

解决一个回溯问题，实际就是一个决策树的遍历过程，只需要思考三个过程：

1. 路径：做出的选车
2. 选择列表：当前可以做的选择
3. 结束条件End Condition：达到决策树的底层，无法再做选择的条件

```python
result = []
def backtrack(trace[], select[]):
  if 满足结束条件：
  	result.add(trace[i])
    return
  for 选择 in select[]:
    do select
    backtrack(trace[next], select[])
    withdraw select
```

**其核心就是for循环里面的递归，在递归调用前“做选择”，在递归调用后“撤销选择”。**

### 贪心

**「What」**贪心的本质是选择每一阶段的局部最优，从而达到全局最优。

其难点在于，如何通过局部最优，从而推出整体最优，因此能使用的贪心的题目可以通过举反例，来判断是否可以使用贪心。即现手动模拟，若可以模拟则尝试贪心，若不可行，就可能需要动态规划了。

贪心的过程：

1. 将问题分解为若干个子问题
2. 找出适合贪心的策略
3. 求解每个子问题的最优解
4. 将局部解堆叠成全局最优解

贪心可以认为是动态规划的一个特例，相比动态规划，使用贪心的算法需要满足更多的条件（贪心性质），但是效率要高于动态规划。

### 动态规划

**「What」**动态规划Dynamic Programming，动态规划问题的一般形式是**求最值**，其求解的核心问题是**穷举**，但是这类穷举问题有点特别存在**重叠子问题**，若暴力穷举则效率低下，因此通过动态规划来避免不必要的计算。而且，动态规划一定**具备最优子结构**，才能通过子问题的最值的到原问题的最值。

动态规划的问题需要以下特点：

- **子问题重叠性质：**这类问题存在重叠子问题
- **最优子结构：**该问题可以通过最优子问题得到原问题的最优解
- **无后效性：**子问题的解一旦确定就不再更改，不受在这之后、包含它的更大问题的求解决策影响

思考状态转移方程的思维框架：

1. 这个问题的base case（最简单情况）是什么？
2. 这个问题有什么“状态”？
3. 对于每个“状态”，可以做什么“选择”使得“状态”发生改变？
4. 如何定义dp数组/函数的含义来表现“状态”和“选择”？

```python
# 初始化 base case
dp[0][0][...] = base
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

## 位运算

### 位操作符

- “&”，两个位都为1时才为1。
- “｜”，两个位都为0时才为0，否则为1。

- “^”异或，相同为0，不同为1。
- “～”取反，0->1,1->0。
- “<<”左移运算符，向左移位，高位丢弃，低位补0。
- “>>“右移运算符，向右移位，对无符号数，高位补0，对有符号数，高位补符号位。

### 常见位运算

- 乘除法：通过“<<1”,”>>1”来实现乘以2和除以。
- 数值交换：

```c
void swap(int &a, int &b) {
  	a ^= b;
  	b ^= a;
  	a ^= b;
}
```

- 判断奇偶性：`a&1==0`判断二进制的最后一位。

- 交换符号位：`return ~a + 1`整数取反加1，正好变成其对应的负数(补码表示)；负数取反加一，则变为其原码，即正数。

- 求绝对值：整数的绝对值是其本身，负数的绝对值正好可以对其进行取反加一求得，即我们首先判断其符号位（整数右移 31 位得到 0，负数右移 31 位得到 -1,即 0xffffffff），然后根据符号进行相应的操作。

```c
 int abs(int a) {
   	int i = a >> 31;
   	return i == 0 ? a : (~a + 1);
 }
```

- 高低位交换：如`a = (a >> 16) | (a << 16);`HashMap的源码中出现。

统计二进制中1的个数：

**x & (-x)：**保留二进制下最后出现的1的位置，其余位置置0（即一个数中最大的2的n次幂的因数  ）

- -x的运算是所有位置取反加1

**x & (x-1)：**消除二进制下最后出现1的位置，其余保持不变

- 可用于判断是否是2的幂次方（是否只出现了一个1）
- 二进制中1的个数

## 数组

### 二分查找

**以下场景为寻找一个数（基本的二分搜索）**

```java
public static int binarySearch(int[] nums, int target) {
  int l = 0, r = nums.length - 1;
  while (l <= r) {
    int mid = l + ((r - l) >> 1);
    if (target < nums[mid]) {
      r = mid - 1;
    } else if (target > nums[mid]) {
      l = mid + 1;
    } else if (target == nums[mid]) {
      return mid;
    }
  }
  return -1;
}
```

二分的几个要点：

- 计算mid时要防止溢出，`l + ((r - l) >> 1)`。
- 为什么while循环的条件中的是<=，而不是<？
  - 因为初始化right的赋值是nums.length-1，即最后一个元素的索引，而不是nums.length。
  - 而这个区间就是两端都闭的区间`[left, right]`，该区间就是每次都进行搜索的区间。
- 尽量统一写法，判断区间的不同，会导致边界更新的方式不同。

若存在重复元素，则可以进行边界查找，查找第一个出现的元素：

```java
    /**
     * 左边界查找 -》 左边第一个元素
     *
     * @param nums
     * @param target
     * @return
     */
    public static int leftBoundSearch(int[] nums, int target) {
        int l = 0, r = nums.length - 1;
        while (l <= r) {
            int mid = l + ((r - l) >> 1);
            if (target < nums[mid]) {
                r = mid - 1;
            } else if (target > nums[mid]) {
                l = mid + 1;
            } else if (target == nums[mid]) {
                r = mid - 1;
            }
        }
        if (l >= nums.length || nums[l] != target) {
            return -1;
        }
        return l;
    }

    /**
     * 右边界查找
     *
     * @param nums
     * @param target
     * @return
     */
    public static int rightBoundSearch(int[] nums, int target) {
        int l = 0, r = nums.length - 1;
        while (l <= r) {
            int mid = l + ((r - l) >> 1);
            if (target < nums[mid]) {
                r = mid - 1;
            } else if (target > nums[mid]) {
                l = mid + 1;
            } else if (target == nums[mid]) {
                l = mid + 1;
            }
        }
        if (r < 0 || nums[r] != target) {
            return -1;
        }
        return r;
    }

    /**
     * 搜索左右边界
     *
     * @param nums
     * @param target
     * @param isLeftOrRight true -> left falst -> right
     * @return
     */
    private int binarySearch(int[] nums, int target, boolean isLeftOrRight) {
        int l = 0, r = nums.length - 1;
        for (; l <= r; ) {
            int mid = l + ((r - l) >> 1);
            if (nums[mid] < target) {
                l = mid + 1;
            } else if (nums[mid] > target) {
                r = mid - 1;
            } else {
                if (isLeftOrRight) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }
        }
        if (isLeftOrRight && l < nums.length && nums[l] == target) {
            return l;
        } else if (!isLeftOrRight && r >= 0 && nums[r] == target) {
            return r;
        } else {
            return -1;
        }
    }
```

而上面算法能搜索到左侧边界的原因在于对`nums[mid] == target`的处理：

- 若为查找左边界，`right = mid - 1`收缩右侧边界。
- 若为查找右边界，`left = mid + 1`收缩左侧边界。

这里注意下边界检查，需要判断下是否过界。

#### 二分查找问题

**搜索旋转数组：**

```java
    public int search(int[] nums, int target) {
        int len = nums.length;
        if(len == 0) {
            return -1;
        }
        int l = 0, r = len - 1;
        for(;l<=r;) {
            int mid = l + ((r - l) >> 1);
            if(nums[mid] == target) {
                return mid;
            } else if(nums[mid] < nums[r]) {
                if(target > nums[mid] && target <= nums[r]) {
                    l = mid + 1;
                } else {
                    r = mid - 1;
                }
            } else {
                if(target < nums[mid] && target >= nums[l]) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }
        }
        return -1;
    }
```



### 双指针

#### 快慢指针

快慢指针主要解决链表中的问题，但也能在数组中有着巧妙的运用。

*reference：[27.移除元素](https://leetcode-cn.com/problems/remove-element/)*

该题的麻烦在于不能使用额外数组空间，但是不需要考虑数组中超出新长度后面的元素，因为数组在内存地中是连续的，不能单独删除数组中的某个元素，只能覆盖（这里磁盘的存储也是如此，所谓的删除仅是将相应的存储索引删除）。

首先考虑暴力，通过两次for循环遍历，一次遍历元素第二次循环更新数组。时间复杂度是$O(n^2)$，代码如下：

```java
    public int removeElement(int[] nums, int val) {
        int n = nums.length;
        for(int i=0; i<n; i++) {
            if(nums[i] == val) {
                for(int j=i+1; j<n; j++) {
                    nums[j-1] = nums[j];
                }
                i--;
                n--;
            }
        }
        return n;
    }
```

在数组操作的时候，可以考虑双指针（快慢指针），这里与快排`partitiion()`有着异曲同工之处：

```java
    private int partition(int[] arr, int left, int right) {
        int pivot = arr[right];
        int i = left - 1;
        for (int j = left; j <= right - 1; j++) {
            if(arr[j] <= pivot) {
                swap(arr, ++i, j);
            }
        }
        swap(arr, i + 1, right);
        return i + 1;
    }
```

```java
    public int removeElement(int[] nums, int val) {
        int n = nums.length;
        int i = 0;
        for(int j = i; j < n; j++) {
            if(nums[j] != val) {
                nums[i++] = nums[j];
            }
        }
        return i;
    }
```

以上复杂度为$O(n + nlogn)$。

#### 左右指针

左右指针主要解决数组（或者字符串中的问题），如二分查找这种。

*reference：[977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)*

一样首先考虑暴力，这里最简单就是for循环遍历一边平方处理，再进行数组排序，时间复杂度为$O(nlogn)$。

这里数组本身就是顺序的，平方后的变化在于负数，因此可以考虑左右指针，来进行处理：

```java
    public int[] sortedSquares(int[] nums) {
        int right = nums.length - 1;
        int left = 0;
        int[] result = new int[nums.length];
        int index = result.length - 1;
        while (left <= right) {
            if (nums[left] * nums[left] > nums[right] * nums[right]) {
                result[index--] = nums[left] * nums[left];
                ++left;
            } else {
                result[index--] = nums[right] * nums[right];
                --right;
            }
        }
        return result;
    }
```

时间复杂度降到了$O(n)$，但相应的空间复杂度相比暴力的$O(1)$变成了$O(n)$。

***题目：翻转数组***

一般的语言都会提供`reverse()`，而这也是双指针的用武之地：

```Java
for (int j = (n-1) >> 1; j >= 0; j--) {
  int k = n - j;
  byte cj = val[j];
  val[j] = val[k];
  val[k] = cj;
}
```

以上为*AbstractStringBuilder.java*中`reverse()`的一段截取。

### 滑动窗口

滑动窗口是双指针的一个进阶用法，即维护一个窗口不断滑动，然后更新答案。主要用于解决一大类字符串匹配问题。

其大致逻辑如下：

```java
int left = 0, right = 0;
while(right < s.length()) {
	window.add(s[right++]); // 扩大窗口
	
	while(window needs shrink) { // 缩小窗口
    res.add()； // 这里进行需要的结果记录
		window.remove(s[left++]);
	}
}
```

时间复杂度为$O(n)$，对于字符串的一些问题进行暴力处理来说效率高的多。

*reference：[76.最小覆盖子串（困难）](https://leetcode-cn.com/problems/minimum-window-substring)*

对于该问题使用暴力，大概如下：

```java
for (int i = 0; i < s.length(); i++)
    for (int j = i + 1; j < s.length(); j++)
        if s[i:j] 包含 t 的所有字母:
            更新答案

```

时间复杂度为$O(n^2)$，而滑动窗口算法如下：

1. 初始化：在字符串S中使用双指针的左右指针，初始化left=right=0，将索引的左闭合右开区间`[left, right)`称为**窗口**。
2. 窗口扩大：先不断扩大right，即扩大窗口，直到窗口中的值符合要求（包含T中的所有字符串）。
3. 达到收缩条件：停止增加right，转而增加left（缩小窗口），直到窗口中的字符串不再符合要求（不包含T的所有字符），同时每次增加left，都要更新一轮结果。
4. 重复(2)、(3)。

具体代码如下：

```java
class Solution {
    public String minWindow(String s, String t) {
        int n1 = s.length(), n2 = t.length();
        if(n1 == 0 || n2 == 0) {
            return "";
        }
        // 需要的元素 记录元素的数量，以来判断何时进行shrink
        HashMap<Character, Integer> needs = new HashMap<>();
        // 窗口
        HashMap<Character, Integer> window = new HashMap<>();
        for(char ch : t.toCharArray()) {
            needs.put(ch, needs.getOrDefault(ch, 0) + 1);
        }
        // validChar   start of result   len of result
        int valid = 0, start = 0, len = Integer.MAX_VALUE;
        for(int l = 0 , r = 0; r < n1;) {
            char ch = s.charAt(r);
            r++;
            
            if(needs.containsKey(ch)) {
                window.put(ch, window.getOrDefault(ch, 0) + 1);
                if(needs.get(ch).intValue() == window.get(ch).intValue()) { // 这里注意Java语言Integer缓存池问题
                    valid++;
                }
            }
            for(;valid == needs.size();l++) { // 达到条件
                char d = s.charAt(l);
                // 记录res结果值
                if(r - l < len) { 
                    len = r - l;
                    start = l;
                }
                if(needs.containsKey(d)) {
                    if(needs.get(d).intValue() == window.get(d).intValue()) {
                        valid--;
                    }
                    window.put(d, window.get(d) - 1);
                }
            }
        }
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }
}
```

### NSum

twoSum的关键在于对于无序数组，首先考虑把数组排序后再考虑双指针，而这里主要是考虑使用HashMap去处理无序数组相关问题。

两数之和：

```java
public int[] twoSum(int[] nums, int target) {
    int[] res = new int[2];
    if(nums == null || nums.length == 0){
        return res;
    }
    Map<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        int temp = target - nums[i];
        if(map.containsKey(temp)){
            res[1] = i;
            res[0] = map.get(temp);
        }
        map.put(nums[i], i);
    }
    return res;
}
```

三数之和为0：

```java
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        if (n < 3) {
            return res;
        }
        Arrays.sort(nums);
        for (int i = 0; i < n - 2; i++) {
            // 如果nums[i] > 0了，数组后面就不可能为0
            if (nums[i] > 0) {
                return res;
            }
            // 去重操作！！
            if (i != 0 && nums[i - 1] == nums[i]) {
                continue;
            }
            // 目标数为它的负数
            int target = -nums[i];
            // j为mid指针，k为right指针，进行移动
            for (int j = i + 1, k = n - 1; j < k; ) {
                int sum = nums[j] + nums[k];
                if (sum == target) {
                    res.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    // ！去重复操作，这是关键
                    while (j < k && nums[j + 1] == nums[j]) {
                        j++;
                    }
                    while (j < k && nums[k - 1] == nums[k]) {
                        k--;
                    }
                    j++;
                    k--;
                } else if (sum > target) {
                    k--;
                } else {
                    j++;
                }
            }
        }
        return res;
  	}
```

三数之和可以化解成，两数组之和加一次for迭代一个数，关键在于不让第一个数重复，至于后面的两个数，复用的towSum也保证它们不能重复。

因此对于NSum问题：

```java
    /**
     * N数之和
     *
     * @param nums
     * @param n 代表N个数之和
     * @param target NSum == target
     * @return
     */
    public List<List<Integer>> nSum(int[] nums, int n, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums.length == 0) {
            return res;
        }
        Arrays.sort(nums);
        return nSumTarget(nums, n, 0, target);
    }

    private List<List<Integer>> nSumTarget(int[] nums, int n, int start, int target) {
        int size = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        // 最少是n>2的情况
        if (n < 2 || size < n) {
            return res;
        }
        if (n == 2) { // 最后复用twoSum函数
            int lo = start, hi = size - 1;
            while (lo < hi) {
                List<Integer> ans = new ArrayList<>();
                int left = nums[lo], right = nums[hi];
                int sum = left + right;
                if (sum < target) {
                    while (lo < hi && nums[lo] == left) {
                        lo++;
                    }
                } else if (sum > target) {
                    while (lo < hi && nums[hi] == right) {
                        hi--;
                    }
                } else if (sum == target) {
                    ans.add(left);
                    ans.add(right);
                    res.add(ans);
                    while (lo < hi && nums[lo] == left) {
                        lo++;
                    }
                    while (lo < hi && nums[hi] == right) {
                        hi--;
                    }
                }
            }
        } else {
            for (int i = start; i < size; i++) { // n > 2，递归计算(n-1)Sum的结果
                List<List<Integer>> ret = nSumTarget(nums, n - 1, i + 1, target - nums[i]);
                for (List<Integer> arr : ret) {
                    arr.add(nums[i]);
                    res.add(arr);
                }
                while (i < size - 1 && nums[i] == nums[i + 1]) {
                    i++;
                }
            }
        }
        return res;
    }
```

## 题目补充

### 数字序列

```Java
    /**
     * 实现获取 下一个排列 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
     * 如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
     * 必须 原地 修改，只允许使用额外常数空间。
     * Q.1 2 3 4 5
     * A.1 2 3 5 4
     * Q.1 5 4 3 2
     * A. 2 1 3 4 5
     *
     * @param nums
     */
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        if (n <= 1) {
            return;
        }
        // 数组中升序的最后一位
        int l = 0, r = n - 1;
        for (int i = 0; i < n - 1; i++) {
            if (nums[i] < nums[i + 1]) {
                l = i;
            }
        }
        // 之后查找降序的第一位
        for (int i = l + 1; i < n; i++) {
            if (nums[i] > nums[l]) {
                r = i;
            }
        }
        // 交换两个位置的数
        swap(nums, l, r);
        // 进行排序 对降序的
        Arrays.sort(nums, l + 1, n);
    }
```

### 数组中第K大的数

```Java
   /**
     * 数组中第k大的元素
     *
     * @param nums
     * @param k
     * @return
     */
    public int findKthLargest(int[] nums, int k) {
        int heapSize = nums.length;
        buildMaxHeap(nums, heapSize);
        for (int i = nums.length - 1; i >= nums.length - k + 1; --i) {
            swap(nums, 0, i);
            --heapSize;
            maxHeapify(nums, 0, heapSize);
        }
        return nums[0];
    }

    public void buildMaxHeap(int[] a, int heapSize) {
        for (int i = heapSize / 2; i >= 0; --i) {
            maxHeapify(a, i, heapSize);
        }
    }

    public void maxHeapify(int[] a, int i, int heapSize) {
        int l = i * 2 + 1, r = i * 2 + 2, largest = i;
        if (l < heapSize && a[l] > a[largest]) {
            largest = l;
        }
        if (r < heapSize && a[r] > a[largest]) {
            largest = r;
        }
        if (largest != i) {
            swap(a, i, largest);
            maxHeapify(a, largest, heapSize);
        }
    }
```

### 数组中出现两次的元素

```Java
    /**
     * 给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。
     * 找到所有出现两次的元素。
     * 你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？
     * 注意1 ≤ a[i] ≤ n
     * 通过这个来在原地数组进行记录
     *
     * @param nums
     * @return
     */
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> ret = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            nums[(nums[i] - 1) % n] += n;
        }

        for (int i = 0; i < n; i++) {
            if (nums[i] > 2 * n) ret.add(i + 1);
        }
        return ret;
    }
```

### 搜索旋转数组

```Java
class Solution {
    public int search(int[] nums, int target) {
        int len = nums.length;
        if(len == 0) {
            return -1;
        }
        int l = 0, r = len - 1;
        for(;l<=r;) {
            int mid = l + ((r - l) >> 1);
            if(nums[mid] == target) {
                return mid;
            } else if(nums[mid] < nums[r]) {
                if(target > nums[mid] && target <= nums[r]) {
                    l = mid + 1;
                } else {
                    r = mid - 1;
                }
            } else {
                if(target < nums[mid] && target >= nums[l]) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }
        }
        return -1;
    }
}
```



