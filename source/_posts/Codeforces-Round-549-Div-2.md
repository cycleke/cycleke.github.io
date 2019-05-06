---
title: 'Codeforces Round #549 (Div. 2)'
abbrlink: 9182a3d4
date: 2019-04-06 23:45:01
tags: 
  - codeforces
categories:
  - 训练
type: "categories"
---

# A.The Doors 
记录最后一个0和1的位置。

# B.Nirvana
对于每一位,答案有三种情况:

<!--more-->

1,取这位原本数字;

2,取x-1,同时让后一位取9;

3,让前面全取9;

# C.Queen 
一个点如果会被删，那么其他的点被删不会影响它最后被删的结果，判断一下那些点会被删，
然后排序。


# D.The Beatles 
显然\\( L = c \times k + a - b \\) 或 \\( L = c \times k - a - b \\)，对于每个L，$$ step =
lcm(n \times k,L) / L = n \times k / gcd(n \times k, L) $$ c从1取到n。


# E.Lynyrd Skynyrd
设begin_p[i]为以i结尾的环排列起点的最大位置，用倍增求begin_p[i],用前缀和优化就可
以解决。


# F.U2
对于每一个抛物线，满足\\( x_i^2 + bx_i + c \geq y_i \\)，即\\( x_i*b + c \geq y_i
-x_i^2 \\)，然后求个凸包，答案是交点个数。
