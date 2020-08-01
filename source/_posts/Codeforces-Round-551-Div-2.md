---
title: "Codeforces Round #551 Div. 2"
tags:
  - codeforces
  - 贪心
  - 乱搞
  - 二分
  - 交互题
categories: 训练
abbrlink: 43db3ce0
date: 2019-04-23 22:54:40
mathjax: true
---

# A. Serval and Bus

算出 $t$ 之后每班车的最早时间，取最小值

# B. Serval and Toy Bricks

每个有方块的位置尽可能取高，即 $\min(a[j], b[i])$

<!--more-->

# C. Serval and Parenthesis Sequence

统计一下要放多少的$($和$)$,显然前面尽量放$($是最优的，然后 check 判断

# D. Serval and Rooted Tree

考虑最下面两层，如果答案再这棵子树中，那么:

- 如果是 min 操作，$ ans = k - leaves + 1 $
- 如果是 max 操作，$ ans = leaves = k - 1 + 1 $

我们定义 f[i]为 i 子树中的有效叶子，那么 ans = k - f[1] + 1，且:

- 如果是 min 操作，$ f[u] = sum(f[v]) $
- 如果是 max 操作，$ f[u] = min(f[v]) $

# E. Serval and Snake

不难发现如果一个矩阵中有一端，那么交点为奇数， ~~然后乱搞就好了~~
枚举找到头和尾的行或者列，然后二分就好了。

~~然而可怜的 cycleke 常用二分方法超限了，不得不临时想（猜）其他方法~~
