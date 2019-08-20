---
description: 动态规划
---

# Dynamic Programming

## 特征

1. 求一个问题的最优解
2. 大问题可以分解为小问题，子问题还有重叠的更小子问题
3. 整体问题最优解取决于子问题的最优解（状态转移方程）
4. 从上往下分析问题，从下往上解决问题
5. 讨论底层的边界问题

### 自定向下的备忘录法

### 自底向上的动态规划

### 线性动规，区域动规，树形动规，背包动规四类。

### 区域动规



[爬楼梯](https://leetcode-cn.com/problems/climbing-stairs)

```java
class Solution {
    private int[] memo;
    public int climbStairs(int n) {
        memo = new int[n + 1];
        Arrays.fill(memo, -1);
        return getClimbStairs(n);
    }
    private int getClimbStairs(int n) {
        if (n == 1) {
            return 1;
        }
        if (n == 2) {
            return 2;
        }
        if (memo[n] == -1) {
            memo[n] = getClimbStairs(n - 1) + getClimbStairs(n - 2);
        }
        return memo[n];
    }
}
```

