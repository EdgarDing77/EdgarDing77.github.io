---
title: 算法(4)-动态规划
date: 2022-01-14 13:08:05.725
updated: 2022-01-30 10:58:34.368
categories: [算法]
tags: [算法]
---

# 算法(4)-动态规划

## Introduction

**「What」**动态规划（Dynamic Programming，DP），动态规划是一种过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。

首先，动态规划的一般形式就是**求最值**，而求解动态规划的核心问题是**穷举**，但动态规划通过仅仅解决每个子问题一次从而减少计算量，相比朴素解法效率上高很多。动态规划在查找有很多**重叠子问题**的情况的最优解时有效。它将问题重新组合成子问题。为了避免多次解决这些子问题，它们的结果都逐渐被计算并被保存，从简单的问题直到整个问题都被解决。因此动态规划**保存递归时结果**，因而不会在解决同样的问题时花费时间。

动态规划只能应用于有最优子结构的问题。最优子结构的意思是局部最优解能决定全局最优解（对有些问题这个要求并不能完全满足，故有时需要引入一定的近似）。简单地说，问题能够分解成子问题来解决。

综上动态规划的问题需要以下特点：

- **子问题重叠性质：**这类问题存在重叠子问题
- **最优子结构：**该问题可以通过最优子问题得到原问题的最优解
- **无后效性：**子问题的解一旦确定就不再更改，不受在这之后、包含它的更大问题的求解决策影响

思考状态转移方程的思维框架：

1. 这个问题的base case（最简单情况）是什么？
2. 这个问题有什么“状态”？
3. 对于每个“状态”，可以做什么“选择”使得“状态”发生改变？
4. 如何定义dp数组/函数的含义来表现“状态”和“选择”？
5. dp方向？根据已知推出未知。

即三点：**状态**、**选择**、**dp数组的定义**。

*tips：这里只是做题目代码的整合。同时以《labuladong的算法小抄》为主进行刷题的笔记。*

**思路模版**

1. 一维数组：一般定义为以dp[i]结尾的最长XXX。
2. 二维数组：
   1. 涉及两个字符串/数组的时候：对于arr[0..i]和arr[0..j]的最长为dp\[i][j]。
   2. 涉及一个字符串/数组的时候，对于arr[i..j]的最长为dp\[i][j]。

## 基础

### 爬楼梯问题

#### [70.爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

```java
public int climbStairs(int n) {
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}
```

#### [746.使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/)

多了条件：每当你爬上一个阶梯你都要花费对应的体力值，一旦支付了相应的体力值，你就可以选择向上爬一个阶梯或者爬两个阶梯；因此这里的dp[i]的定义：到达第i个台阶所花费的最少体力为dp[i]。

```java
    public int minCostClimbingStairs(int[] cost) {
        if (cost == null || cost.length == 0) {
            return 0;
        }
        if (cost.length == 1) {
            return cost[0];
        }
        int[] dp = new int[cost.length];
        dp[0] = cost[0];
        dp[1] = cost[1];
        for (int i = 2; i < cost.length; i++) {
            dp[i] = Math.min(dp[i - 1], dp[i - 2]) + cost[i];
        }
        //最后一步，如果是由倒数第二步爬，则最后一步的体力花费可以不用算
        return Math.min(dp[cost.length - 1], dp[cost.length - 2]);
    }
```

### 路径问题

#### [62.不同路径](https://leetcode-cn.com/problems/unique-paths/)

`dp[i][j]`表示从（0，0）出发到（i，j）有`dp[i][j]`种不同路径。

```java
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        //初始化
        for (int i = 0; i < m; i++) {
            dp[i][0] = 1;
        }
        for (int i = 0; i < n; i++) {
            dp[0][i] = 1;
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
```

#### [63.不同路径II](https://leetcode-cn.com/problems/unique-paths-ii/)

增加了障碍物，仅需标记对应的dp位置保持初值就好了。

```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int n = obstacleGrid.length, m = obstacleGrid[0].length;
        int[][] dp = new int[n][m];
        dp[0][0] = 1 - obstacleGrid[0][0];
        for (int i = 1; i < m; i++) {
            if (obstacleGrid[0][i] == 0 && dp[0][i - 1] == 1) {
                dp[0][i] = 1;
            }
        }
        for (int i = 1; i < n; i++) {
            if (obstacleGrid[i][0] == 0 && dp[i - 1][0] == 1) {
                dp[i][0] = 1;
            }
        }
        for (int i = 1; i < n; i++) {
            for (int j = 1; j < m; j++) {
                if (obstacleGrid[i][j] == 1) continue;
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[n - 1][m - 1];
    }
```

### 跳跃游戏问题

#### 跳跃游戏I

> #### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)
>
> 难度中等1592
>
> 给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 判断你是否能够到达最后一个下标。

**思路**

1. 贪心：只需要计算能跳达的最远是否超过最后一个下标即可。

**实现**

Method1: 贪心

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int farthest = 0;
        for(int i=0; i<n-1; i++) {
            farthest = Math.max(farthest, i + nums[i]);
            if(farthest <= i) { // 碰到0卡住了
                return false;
            }
        }
        return farthest >= n-1;
    }
}
```

#### 跳跃游戏II

> #### [45. 跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)
>
> 难度中等1368
>
> 给你一个非负整数数组 `nums` ，你最初位于数组的第一个位置。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 你的目标是使用最少的跳跃次数到达数组的最后一个位置。
>
> 假设你总是可以到达数组的最后一个位置。

**思路**

1. 这里不同于上题，求的是最少需要跳多少次能到最后一个位置。因此，并不是跳的越多越好。
2. Method1: 若采用动态规划，自顶向下的动态规划：
   1. base case：dp[0]=0;dp[1..i]=Integer.MAX_VALUE;
   2. 定义：dp[i]表示从所以i跳到最后一格所需的最少步数。
   3. 选择跳i步，但是寻找跳到i最小的作为结果。
3. Method2: 贪心，在当前i得时候选择能跳到最远的索引(i, i+nums[i])

**实现**

Method1: 

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        for(int i=1; i<n; i++) {
            dp[i] = n;
            for(int j=0; j<i; j++) {
                if(nums[j] >= i - j) { // 当前能跳的距离大于了搜索范围
                    dp[i] = Math.min(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[n-1];
    }
}
```

Method2:

```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        // 站在索引i，最多能跳到索引end
        int end = 0;
        // 从索引i到end能跳到最远的距离
        int farthest = 0;
        int jumpCount = 0;
        for (int i = 0; i < n - 1; i++) {
            farthest = Math.max(nums[i] + i, farthest);
            // farthest记录了从i 到 end能跳到的最远距离
            if (end == i) {
                jumpCount++;
                end = farthest;
            }
        }
        return jumpCount;
    }
}
```

#### 跳跃游戏III

> #### [1306. 跳跃游戏 III](https://leetcode-cn.com/problems/jump-game-iii/)
>
> 难度中等91
>
> 这里有一个非负整数数组 `arr`，你最开始位于该数组的起始下标 `start` 处。当你位于下标 `i` 处时，你可以跳到 `i + arr[i]` 或者 `i - arr[i]`。
>
> 请你判断自己是否能够跳到对应元素值为 0 的 **任一** 下标处。
>
> 注意，不管是什么情况下，你都无法跳到数组之外。

**实现**

*该问题与动态规划不搭，因此这里仅做补充类型问题的存在：*

```java
class Solution {
    public boolean canReach(int[] arr, int start) {
        if(start >= arr.length || start < 0) {
            return false;
        }
        if(arr[start] < 0) { // 来过
            return false;
        }
        if(arr[start] == 0) {
            return true;
        }
        int l = start + arr[start];
        int r = start - arr[start];
        arr[start] = -1;
        return canReach(arr, l) || canReach(arr, r);
    }
}
```

## 子序列问题

### 最长递增子序列

最长递增子序列（Longest Increasing Subsequence，LIS）问题，注意子序列和子串的区别，子串是必须连续的元素。

> #### [300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
>
> 难度中等2150
>
> 给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。
>
> 子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

**思路**

1. 依据动态规划的思想，假设dp[0…i]都已知，推出dp[i]，思考dp[i]代表什么。
2. 可以得到定义：dp[i]代表以nums[i]为结尾的最长递增子序列的长度。
3. base case：dp[0…i]=1，因为起码包括它本身。
4. 状态转移：若nums[i]>nums[j]则说明dp[i]=Math.max(dp[i], dp[j] + 1)，选择形成一个新的递增序列+1，或是保持不变。

**实现**

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        int res = 1;
        for(int i=0; i<n; i++) {
            for(int j=0; j<i; j++) {
                if(nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                    res = Math.max(dp[i], res);
                }
            }
        }
        return res;
    }
}
```

- 需要注意的是dp[i]并不是整个序列的最长递增序列，因此需要记录。
- 时间复杂为O(n^2)，空间为O(n)。有更高效率的patience sort，这里不详细说明。

#### 信封嵌套问题

实质将最长递增子序列问题上升到了二维层面。

> #### [354. 俄罗斯套娃信封问题](https://leetcode-cn.com/problems/russian-doll-envelopes/)
>
> 难度困难641
>
> 给你一个二维整数数组 `envelopes` ，其中 `envelopes[i] = [wi, hi]` ，表示第 `i` 个信封的宽度和高度。
>
> 当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。
>
> 请计算 **最多能有多少个** 信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。
>
> **注意**：不允许旋转信封。

**思路**

1. 先对其中一个维度进行排序，如w，这样可以保证了一个从小到大宽度上的嵌套，而h在w的排序上进行降序，在h数组上进行计算LIS的长度就是答案了。
2. 降序的原因是因为，在w一样的基础上，h大的没法嵌套进h小的中。

**实现**

```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        Arrays.sort(envelopes, (o1, o2) -> {
            return o1[0]==o2[0] ? o2[1]-o1[1] : o1[0]-o2[0];
        });
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        int res = 1;
        for(int i=0; i<n; i++) {
            for(int j=0; j<i; j++) {
                if(envelopes[i][1] > envelopes[j][1]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                    res = Math.max(dp[i], res);
                }
            }
        }
        return res;
    }
}
```

- 该方法仅能在2维内使用。

### 最长公共子序列

最长公共子序列（Longest Common Subsequence，LCS）。

> #### [1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)
>
> 难度中等807
>
> 给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。
>
> 一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
>
> - 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。
>
> 两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

**思路**

1. 对于一般两个字符串的问题，需要一个二维dp数组。
2. dp数组含义：dp\[i][j]的含义为，对于s\[0..i-1]和s\[0..j-1]他们的LCS是dp\[i][j]。
3. base case：dp\[0][…]=0||dp\[…][0]。
4. 状态转移：对于每一个字符有两种选择，要么在lcs，要么不在，
   1. 因此若s1[i]==s2[j]就是存在于lcs中，`dp[i][j] = dp[i-1][j-1] + 1`;
   2. 若不存在，则说明这两个字符有一个不存在：`dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1])`。
   3. 当然也存在两个字符都不存在的情况，但是它永远是最小的情况，可以无需考虑。

**实现**

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int n1, n2;
        if((n1 = text1.length()) == 0 || (n2 = text2.length()) == 0) {
            return 0;
        } 
        int[][] dp = new int[n1+1][n2+1];
        for(int i=1; i<=n1; i++) {
            for(int j=1; j<=n2; j++) {
                if(text1.charAt(i-1) == text2.charAt(j-1)) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        return dp[n1][n2];
    }
}
```

- dp\[i][j]的状态与dp\[i-1][j]、dp\[i][j-1]和dp\[i-1][j-1]有关。
- 进行状态压缩，可以使用一个一维数组来标识dp[j]，dp\[i-1][j]就代表上一个状态。而dp\[i][j-1]就是dp[j-1]，即dp\[i-1][j-1]也就是上一个dp[j-1]的状态，但是会被覆盖，因此使用一个tmp来保存（这是状态压缩的关键）。
- 状态压缩的核心思路就是将**二维投影成一维数组。**

状态压缩：

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int n1, n2;
        if((n1 = text1.length()) == 0 || (n2 = text2.length()) == 0) {
            return 0;
        } 
        //int[][] dp = new int[n1+1][n2+1];
        int[] dp = new int[n2 + 1];
        for(int i=1; i<=n1; i++) {
            for(int j=1, pre=0; j<=n2; j++) {
                int tmp = dp[j];
                if(text1.charAt(i-1) == text2.charAt(j-1)) {
                    //dp[i][j] = dp[i-1][j-1] + 1;
                    dp[j] = pre + 1;
                } else {
                    //dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
                    dp[j] = Math.max(dp[j], dp[j-1]);
                }
                pre = tmp;
            }
        }
        return dp[n2];
    }
}
```

### 最长回文子序列

> #### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)
>
> 难度中等705
>
> 给你一个字符串 `s` ，找出其中最长的回文子序列，并返回该序列的长度。
>
> 子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。

**思路**

1. 定义dp数组：dp\[i][j]为对于s[i..j]中最长回文子序列。
2. base case：dp\[i][i]=1其本身。
3. dp结果：dp\[0][n]，判断其dp方向为自下向上，自左向右。
4. 状态方程：若s[i]==s[j]，则dp\[i][j]=dp\[i+1][j-1]+2即可，否则，选取dp\[i-1][j]和dp\[i][j-1]中最大的一个。

**实现**

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n;
        if((n = s.length()) == 0) {
            return 0;
        }
        int[][] dp = new int[n][n];
        for(int i=0; i<n; i++) {
            dp[i][i] = 1;
        }
        for(int i=n-2; i>=0; i--) {
            for(int j=i+1; j<n; j++) {
                if(s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i+1][j-1] + 2; 
                } else {
                    dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1]);
                }
            }
        }
        return dp[0][n-1];
    }
}
```

- dp优化，优化思路同上题。

状态压缩：

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n;
        if((n = s.length()) == 0) {
            return 0;
        }
        // int[][] dp = new int[n][n];
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        for(int i=n-2; i>=0; i--) {
            for(int j=i+1, pre=0; j<n; j++) {
                int tmp = dp[j];
                if(s.charAt(i) == s.charAt(j)) {
                    // dp[i][j] = dp[i+1][j-1] + 2; 
                    dp[j] = pre + 2;
                } else {
                    // dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1]);
                    dp[j] = Math.max(dp[j], dp[j-1]);
                }
                pre = tmp;
            }
        }
        return dp[n-1];
    }
}
```

#### 最长回文子串

这里补充一个回文子串的动态规划做法。

> #### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)
>
> 难度中等4575
>
> 给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**思路**

1. Mehtod1:回文的思想就是从中间向两边扩散。

2. Method2:动态规划
   1. 定义dp数组的含义：dp[i]\[j]标识s[i..j]是否为回文子串，因此我们返回最长的子串需要记录。
   2. base case：i==j时dp[i]\[j]=true。

**实现**

Method1:中间向两边扩散

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) {
            return "";
        }
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            int len1 = expandAroundCenter(s, i, i);
            int len2 = expandAroundCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }

    public int expandAroundCenter(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            --left;
            ++right;
        }
        return right - left - 1;
    }
}
```

Mehtod2:动态规划

```java
class Solution {
    public String longestPalindrome(String s) {
        int n;
        if(s == null || (n = s.length()) < 2) {
            return s;
        }
        int max = 1;
        int l = 0;
        boolean[][] dp = new boolean[n][n];
        for(int i=n-1; i>=0; i--) {
            for(int j=i; j<n; j++) {
                if(i == j) {
                    dp[i][j] = true;
                } else if(i + 1 == j || i == j - 1) {
                    dp[i][j] = s.charAt(i) == s.charAt(j);
                } else {
                    dp[i][j] = s.charAt(i)==s.charAt(j) && dp[i+1][j-1];
                }
                if(dp[i][j] && j-i+1 >= max) {
                    max = j - i + 1;
                    l = i;
                }
            }
        }
        return s.substring(l, l + max);
    }
}
```

- 时间效率为O(n^2)，空间可以优化，这里不去优化。

#### 最长回文串

> #### [409. 最长回文串](https://leetcode-cn.com/problems/longest-palindrome/)
>
> 难度简单374
>
> 给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。
>
> 在构造过程中，请注意区分大小写。比如 `"Aa"` 不能当做一个回文字符串。

以下是一个很巧妙的解法：

```java
class Solution {
    public int longestPalindrome(String s) {
        int[] cnt = new int[58];
        for(char ch : s.toCharArray()) {
            cnt[ch - 'A'] += 1;
        }
        int ans = 0;
        for(int x : cnt) {
            ans += x - (x & 1);
        }
        return ans < s.length() ? ans + 1 : ans;
    }
}
```

## 高楼扔鸡蛋

> #### [887. 鸡蛋掉落](https://leetcode-cn.com/problems/super-egg-drop/)
>
> 难度困难743
>
> 给你 `k` 枚相同的鸡蛋，并可以使用一栋从第 `1` 层到第 `n` 层共有 `n` 层楼的建筑。
>
> 已知存在楼层 `f` ，满足 `0 <= f <= n` ，任何从 **高于** `f` 的楼层落下的鸡蛋都会碎，从 `f` 楼层或比它低的楼层落下的鸡蛋都不会破。
>
> 每次操作，你可以取一枚没有碎的鸡蛋并把它从任一楼层 `x` 扔下（满足 `1 <= x <= n`）。如果鸡蛋碎了，你就不能再次使用它。如果某枚鸡蛋扔下后没有摔碎，则可以在之后的操作中 **重复使用** 这枚鸡蛋。
>
> 请你计算并返回要确定 `f` **确切的值** 的 **最小操作次数** 是多少？

**分析**

1. 存在楼层f[0,n]，任何>f的鸡蛋都会碎，<=f的鸡蛋不会。
2. 取1鸡蛋，在x层[1,n]下落，如果没碎可以重复使用。
3. 求鸡蛋在f（最大值）不会碎，和最小操作次数。即**最坏情况（鸡蛋破碎一定发生在搜索区间穷尽的时候）**下，**至少**要扔多少次，才能确定楼层f？
4. 不考虑鸡蛋个数限制：
   1. 线性：从1扔到f，最坏的情况是，扔到f没碎，所以操作了f次。
   2. 二分：每次从n/2开始扔，最坏的情况是，f==1和f==n，操作logn次，这是最优的策略。（但是限定了鸡蛋数量，所以直接使用二分是不行的）。

**思路**

Method1:

1. “状态”（发生变化的量）：明显有两个，就是当前拥有的鸡蛋数k和需要测试的楼层n。
2. “选择”：选择哪层去扔。
3. “状态转移”：在第i层扔下后有两种状态：
   1. 如果鸡蛋碎了，那么鸡蛋个数k-1，搜索区间从[1..n]变为[1..i-1]。
   2. 如果鸡蛋没碎，那么鸡蛋个数k，搜索区间为[i+1..n]。
   3. base case：`n==0,return 0;`和`k==1,return n;`当鸡蛋只剩一个的时候，只能线性扫描了。
   4. 定义：dp\[i][k]表示有k个鸡蛋，i层楼中确定F的具体值的最小搜索次数。
   5. 状态方程：`dp[i][k] =min{ max(dp[j-1][k-1], dp[i-j][k]) + 1 | 1<=j<=i  }`。

Method2:

1. broken: dp\[i-1][k-1]函数单调递增，并且最小值为dp\[0][k-1]为0，最大值为dp\[N-1][k-1]。
2. noBroken: dp\[n-i][k]函数单调递减，并且最小值为dp\[0][k]为0，最大值为dp\[n-1][k]。
3. 两个函数的交点就是一个最低点，因此，可以采用二分的方式。横坐标就是i(r)。

Method3: 该方式转换思考，重新定义状态转移，个人凭自己没法想到该方式（想不到，想不到啊～）。

1. 修改dp定义，dp\[k][m]=n：表示当前有k个鸡蛋，最多尝试m次，在这个状态下，最坏的情况能最多确切测试一栋n层的楼。即原始思路的一个反向版本。
2. 根据以下事实：
   1. 无论你在哪层楼扔鸡蛋，鸡蛋只可能摔碎或者没摔碎，碎了的话就测楼下，没碎的话就测楼上。
   2. 无论你上楼还是下楼，总的楼层数 = 楼上的楼层数 + 楼下的楼层数 + 1（当前这层楼）。
3. 状态转移方程：`dp[k][m] = dp[k][m - 1] + dp[k - 1][m - 1] + 1`。
4. base case：dp[0]\[…]=0,dp[…]\[0]=0，且m最多不会超过n次。

**实现**

Method1:

```java
class Solution {
    public int superEggDrop(int k, int n) {
        int[][] dp = new int[n + 1][k+1];
        for(int i=n; i>=0; i--) {
            dp[i][1] = i;
        }
        for(int i=1; i<=n; i++) {
            for(int u=2; u<=k; u++) { // 鸡蛋个数为1无需计算，属于base case了。
                int res = Integer.MAX_VALUE;
                for(int j=1; j<=i; j++) {
                    res = Math.min(res, 
                        Math.max(dp[j-1][u-1], dp[i-j][u]) + 1);
                }
                dp[i][u] = res;
            }   
        }
        return dp[n][k];
    }
}
```

- 时间复杂度为O(kn^2)效率不高。

Method2:

```java
class Solution {
    public int superEggDrop(int k, int n) {
        int[][] dp = new int[n + 1][k+1];
        for(int i=n; i>=0; i--) {
            dp[i][1] = i;
        }
        for(int i=1; i<=n; i++) {
            for(int u=2; u<=k; u++) {
                // int res = Integer.MAX_VALUE;
                // for(int j=1; j<=i; j++) {
                //     res = Math.min(res, 
                //         Math.max(dp[j-1][u-1], dp[i-j][u]) + 1);
                // }
                // dp[i][u] = res;
                int l = 1, r = i;
                int res = Integer.MAX_VALUE;
                for(;l <= r; ) {
                    int mid = l + (r - l) / 2;
                    int broken = dp[mid - 1][u-1]; // 若broken递增，大了则缩小
                    int noBroken = dp[i - mid][u]; // 若noBroken递减，大了则增加
                    if(broken > noBroken) {
                        res = Math.min(res, broken + 1);
                        r = mid - 1;
                    } else {
                        res = Math.min(res, noBroken + 1);
                        l = mid + 1;
                    }
                }
                dp[i][u] = res;
            }   
        }
        return dp[n][k];
    }
}
```

- 时间复杂度为O(knlogn)。

Method3:

```java
class Solution {
    public int superEggDrop(int K, int N) {
        int[] dp = new int[K + 1];
        int ans = 0;    // 操作的次数
        while (dp[K] < N){
            for (int i = K; i > 0; i--) // 从后往前计算
                dp[i] = dp[i] + dp[i-1] + 1;
            ans++;
        }
        return ans;
    }
}
```

- 时间O(kn)

## 背包问题

背包问题

- 背包
  - 最大容量
- 物品
  - 价值w
  - 体积v
  - 每个物品的数量
    - 只有一个：选一个和不选「01背包」
    - 无数个：选多个和不选「完全背包」
    - 不同的物品数量不同：「多重背包」
    - 按组打包，每组最多选一个：「分组背包」

### 01背包

有N件物品和一个最多能背重量为W 的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。**每件物品只能用一次**，求解将哪些物品装入背包里物品价值总和最大。

**思路：**

- 可以发现有两个状态重量与价值，若使用朴素解法，时间复杂度就是$O(n^2)$，指数级别。
- 判断是否可以使用动态规划
  - 每次拿到价值最大的，重量最小的，这样就能得到价值总和最大。
  - 因此存在着最优子结构。
- 框架带入：
  - 状态：重量w和价值v，`dp[i][j]`的含义表示从0…i的物品中任意取取得的价值为`dp[i][j]`，背包容量为j。
  - base case：`dp[0...i][0] == 0`和`dp[0][0...j]`（没有容量时为0，没拿东西当然也为0）
  - 状态改变：
    - 放东西：`dp[i][j] = dp[i - 1][j - weight[i]] + value[i]`，只有j>weight[i]的时候即能放进东西的时候。
    - 不放东西：`dp[i][j] = dp[i - 1][j]`（不放东西就相当于没变化等与之前的）。
  - 状态转移方程：我们要取得最大，因此获取放东西与不放东西的最大价值`dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`。
  - dp方向：`dp[bagSize - 1][bagWeight]`是要求得的值，观察dp方程，基本可以确定从左上到右下。

最后可以得到：

```java
public class Knapsack {
  public int 01Knapsack(int[] weight, int[] value, int bagSize) {
        int wLen = weight.length, value0 = 0;
        //定义dp数组：dp[i][j]表示背包容量为j时，前i个物品能获得的最大价值
        int[][] dp = new int[wLen + 1][bagSize + 1];
        //初始化：背包容量为0时，能获得的价值都为0
        for (int i = 0; i <= wLen; i++){
            dp[i][0] = value0;
        }
        //遍历顺序：先遍历物品，再遍历背包容量
        for (int i = 1; i <= wLen; i++){
            for (int j = 1; j <= bagSize; j++){
                if (j < weight[i - 1]){
                    dp[i][j] = dp[i - 1][j];
                }else{
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - weight[i - 1]] + value[i - 1]);
                }
            }
        }
    		return dp[wLen][bagSize];
  }
}
```

**如果计算状态`dp[i][j]`需要的都是`dp[i][j]`相邻的状态，那么就可以使用状态压缩。**

```java
        for (int i = 0; i < wLen; i++){
            for (int j = bagWeight; j >= weight[i]; j--){
                dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
            }
        }
```

## 买卖股票问题

**分析**

该问题的状态有三种：

1. 天数i
2. 允许交易的最大次数k
3. 当前持有的状态（0-没有，1-持有）j

而每天都有三种选择：

1. 买入buy
2. 卖出sell
3. 无操作no

因此dp数组的定义为：`dp[i][k][0/1]`表示可以获取的最大利润，它的base case如下：

```
dp[-1][k][0] = 0
// 解释：因为 i 是从 0 开始的，所以 i = -1 意味着还没有开始，这时候的利润当然是 0 。
dp[-1][k][1] = -infinity
// 解释：还没开始的时候，是不可能持有股票的，用负无穷表示这种不可能。
dp[i][0][0] = 0
// 解释：因为 k 是从 1 开始的，所以 k = 0 意味着根本不允许交易，这时候利润当然是 0 。
dp[i][0][1] = -infinity
// 解释：不允许交易的情况下，是不可能持有股票的，用负无穷表示这种不可能。
```

### 买卖股票的最佳时机I

> #### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
>
> 难度简单2054
>
> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。
>
> 你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。
>
> 返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

**思路**

1. 该题根据上面的状态，只能买卖一次，因此k=1。

2. 根据前文提供的分析，在该题中base case：`dp[i][0]=0,dp[i][1]=Integer.MIN_VALUE`。

3. 状态转移：

   ```
   dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i - 1]);
   // 0代表没有持有，可以进行的操作有sell, no
   dp[i][1] = Math.max(dp[i - 1][1], -prices[i - 1]);
   // 1代表持有，可以进行的操作有buy, no
   ```

**实现**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n;
        if((n = prices.length) == 0) {
            return 0;
        }
        int[][] dp = new int[n+1][2];
        for(int i=0; i<=n; i++) {
            dp[i][1] = Integer.MIN_VALUE;
        }
        for(int i=1; i<=n; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i - 1]);
dp[i][1] = Math.max(dp[i - 1][1], -prices[i - 1]);
        }
        return dp[n][0];
    }
  //	 优化
    public int maxProfit(int[] prices) {
        if(prices == null) {
            return 0;
        }
        int min = Integer.MAX_VALUE, max = 0;
        for(int i=0; i<prices.length; i++) {
            if(prices[i] < min) {
                min = prices[i];
            }
            max = Math.max(max, prices[i] - min);
        }
        return max;
    }
}
```

### 买卖股票的最佳时机II

> #### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)
>
> 难度中等1522
>
> 给定一个数组 `prices` ，其中 `prices[i]` 是一支给定股票第 `i` 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
> **注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**思路**

1. 不同于上题的是，这里的交易次数不在被约束，可以多次买卖，因此k为infinity，而k=infinity与k=1是一样的思路。

2. base case：`dp[i][1]=-infinity,dp[i][0]=0`。

3. 状态转移：

   ```
   dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i-1]);
   dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i-1]);
   // 不同于第一题的是，因为k是无限次，所以在持有的情况下，卖出时需要注意当时未持有时所得的利润。
   ```

**实现**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n;
        if((n = prices.length) == 0) {
            return 0;
        }
        int[][] dp = new int[n+1][n+1];
        for(int i=0; i<=n; i++) {
            dp[i][1] = Integer.MIN_VALUE;
        }
        for(int i=1; i<=n; i++) {
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i-1]);
            dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i-1]);
        }
        return dp[n][0];
    }
}
```

### 买卖股票的最佳时机III

> #### [123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)
>
> 难度困难979
>
> 给定一个数组，它的第 `i` 个元素是一支给定的股票在第 `i` 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 **两笔** 交易。
>
> **注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**思路**

1. 这里k=2（买卖次数），因此做法都不同于上面。
2. base case：`dp[0][0..k][0]=0,dp[0][0..k][1]=Integer.MIN_VALUE`。
3. 最多是两笔交易，因此可以进行<=2次交易。

**实现**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n;
        if((n = prices.length) == 0) {
            return 0;
        }
        int K = 2;
        int[][][] dp = new int[n+1][K+1][2];
        // for(int i=0; i<=n; i++) {
        //     dp[i][0][1] = Integer.MIN_VALUE;
        //     dp[i][1][1] = Integer.MIN_VALUE;
        //     dp[i][2][1] = Integer.MIN_VALUE;
        // }
        for(int i=0; i<=K; i++) {
            dp[0][i][1] = Integer.MIN_VALUE;
        }
        for(int i=1; i<=n; i++) {
            for(int k=1; k<=K; k++) {
                dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i-1]);
                dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i - 1]);
            }
        }
        return dp[n][K][0];
    }
}
```

- 注意，这里因为base case：`dp[1..i][0..k][1]`都只与前一个状态有关，且在i==1的时候必然发生改变，因此无需遍历赋值。
- 当持有状态的时候：`dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i-1]);`sell处于第k次交易中，而buy进行交易记录所以与上一次的交易有关，因此k-1。

### 买卖股票的最佳时机IV

> #### [188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)
>
> 难度困难642
>
> 给定一个整数数组 `prices` ，它的第 `i` 个元素 `prices[i]` 是一支给定的股票在第 `i` 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 **k** 笔交易。
>
> **注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**思路**

1. 最大的不同是k不再是一个定值，但是解决的方法仍然与k=2相同。

**实现**

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int n;
        if((n = prices.length) == 0) {
            return 0;
        }
        int[][][] dp = new int[n+1][k+1][2];
        for(int i=0; i<=k; i++) {
            dp[0][i][1] = Integer.MIN_VALUE;
        }
        for(int i=1; i<=n; i++) {
            for(int j=1; j<=k; j++) {
                dp[i][j][0] = Math.max(dp[i-1][j][0], dp[i-1][j][1] + prices[i-1]);
                dp[i][j][1] = Math.max(dp[i-1][j][1], dp[i-1][j-1][0] - prices[i-1]);
            }
        }
        return dp[n][k][0];
    }
}
```

## 常见问题

### 最大子数组和

> #### [剑指 Offer 42. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)
>
> 难度简单440
>
> 输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。
>
> 要求时间复杂度为O(n)。

**思路**

1. 滑动窗口多用于解决子串和子数组问题，但是在该问题上，窗口中的值可能增大或缩小没法进行窗口收缩的判断。
2. dp设计上，若连接之前的还是以自己为开头另起一个子数组。

**实现**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int n;
        if((n = nums.length) == 0) {
            return 0;
        }
        int[] dp = new int[n];
        dp[0] = nums[0];
        int ans = dp[0];
        for(int i=1; i<n; i++) {
            dp[i] = Math.max(nums[i], dp[i-1]+nums[i]);
            ans = Math.max(dp[i], ans);
        }
        return ans;
    }
}
```

## 题目补充

### 正则表达式

> #### [10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)
>
> 难度困难2663
>
> 给你一个字符串 `s` 和一个字符规律 `p`，请你来实现一个支持 `'.'` 和 `'*'` 的正则表达式匹配。
>
> - `'.'` 匹配任意单个字符
> - `'*'` 匹配零个或多个前面的那一个元素
>
> 所谓匹配，是要涵盖 **整个** 字符串 `s`的，而不是部分字符串。
>
> 提示：
>
> 1 <= s.length <= 20
> 1 <= p.length <= 30
> s 只含小写英文字母。
> p 只含小写英文字母，以及字符 . 和 *。
> 保证每次出现字符 * 时，前面都匹配到有效的字符

**实现**

```java
public class RegularSolution {
    /**
     * 剑指 Offer 19. 正则表达式匹配
     * 请实现一个函数用来匹配包含'. '和'*'的正则表达式。
     * 模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（含0次）。
     * 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但与"aa.a"和"ab*a"均不匹配。
     *
     * @param s
     * @param p
     * @return
     */
    public boolean isMatch(String s, String p) {
        // 大致思路：双指针移动，若都移动到末尾，则匹配成功
        return dp(s, 0, p, 0, new HashMap<>());
    }

    private boolean dp(String s, int i, String p, int j, HashMap<String, Boolean> memo) {
        int m = s.length(), n = p.length();
        if (j == n) {
            return i == m;
        }
        if (i == m) {
            // *的位置
            if ((n - j) % 2 == 1) {
                return false;
            }
            for (; j + 1 < n; j += 2) {
                if (p.charAt(j + 1) != '*') {
                    return false;
                }
            }
            return true;
        }
        String key = String.valueOf(i + "," + j);
        if(memo.containsKey(key)) {
            return memo.get(key);
        }
        boolean res = false;
        if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
            if (j < n - 1 && p.charAt(j + 1) == '*') {
                res = dp(s, i, p, j + 2, memo) || dp(s, i + 1, p, j, memo);
            } else {
                res = dp(s, i + 1, p, j + 1, memo);
            }
        } else {
            if(j < n - 1 && p.charAt(j + 1) == '*') {
                res = dp(s, i, p, j + 2, memo);
            } else {
                res = false;
            }
        }
        memo.put(key, res);
        return res;
    }
}
```

