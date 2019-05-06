---
title: 'Codeforces Round #551 (Div. 2)'
tags:
  - codeforces
categories:
  - 训练
type: categories
abbrlink: 43db3ce0
date: 2019-04-23 22:54:40
---

# A. Serval and Bus
算出t之后每班车的最早时间，取最小值

# B. Serval and Toy Bricks
每个有方块的位置尽可能取高，即min(a[j], b[i])

<!--more-->

# C. Serval and Parenthesis Sequence
统计一下要放多少的(和),显然前面尽量放(是最优的，然后check判断

# D. Serval and Rooted Tree
考虑最下面两层，如果答案再这棵子树中，那么:
  - 如果是min操作，$ ans = k - leaves + 1 $
  - 如果是max操作，$ ans = leaves = k - 1 + 1 $

我们定义f[i]为i子树中的有效叶子，那么ans = k - f[1] + 1，且:
  - 如果是min操作，$ f[u] = sum(f[v]) $
  - 如果是max操作，$ f[u] = min(f[v]) $

# E. Serval and Snake
不难发现如果一个矩阵中有一端，那么交点为奇数， ~~然后乱搞就好了~~
枚举找到头和尾的行或者列，然后二分就好了。

~~然而可怜的cycleke常用二分方法超限了，不得不临时想(猜)其他方法~~
