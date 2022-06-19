---
title: 算法(3)-二叉树
date: 2022-01-09 11:30:28.707
updated: 2022-01-14 13:08:15.766
categories: [算法]
tags: [算法]
---

# 算法(3)-二叉树

## Introduction

「What」每个结点至多有两个分支（即不存在分支度大于2）的树结构。通常分支被称为“左子树”和“右子树”，分支具有左右次序，不可被随意颠倒。

二叉树节点的定义：

```java
class TreeNode {
  	int val;
  	TreeNode left;
  	TreeNode right;
  	
    public TreeNode(int val, TreeNode left, TreeNode right) {
				this.val = val;
      	this.left = left;
      	this.right = right;
    }
}
```

### **二叉树性质**

1. 二叉树的第*i*层至多有${2^{i-1}}$个结点。
2. **空二叉树：**注意，二叉树的节点数为空。
3. **满二叉树：**深度为*k*（k≥0）的二叉树至多总共有$2^{k+1}-1$个结点（定义根结点所在的深度为*k0=0）*，而总结拥有结点的数符合的，称“满二叉树”。
   1. 节点个数：共有$2^k-1$个结点
   2. 节点奇偶：结点个数一定为奇数（仅有根结点（k=1层）为一个结点）
   3. i层节点个数：第*i*层有$2^{i-1}$结点
   4. 叶子个数：有$2^{k-1}$个叶子
4. **完全二叉树：**在一颗二叉树中，若除最后一层外的其余层都是满的，并且要么最后一层是满的，要么右边结点缺少若干结点，称“完全二叉树”，具有n个结点完全二叉树的深度为$log2n+1$，且结点数≥$2^{k-1}$。

### 二叉树的衍生

**二叉搜索树**

「What」二叉搜索树（Binary Search Tree）也称为二叉查找树、有序二叉树或者排序二叉树，指**一颗空树**或者具有以下性质的二叉树：

1. 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
2. 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
3. 任意节点的左、右子树也分别为二叉查找树；

BST的优势在于查找、插入的时间复杂度较低为O(logn)。BST是基础线性数据结构，用于构建更为抽象的数据结构。搜索、插入、删除的复杂度等于树高，期望O(logn)，最坏为O(n)（数列有序，树会退化成线性表）。改进版的二叉树可以使得树高为O(logn)，从降低最坏效率。如AVL树、

**AVL树**

「What」AVL（Adelson-Velsky and Landis Tree）一种自平衡二叉树，在AVL树中，任一节点对应的两棵子树的最大高度差为1，因此它也被称为高度平衡树。

- 查找、插入和删除在平均和最坏情况下的时间复杂度都是O(logn)，增加和删除元素的操作可能需要借助由一次或多次树旋转，来实现树的重新平衡。

- 节点平衡因子：由左子树的高度减去它的右子树高度（绝对值）≤1则被认为是平衡的。

**红黑树**

「What」红黑树（Red-Black Tree）是一种自平衡二叉查找树，有着良好的最坏情况运行时间，并且在实践中高效：可以在O(logn)时间内完成查找、插入和删除，这里的*n*是树中的元素数目。红黑树的每个节点不同于AVL树，都是带有颜色属性的二叉查找树，颜色为红色或黑色。

![Image](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/code-interview/red-black-tree.png)

1. 节点为red或black
2. 根为black
3. 所有叶子都是black（叶子是NIL节点）
4. 每个red节点必须有两个black的子节点（从每个叶子到根的所有路径上不能有两个red节点）
5. 从任一节点到每个叶子节点的所有叶子的所有简单路径都包含相同的black节点

这些约束确保了红黑树的关键特性：从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这个树大致上是平衡的。因为操作比如插入、删除和查找某个值的最坏情况时间都要求与树的高度成比例，这个在高度上的理论上限允许红黑树在最坏情况下都是高效的，而不同于普通的二叉查找树。

## 二叉树的遍历

- 前序遍历：根左右

- 中序遍历：左根右
- 后续遍历：左右根

二叉树的思想运用十分广泛，几乎涉及递归都可以抽象成二叉树的问题进行解决，树的所有问题几乎都离不开递归遍历的框架，而二叉树遍历的精髓其实也是如何写递归的关键：

1. 定义是什么，即该递归函数的最终结果是什么，我们相信这个函数定义，而不需要先过多考虑其细节。
2. base case：该函数何时终止，即终止条件。

根据函数定义递归调用子节点。

其回溯的模版：

````java
public void backtrack(Parameter p) {
  	if(终止条件) {
      return;
    }
  	过程
}
````

如计算二叉树的节点个数，将其分解，其实就是后序遍历，从叶子累加到根得出总节点数：

```java
// 定义：count(root) 返回以 root 为根的树有多少节点
int count(TreeNode root) {
    // base case
    if (root == null) return 0;
    // 自己加上子树的节点数就是整棵树的节点数（思考每个节点应该做什么）
    return 1 + count(root.left) + count(root.right);
}
```

#### 递归

```java
    private void traversal(TreeNode root) {
        if (root == null) {
            return;
        }
        // 前序处理位置
        traversal(root.left);
        // 中序处理位置
        traversal(root.right);
        // 后续处理位置
    }
```

层序遍历的递归版本：

```java
    public void levelOrderRec(TreeNode root, int height, List<List<Integer>> res) {
        if (root == null) {
            return;
        }
        if (height >= res.size()) {
            res.add(new ArrayList<>());
        }
        res.get(height).add(root.val);
        levelOrderRec(root.left, height + 1, res);
        levelOrderRec(root.right, height + 1, res);
    }
```

#### 迭代

前序遍历：

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        for (TreeNode cur = root; cur != null || !stack.isEmpty(); cur = cur.right) {
            for (; cur != null; cur = cur.left) {
                // 记录根
                res.add(cur.val);
                stack.push(cur);
            }
            cur = stack.pop();
        }
        return res;
    }
```

中序遍历：

```java
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        for (TreeNode cur = root; cur != null || !stack.isEmpty(); cur = cur.right) {
            for (; cur != null; cur = cur.left) {
                stack.push(cur);
            }
            cur = stack.pop();
            // 记录
            res.add(cur.val);
        }
        return res;
    }
```

后序遍历与前两种不同，因为最后才遍历根，而遍历根前需要遍历右，因此需要对父亲节点进行记录，否则没法向后迭代：

```java
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        for (TreeNode cur = root, pre = null; cur != null || !stack.isEmpty(); ) {
            for (; cur != null; cur = cur.left) {
                stack.push(cur);
            }
            cur = stack.pop();
            // 若无右节点 -》 记录
            // 若pre==cur -》 到根 -》 记录 
            // 否则继续向右迭代
            if (cur.right == null || cur.right == pre) {
                res.add(cur.val);
                pre = cur;
                cur = null; // 保证不会向左
            } else {
                stack.push(cur);
                cur = cur.right;
            }
        }
        return res;
    }
```

**层序遍历**即BFS，关键在于通过队列进行每一层节点的记录，同时层序遍历一般用于找最短的问题上：

```java
    public List<List<Integer>> levelOrderTraverse(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int len = queue.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < len; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            res.add(list);
        }
        return res;
    }
```

### 遍历二叉树的神级方法

> 给定一颗二叉树头节点head，完成二叉树的先序、中序和后序遍历。如果二叉树的节点为N，则要求时间复杂度为O(N)，额外空间复杂度为O(1)。

- 本题的难度在于复杂度的要求，特别是空间复杂度，因为前面的方法无论是迭代还是递归都需要到O(h)的空间复杂度（h指树高，函数栈的深度）。

**思路**

1. 完全不用栈结构来实现三种遍历的方法——**Morris遍历**。
2. 二叉树的遍历的关键在于处理完某个节点后回到上层节点，这也是无论递归还是迭代都用到了栈结构来存储上层指针。
3. Morris遍历的实质也就是避免使用栈结构，而是让下层到上层有指针。通过让底层节点指向null的空闲指针返回上层的某个节点，从而完成下层到上层的移动。
4. tips：空闲指针，如某些节点没有右子节点，则这些节点称为空闲指针。
5. *Morries的遍历过程：*假设当前节点为cur，base case状态时cur就是树的头节点
   1. 若cur==null，停止整个过程。
   2. 若cur.left==null，cur=cur.right。
   3. 若cur.left!=null，cur=mostRight(cur.left)找到cur.left最右的节点，记mostRight。以下用代码进行表示

```java
if(mostRight.right == null) {
  	mostRight.right = cur;
  	cur = cur.left;
} else if(mostRight.right == cur) {
  	mostRight.right = null;
  	cur = cur.right;
}
```

**实现**

Morries遍历:

```java
    public void morrisTraverse(TreeNode root) {
        if (root == null) {
            return;
        }
        TreeNode cur = root;
        TreeNode mostRight = null;
        for (; cur != null; ) {
            System.out.print(cur.val + " ");
            mostRight = cur.left;
            if (mostRight != null) {
                for (; mostRight.right != null && mostRight.right != cur;
                     mostRight = mostRight.right) {
                }
                if (mostRight.right == null) {
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                } else {
                    mostRight.right = null;
                }
            }
            cur = cur.right;
        }
    }
```

根据Morris可以加工出前中后序三种遍历方法：

- MorriesPre: 
  - 对于cur只到达一次的节点（无左子树的节点），cur到达直接打印。「左」
  - 对于cur可以到达两次的节点（有左子树的节点），cur第一次到时进行打印。「根->左」
- MorriesIn:
  - 对于cur只到达一次的节点（无左子树的节点），cur到达直接打印。「左」
  - 对于cur可以到达两次的节点（有左子树的节点），cur第二次到时进行打印。「根->右」（注意，这里是和前序的区别）
- *MorriesPost：（该操作较为复杂）*
  - 对于cur只到达一次的节点（无左子树的节点），直接跳过。
  - 对于cur可以到达两次的节点（有左子树的节点X），cur第一次到达X没有打印行为；当第二次到达X，逆序打印X左子树的右边界。
  - cur遍历完成，逆序打印整个右边界。

**MorriesPre:**

```java
    public void morrisPre(TreeNode root) {
        if (root == null) {
            return;
        }
        TreeNode cur = root;
        TreeNode mostRight = null;
        for (; cur != null; ) {
            mostRight = cur.left;
            if (mostRight != null) {
                for (; mostRight.right != null && mostRight.right != cur;
                     mostRight = mostRight.right) {
                }
                if (mostRight.right == null) {
                    mostRight.right = cur;
                    // 第一次到达
                    System.out.print(cur.val + " ");
                    cur = cur.left;
                    continue;
                } else {
                    mostRight.right = null;
                }
            } else {
                // 无左子树的节点
                System.out.print(cur.val + " ");
            }
            cur = cur.right;
        }
    }
```

**MorriesIn:**

```java
    public void morrisIn(TreeNode root) {
        if (root == null) {
            return;
        }
        TreeNode cur = root;
        TreeNode mostRight = null;
        for (; cur != null; ) {
            mostRight = cur.left;
            if (mostRight != null) {
                for (; mostRight.right != null && mostRight.right != cur;
                     mostRight = mostRight.right) {
                }
                if (mostRight.right == null) {
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                } else {
                    mostRight.right = null;
                }
            }
            // 无左子树的节点 | 第二次到达也会执行该步骤
            System.out.print(cur.val + " ");
            cur = cur.right;
        }
    }
```

**MorriesPost:**

```java
    public void morrisPost(TreeNode root) {
        if (root == null) {
            return;
        }
        TreeNode cur = root;
        TreeNode mostRight = null;
        for (; cur != null; ) {
            mostRight = cur.left;
            if (mostRight != null) {
                for (; mostRight.right != null && mostRight.right != cur;
                     mostRight = mostRight.right) {
                }
                if (mostRight.right == null) {
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                } else {
                    mostRight.right = null;
                    // 第二次到达
                    printRightEdge(cur.left);
                }
            }
            cur = cur.right;
        }
        // 遍历完成
        printRightEdge(root);
    }

    private static void printRightEdge(TreeNode root) {
        TreeNode tail = reverseRightEdge(root);
        TreeNode cur = tail;
        for(; cur != null; cur = cur.right) {
            System.out.print(cur.val + " ");
        }
        // 复原树
        reverseRightEdge(tail);
    }
    // 有点链表翻转的味道
    private static TreeNode reverseRightEdge(TreeNode node) {
        TreeNode pre = null;
        for(TreeNode tmp = null; node != null;) {
            tmp = node.right;
            node.right = pre;
            pre = node;
            node = tmp;
        }
        return pre;
    }
```

**测试结果**

```
原序：1,2,3,null,null,4,5
[1][2][3, 4][5]
前序：12345
中序：32541
后序：35421
Morris遍历顺序：
第一次遇到情况:1 
第一次遇到情况:2 
无左节点情况:3 
第二次到达情况:2 
第一次遇到情况:4 
无左节点情况:5 
第二次到达情况:4 
第二次到达情况:1 
======================
前序：
1 2 3 4 5 
======================
中序：
3 2 5 4 1 
======================
后序：
3 5 4 2 1 
```

## 常用操作

### 二叉树的深度

#### 最大深度

> #### [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
>
> 难度简单1080
>
> 给定一个二叉树，找出其最大深度。
>
> 二叉树的深度为**根节点到最远叶子节点**的最长路径上的节点数。

**实现**

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

#### 最小深度

> #### [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)
>
> 难度简单648
>
> 给定一个二叉树，找出其最小深度。
>
> 最小深度是**从根节点到最近叶子节点**的最短路径上的节点数量。

**思路**

1. 一般最小深度可以很快想到BFS，从而的得到结果。当遍历到叶子节点时候，返回当前层数，也就是最小深度了。
2. 若使用DFS`Math.min(minDepth(root.left), minDepth(root.right)) + 1`，单纯计算左右最短的话，就会导致，最小深度为没有左子树的分支为最短深度。这也该问题的关键。（注意是到叶子节点）
3. 因此需要单独判断。

**实现**

Method1:BFS

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        return minDepth(root, 1);
    }

    private int minDepth(TreeNode root, int level) {
        // base case: leaf node
        if(root.left == null && root.right == null) {
            return level;
        }
        int ans = Integer.MAX_VALUE;
        if(root.left != null) {
            ans = Math.min(ans, minDepth(root.left, level + 1));
        }
        if(root.right != null) {
            ans = Math.min(ans, minDepth(root.right, level + 1));
        }
        return ans;
    }
}
```

Method2:DFS

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        int l = minDepth(root.left);
        int r = minDepth(root.right);
        if(root.left != null && root.right == null) {
            return 1 + l;
        }
        if(root.right != null && root.left == null) {
            return 1 + r;
        }
        return 1 + Math.min(l, r);
    }
}
```

### 翻转二叉树

> #### [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)
>
> 难度简单1122
>
> 翻转一棵二叉树。

**实现**

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root == null) {
            return null;
        }
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;
        invertTree(root.left);
        invertTree(root.right);
        return root;
    }
}
```

- 只要将每个节点进行一次翻转，就能得到完整的翻转二叉树。

### 对称二叉树

> #### [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)
>
> 难度简单1695
>
> 给你一个二叉树的根节点 `root` ， 检查它是否轴对称。

**思路**

1. 该题关键在于，判断的时候需要*跨父节点*进行，因此单参数的递归就没法实现了。

**实现**

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null) {
            return true;
        }
        return isSymmetric(root.left, root.right);
    }

    private boolean isSymmetric(TreeNode l, TreeNode r) {
        if(l == null && r == null) {
            return true;
        }
        if(l == null || r == null) {
            return false;
        }
        return l.val == r.val
            && isSymmetric(l.right, r.left)
            && isSymmetric(l.left, r.right);
    }
}
```

### 平衡二叉树

> #### [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)
>
> 难度简单855
>
> 给定一个二叉树，判断它是否是高度平衡的二叉树。
>
> 本题中，一棵高度平衡二叉树定义为：
>
> > 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

**思路**

1. 通过计算最大和最小的深度来进行判断。
2. 为了减少两次计算的复杂，其实只需要计算root.left和root.right的高度来同时计算差值即可。

**实现**

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return height(root) >= 0;
    }

    public int height(TreeNode root) {
        if(root == null) {
            return 0;
        }
        int lh = height(root.left);
        int rh = height(root.right);
        if(lh >= 0 && rh >= 0 && Math.abs(lh - rh) <= 1) {
            return Math.max(lh, rh) + 1;
        }
        return -1;
    }
}
```

- 本质通过后序遍历，自底向上，若都高度差>=1了，则最后递归到顶必然不可能>=0。

### 填充每个节点的下一个右侧节点

> #### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)
>
> 难度中等639
>
> 给定一个 **完美二叉树** ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：
>
> ```
> struct Node {
> int val;
> Node *left;
> Node *right;
> Node *next;
> }
> ```
>
> 填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。
>
> 初始状态下，所有 next 指针都被设置为 `NULL`。
>
> 
>
> **进阶：**
>
> - 你只能使用常量级额外空间。
> - 使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。

**思路**

1. 对于同一棵树下的节点可以很简单的进行填充，但该题难点在于不同树节点的下一个节点填充。
2. Method1:而每一层节点的填充，可以很快想到层序遍历，但这会开辟额外的空间出来。
3. Method2:一个节点很难完成不同树节点的连接，但是可以通过两个节点参数来实现，且最后侧节点必定为NULL，无需我们操作。

**实现**

Method2:

```java
class Solution {
    public Node connect(Node root) {
        if(root == null) {
            return null;
        }
        connect(root.left, root.right);
        return root;
    }
    
    private void connect(Node l, Node r) {
        if(l == null || r == null) {
            return;
        }
        l.next = r;
        connect(l.left, l.right);
        connect(r.left, r.right);
        connect(l.right, r.left);
    }
}
```

- 该题需要学会，若进行*跨父节点*的操作时，只依赖一个节点肯定是没法做到的。

### 寻找重复子树

> #### [652. 寻找重复的子树](https://leetcode-cn.com/problems/find-duplicate-subtrees/)
>
> 难度中等354
>
> 给定一棵二叉树，返回所有重复的子树。对于同一类的重复子树，你只需要返回其中任意**一棵**的根结点即可。
>
> 两棵树重复是指它们具有相同的结构以及相同的结点值。

**思路**

1. 对于一个节点，想要知道以自己为根的树是不是重复，需要知道什么？
   1. 以自己为根的树长啥样？
   2. 其他节点为根的树长啥样？
2. 如何才能知道自己长啥样？
   1. 后序遍历，通过左右最后到自己就知道长啥样了。
3. 如何知道别人长啥样？
   1. 记录，序列化结果存储。

**实现**

```java
class Solution {
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        if(root == null) {
            return res;
        }
        findDuplicateSubtrees(root, new HashMap<>(), res);
        return res;
    }

    private String findDuplicateSubtrees(TreeNode root, Map<String, Integer> rec, List<TreeNode> res) {
        if(root == null) {
            return "#";
        }
        String left = findDuplicateSubtrees(root.left, rec, res);
        String right = findDuplicateSubtrees(root.right, rec, res);
        String tree = left + "," + right + "," + root.val;
        if(rec.containsKey(tree)) {
            if(rec.get(tree) == 1) {
                res.add(root);
            }    
        }
        rec.put(tree, rec.getOrDefault(tree, 0) + 1);
        return tree;
    }
}
```

### 树的子结构

> #### [剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)
>
> 难度中等436
>
> 输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
>
> B是A的子结构， 即 A中有出现和B相同的结构和节点值。

**思路**

1. 在子树中进行遍历判断子树结构，可以想到递归的嵌套。
2. base case: 当B为null的时候说明递归完成，返回true。

**实现**

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if(A == null || B == null) {
            return false;
        }
        return isSub(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }

    private boolean isSub(TreeNode A, TreeNode B) {
        if(B == null) {
            return true;
        }
        if(A == null) {
            return false;
        }
        return A.val == B.val && isSub(A.left, B.left) && isSub(A.right, B.right);
    }
}
```

- 需要注意在`isSub()`中B与A判断顺序，因为B和A有可能同时到底。

### 二叉树转换为链表

> #### [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)
>
> 难度中等1031
>
> 给你二叉树的根结点 `root` ，请你将它展开为一个单链表：
>
> - 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
> - 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

**思路**

因为展开后的单链表要与二叉树的先序遍历相同，思路如下：

1. 将 `root` 的左子树和右子树拉平。
2. 将 `root` 的右子树接到左子树下方，然后将整个左子树作为右子树。

**实现**

```java
    public void flatten(TreeNode root) {
        if(root == null) {
            return ;
        }
        flatten(root.left);
        flatten(root.right);
        // 1、这时左右子树已经拉成一条链表
        TreeNode left = root.left;
        TreeNode right = root.right;
        // 2、将左子树作为右子树
        root.left = null;
        root.right = left;
        // 3、将原先的右子树接到当前右子树的末端
        TreeNode p = root;
        while (p.right != null) {
            p = p.right;
        }
        p.right = right;
    }
```

## 经典问题

### 根据遍历序列构建二叉树问题

该类问题的关键在于利用前序或是后序，来确定根，并通过中序遍历特性（左根右）找到根的位置以此来划分子树区间。

#### 从前序和中序遍历构造二叉树

> #### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
>
> 难度中等1368
>
> 给定一棵树的前序遍历 `preorder` 与中序遍历 `inorder`。请构造二叉树并返回其根节点。

**思路**

1. 通过前序（根左右）来确定root，而中序遍历（左根右）在知道root的值后确定在中序的rootIndex即可找到root左右子树区间。
2. 通过HashMap来记录每个值在中序数组中的坐标。

**实现**

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0; i<inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(preorder, 0, preorder.length - 1,
        inorder, 0, inorder.length - 1, map);
    }

    private TreeNode buildTree(int[] preorder, int pStart, int pEnd,
    int[] inorder, int iStart, int iEnd, Map<Integer, Integer> map) {
        if(pStart > pEnd || iStart > iEnd) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[pStart]);
        if(iStart != iEnd) {
            int rootIndex = map.get(root.val);
            int leftSize = rootIndex - iStart;
            int rightSize = iEnd - rootIndex;
            root.left = buildTree(preorder, pStart + 1, pStart + leftSize,
            inorder, iStart, rootIndex - 1, map);
            root.right = buildTree(preorder, pEnd - rightSize + 1, pEnd,
            inorder, rootIndex + 1, iEnd, map);
        }
        return root;
    }
}
```

- 以上注意在递归调用时，遍历区间均为闭区间。如[pStart+1, pStart+leftSize]这里无需再进行加一的。因为根节点的缘故，需要往右推1位，原式的话应该是`pStart+1+leftSize-1`。
- `iStart!=iEnd`说明只有一个节点了，直接返回即可。

#### 从中序和后序遍历构造二叉树

> #### [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
>
> 难度中等647
>
> 根据一棵树的中序遍历与后序遍历构造二叉树。
>
> **注意:**
> 你可以假设树中没有重复的元素。

**思路**

1. 该题与上题基本一样，在实现的时候只是从后往前来寻找root节点。
2. 可以套用上题的模版。

**实现**

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0; i<inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(inorder, 0, inorder.length - 1,
        postorder, 0, postorder.length - 1, map);
    }

    private TreeNode buildTree(int[] inorder, int iStart, int iEnd,
    int[] postorder, int pStart, int pEnd, Map<Integer, Integer> map) {
        if(iStart > iEnd || pStart > pEnd) {
            return null;
        } 
        TreeNode root = new TreeNode(postorder[pEnd]);
        if(iStart != iEnd) {
            int rootIndex = map.get(root.val);
            int leftSize = rootIndex - iStart;
            int rightSize = iEnd - rootIndex;
            root.left = buildTree(inorder, iStart, rootIndex - 1,
            postorder, pStart, pStart + leftSize - 1, map);
            root.right = buildTree(inorder, rootIndex + 1, iEnd,
            postorder, pEnd - rightSize, pEnd - 1, map);
        }
        return root;
    }
}
```

- 注意：这里也是一样的，涉及到后序的index需要考虑-1的情况。如这里[pStart, pStart+leftSize-1]，因为整体序列向左推1位，`pEnd-rightSize=pEnd-1-(rightSize-1)`。

#### 根据前序和后序构造二叉树

> #### [889. 根据前序和后序遍历构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)
>
> 难度中等210
>
> 返回与给定的前序和后序遍历匹配的任何二叉树。
>
>  `pre` 和 `post` 遍历中的值是不同的正整数。

**思路**

1. 该题不同于上面两题，上面两题的关键在于利用中序的特性，而该题无法使用中序。该题的关键如何通过前序和后序来进行子树的划分。
2. 若序列如下：

> **示例：**
>
> ```
> 输入：pre = [1,2,4,5,3,6,7], post = [4,5,2,6,7,3,1]
> 输出：[1,2,3,4,5,6,7]
> ```

若进行观察可以发现：

1. 在前序中，若root==1时，2为root.left子树的根。
2. 在后序中，若root==1时，3位root.right子树的根。
3. 因此在第一次递归的时候可以按根左右的方式划分出[1\[2,4,5][3,6,7]]的序列。后序同理[[4,5,2]\[6,7,3]1]。

**实现**

```java
class Solution {
    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0; i<postorder.length; i++) {
            map.put(postorder[i], i);
        }
        return constructFromPrePost(preorder, 0, preorder.length - 1,
        postorder, 0, postorder.length - 1, map);
    }

    private TreeNode constructFromPrePost(int[] preorder, int preL, int preR,
    int[] postorder, int postL, int postR, Map<Integer, Integer> map) {
        if(preL > preR || postL > postR) {
            return null;
        }
        TreeNode root = new TreeNode(preorder[preL]);
        if(preL != preR) {
            int nextIndex = map.get(preorder[preL + 1]);
            int leftSize = nextIndex - postL + 1;
            int rightSize = postR - nextIndex - 1;
            root.left = constructFromPrePost(preorder, preL + 1, preL + leftSize,
            postorder, postL, nextIndex, map);
            root.right = constructFromPrePost(preorder, preR - rightSize + 1, preR,
            postorder, nextIndex + 1, postR - 1, map);
        }
        return root;
    }
}
```

- **只有每个节点度为2或者0的时候前序和后序才能唯一确定一颗二叉树，只有一个子节点是无法确定的，因为你无法判断他是左子树还是右子树**。
- 这里nextIndex是下一个根节点在后序中的位置。
- 该做法需要许多细节，主要也是和上面两个一样，闭区间的构造，如`preL+leftSize=preL+1+leftSize-1`。

### 二叉树路径问题

路径表示根节点到叶子节点的经过节点。

#### 路径总和

> #### [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)
>
> 难度简单760
>
> 给你二叉树的根节点 `root` 和一个表示目标和的整数 `targetSum` 。判断该树中是否存在 **根节点到叶子节点** 的路径，这条路径上所有节点值相加等于目标和 `targetSum` 。如果存在，返回 `true` ；否则，返回 `false` 。
>
> **叶子节点** 是指没有子节点的节点。

**思路**

1. 根节点到叶子节点，因此无需进行跨父节点操作，设计递归函数的时候为单参数。
2. 思考返回条件：
   1. base case: root==null->false
   2. 到达叶子节点，且路径和等于targetSum->true
   3. 因为是判断存在，在逻辑判断的时候使用`||`。

**实现**

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if(root == null) {
            return false;
        }
        targetSum -= root.val;
        if(root.left == null && root.right == null && targetSum == 0) {
            return true;
        }
        return hasPathSum(root.left, targetSum) || hasPathSum(root.right, targetSum); 
    }
}
```

#### 路径总和II

> #### [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)
>
> 难度中等649
>
> 给你二叉树的根节点 `root` 和一个整数目标和 `targetSum` ，找出所有 **从根节点到叶子节点** 路径总和等于给定目标和的路径。
>
> **叶子节点** 是指没有子节点的节点。

**实现**

1. 其实就是正常在二叉树中使用DFS。

**思路**

```java
class Solution {
    private List<List<Integer>> res;
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        res = new ArrayList<>();
        if(root == null) {
            return res;
        }
        findPath(root, targetSum, new ArrayList<>());
        return res;
    }

    private void findPath(TreeNode root, int targetSum, List<Integer> tmp) {
        if(root == null) {
            return;
        }
        targetSum -= root.val;
        tmp.add(root.val);
        if(root.left == null && root.right == null && targetSum == 0) {
            res.add(new ArrayList<>(tmp));
        } else {
            findPath(root.left, targetSum, tmp);
            findPath(root.right, targetSum, tmp);
        }
        tmp.remove(tmp.size() - 1);
    }
}
```

#### 路径总和III

> #### [437. 路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)
>
> 难度中等1188
>
> 给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。
>
> **路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

**思路**

1. 该题的不同在于，*路径不再是从根节点开始了且无需到达叶子节点*，但仍然是从父节点到子的顺序。
2. 因此在遍历方式上，仍然是使用前序，**关键在于，对于每个子树需要使用递归调用，进行递归嵌套。**

**实现**

```java
class Solution {
    int ans = 0;
    public int pathSum(TreeNode root, int targetSum) {
        if(root == null) {
            return ans;
        }
        findPath(root, targetSum);
        pathSum(root.left, targetSum);
        pathSum(root.right, targetSum);
        return ans;
    }
    
    private void findPath(TreeNode root, int targetSum) {
        if(root == null) {
            return;
        }
        targetSum -= root.val;
        if(targetSum == 0) {
            ans++;
        } 
        findPath(root.left, targetSum);
        findPath(root.right, targetSum);
    }
}
```

#### 二叉树的所有路径

> #### [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)
>
> 难度简单630
>
> 给你一个二叉树的根节点 `root` ，按 **任意顺序** ，返回所有从根节点到叶子节点的路径。
>
> **叶子节点** 是指没有子节点的节点。

**实现**

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if(root == null) {
            return res;
        }
        binaryTreePaths(root, "", res);
        return res;
    }

    private void binaryTreePaths(TreeNode root, String str, List<String> res) {
        if(root == null) {
            return;
        }
        str += root.val;
        if(root.left == null && root.right == null) {
            res.add(str);
        } else {
            str += "->";
            binaryTreePaths(root.left, str, res);
            binaryTreePaths(root.right, str, res);
        }
    }
}
```

#### 二叉树中的最大路径和

> #### [124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
>
> 难度困难1369
>
> **路径** 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。
>
> **路径和** 是路径中各节点值的总和。
>
> 给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

**思路**

1. 注意：树中任意节点沿父节点-子节点连接到任意节点的序列，且路径方式只能为一次。
2. 为了保证路径序列的唯一，且按照该方式遍历，可以观察出使用后序遍历，在遍历完左右节点后必然会到达根节点。
3. base case：`root==null;return 0;`
4. 非空节点的最大贡献=节点值+其子节点值贡献的最大值（对于叶子就是其本身）

**实现**

```java
class Solution {
    private int res = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        getMax(root);
        return res;
    }

    private int getMax(TreeNode root) {
        if(root == null) {
            return 0;
        }
        int left = Math.max(0, getMax(root.left));
        int right = Math.max(0, getMax(root.right));
        int path = left + right + root.val;
        res = Math.max(path, res);
        return Math.max(left, right) + root.val;
    }
}
```

### 最近公共祖先问题

#### 二叉树的最近公共祖先

> #### [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
>
> 难度中等1478
>
> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
>
> [百度百科](https://baike.baidu.com/item/最近公共祖先/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

**思路**

1. *函数是干什么的？*
   1. 若p，q都位于同一个root的树上，函数则返回的是该root。
   2. 若p，q都不在root的树上，函数返回null。
   3. 若p，q有一个在root为根的树上，函数返回的就是该节点。
2. *函数参数的变量状态？*p，q是不会变化的，只会对root进行递归调用。
3. *函数递归调用结果？*base case：
   1. 如果root为null，则肯定返回null。
   2. 如果root==p||root==q，则返回root。

**实现**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if(left != null && right != null) {
            return root;
        }
        return left != null ? left : right;
    }
}
```

- 关键在于递归调用后的处理（后序遍历处理的位置），left和right的判断，如果p，q都在以root为根的树上，则left和right一定分别是p和q。

#### 二叉搜索树的最近公共祖先

> #### [235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
>
> 难度简单737
>
> 给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。
>
> [百度百科](https://baike.baidu.com/item/最近公共祖先/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

**思路**

1. 该题完全可以用上题的方式进行解决，但是如何利用BST的特性去解决该问题呢？
2. 如果两个节点值都比当前节点值小，则说明最近公共祖先在左边。否则相反。

**实现**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        if (root.val > p.val && root.val > q.val) {
            return lowestCommonAncestor(root.left, p, q);
        }
        if (root.val < p.val && root.val < q.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        return root;
    }
}
```

### 完全二叉树的节点个数

> #### [222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)
>
> 难度中等602
>
> 给你一棵 **完全二叉树** 的根节点 `root` ，求出该树的节点个数。
>
> [完全二叉树](https://baike.baidu.com/item/完全二叉树/7773232?fr=aladdin) 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 `h` 层，则该层包含 `1~ 2h` 个节点。

**思路**

1. 若输入一个普通树，计算节点个数，只需如此遍历一遍即可：`return countNodes(root.left) + countNodes(root.right) + 1;`。时间复杂度为O(N)。
2. 若输入一个满二叉树full binary tree，只需计算树高h，而节点总数就是$2^h-1$。
3. 而完全二叉树是普通二叉树与满二叉树之间，它由于性质，两颗子树一定存在一颗满二叉树。

**实现**

```java
class Solution {
    public int countNodes(TreeNode root) {
        TreeNode l = root, r = root;
        int hl = 0, hr = 0;
        while(l != null) {
            l = l.left;
            hl++;
        }
        while(r != null) {
            r = r.right;
            hr++;
        }
        if(hl == hr) {
            return (int)Math.pow(2, hl) - 1;
        }
        return 1 + countNodes(root.left) + countNodes(root.right);
    }
}
```

- 关键在于两个递归不会一个真的递归下去。时间复杂度为O(logNlogN)，遍历树高O(logN)\*算法递归深度O(logN)。

### 二叉树序列化和反序列化

> #### [297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)
>
> 难度困难727
>
> 序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。
>
> 请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

**实现**

```java
public class TreeEncodeAndDecode {
    String SEP = ",";
    String NULL = "#";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serialize(root, sb);
        return sb.toString();
    }

    public void serialize(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append(NULL).append(SEP);
            return;
        }
      	// 前序方式序列化
        sb.append(root.val).append(SEP);
        serialize(root.left, sb);
        serialize(root.right, sb);
      	// 后序方式序列化位置
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        List<String> nodes = new LinkedList<>(Arrays.asList(data.split(SEP)));
        return deserialize(nodes);
    }

    public TreeNode deserialize(List<String> nodes) {
        if (nodes.isEmpty()) {
            return null;
        }
        String node = nodes.remove(0);
      	// String node = nodes.removeLast(); 后序方式
        if (NULL.equals(node)) {
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(node));
      	// 若是后序的方式，需要先构建右子树，再构建左子树
        root.left = deserialize(nodes);
        root.right = deserialize(nodes);
        return root;
    }
}
```

更加具体序列化讲解：请[参考此处](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485871&idx=1&sn=bcb24ea8927995b585629a8b9caeed01&scene=21#wechat_redirect)。

- 以上为使用前序进行序列化和反序列化。
- 注意：无法使用中序进行序列化，因为无法确定root的位置。
- 使用后序反序列化是，注意需要先构建右侧子树再左侧子树。

## 二叉搜索树问题

「What」二叉搜索树(Binary Search Tree,BST)，在一个二叉树中，任意节点的值大于等于左子树所有节点的值，且要小于等于右子树的所有节点的值。

根据二叉搜索树的性质不同，因此递归的框架也需要有些改变：

```java
public void BST(TreeNode root, int target) {
  	if(root.val == target) {
      	// 找到
    }
  	if(root.val < target) {
      	BST(root.right, target);
    }
  	if(root.val > target) {
      	BST(root.left, target);
    }
}
```

### 验证二叉搜索树

> #### [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)
>
> 难度中等1379
>
> 给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。
>
> **有效** 二叉搜索树定义如下：
>
> - 节点的左子树只包含 **小于** 当前节点的数。
> - 节点的右子树只包含 **大于** 当前节点的数。
> - 所有左子树和右子树自身必须也是二叉搜索树。

**思路**

1. 直接思考是不是只要比较左右节点的值就即可，写出以下代码：

   ```java
   class Solution {
       public boolean isValidBST(TreeNode root) {
           if(root == null) {
               return true;
           }
           if(root.left != null && root.left.val >= root.val) {
               return false;
           }
           if(root.right != null && root.right.val <= root.val) {
               return false;
           }
           return isValidBST(root.left) && isValidBST(root.right);
       }
   }
   ```

2. 该算法出现了错误，因为每个BST的节点都应该小于右子树，而不能仅仅单纯判断一小颗树。

3. 因此需要记录两个值：最大和最小，通过该边界进行判断约束。

**实现**

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, null, null);
    }

    private boolean isValidBST(TreeNode root, TreeNode min, TreeNode max) {
        if(root == null) {
            return true;
        }
        if(min != null && root.val <= min.val) {
            return false;
        }
        if(max != null && root.val >= max.val) {
            return false;
        }
        return isValidBST(root.left, min, root) && isValidBST(root.right, root, max);
    }
}
```

### 二叉搜索树中的插入操作

> #### [701. 二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
>
> 难度中等249
>
> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 **保证** ，新值和原始二叉搜索树中的任意节点值都不同。
>
> **注意**，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回 **任意有效的结果** 。

**实现**

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if(root == null) {
            return new TreeNode(val);
        } else if(root.val > val) {
            root.left = insertIntoBST(root.left, val);
        } else if(root.val < val) {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```

### 二叉搜索树中的删除操作

> #### [450. 删除二叉搜索树中的节点](https://leetcode-cn.com/problems/delete-node-in-a-bst/)
>
> 难度中等614
>
> 给定一个二叉搜索树的根节点 **root** 和一个值 **key**，删除二叉搜索树中的 **key** 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。
>
> 一般来说，删除节点可分为两个步骤：
>
> 1. 首先找到需要删除的节点；
> 2. 如果找到了，删除它。

**思路**

1. 找到该节点，若要删除该节点A，会改变BST的性质。有三种情况：
   1. A为末端节点，直接删除。
   2. A有一个非空节点，让该非空节点接替自己。
   3. A有两个非空节点，为了不破坏BST的性质，需要找到左子树中最大的，或右子树中最小的来接替A。

**实现**

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null) {
            return null;
        }
        if(root.val == key) {
            // 删除操作
            if(root.left == null) {
                return root.right;
            } 
            if(root.right == null) {
                return root.left;
            } 
            TreeNode minNode = getMin(root.right);
            root.val = minNode.val;
            root.right = deleteNode(root.right, minNode.val);
        } else if(root.val > key) {
            root.left = deleteNode(root.left, key);
        } else if(root.val < key) {
            root.right = deleteNode(root.right, key);
        }
        return root;
    }

    private TreeNode getMin(TreeNode root) {
        for(;root.left != null; root = root.left){};
        return root;
    }
}
```

- 这里不考虑删除操作的细节，仅通过值交换。



## 题目补充

#### 树的直径

一棵二叉树的直径长度是任意两个结点路径长度中的最大值，这条路径可能穿过也可能不穿过根结点。

```java
    public int diameterOfBinaryTree(TreeNode root) {
        // 两个节点路径长度最大值
        // 1. left root right
        // 2. left root 
        // 3. root right
        // 4. root
        getDiameterOfBinaryTree(root);
        return max;
    }

    private int max = 0;

    private int getDiameterOfBinaryTree(TreeNode root) {
        if(root.left == null && root.right == null) {
            return 0;
        }
        int left = root.left == null ? 0 : getDiameterOfBinaryTree(root.left) + 1;
        int right = root.right == null ? 0 : getDiameterOfBinaryTree(root.right) + 1;
        max = Math.max(max, left + right);
        return Math.max(left, right);
    }
```

