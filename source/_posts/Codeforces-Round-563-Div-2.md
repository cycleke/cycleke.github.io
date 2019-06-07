---
title: "Codeforces Round #563 (Div. 2)"
tags:
  - codeforces
categories:
  - 训练
type: categories
abbrlink: decc8adc
date: 2019-06-07 13:14:01
---

|  A   |  B   |     C      |      D       |    E     |   F    |
| :--: | :--: | :--------: | :----------: | :------: | :----: |
| 构造 | 排序 | 贪心，筛法 | 异或，前缀和 | 计数问题 | 点分治 |
| 1000 | 1200 |    1300    |     1900     |   2500   |  2300  |

<!--more-->

# A. Ehab Fails to Be Thanos

将数组排序，判断前半部分和后半部分的和是否相同。

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;
  n *= 2;
  vi a(n);
  FOR(i, 0, n) { cin >> a[i]; }
  sort(ALL(a));
  ll sum = 0, sum_half = 0;
  FOR(i, 0, n) {
    sum += a[i];
    if (i < n / 2) {
      sum_half += a[i];
    }
  }
  if (sum_half * 2 < sum) {
    for (int x : a) {
      cout << x << " ";
    }
    cout << "\n";
  } else {
    cout << "-1\n";
  }

  return 0;
}

```

# B. Ehab Is an Odd Person

若数组有奇数有偶数，那么我们总可以通过交换将数组排序，判断一下奇偶就好了

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;
  vi a(n);
  bool odd = false, even = false;
  FOR(i, 0, n) {
    cin >> a[i];
    odd |= a[i] & 1;
    even |= ~a[i] & 1;
  }
  if (odd && even) {
    sort(ALL(a));
  }
  for (int x : a) {
    cout << x << " ";
  }
  cout << "\n";

  return 0;
}
```

# C. Ehab and a Special Coloring Problem

将每个质数的倍数赋予相同的值就好了

```c++
const int MAXN = 1e5 + 3;
int a[MAXN];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;
  int cur = 1;
  FOR(i, 2, n + 1) {
    if (a[i]) {
      continue;
    }
    for (int j = i; j <= n; j += i) {
      a[j] = cur;
    }
    ++cur;
  }
  FOR(i, 2, n + 1) { cout << a[i] << " "; }
  cout << "\n";

  return 0;
}
```

# D. Ehab and the Expected XOR Problem

将子串通过前缀和就可以变为两个数的异或值。现在的要求就是求一个集合，使得其中没有两个数的异或值为 x

```c++
const int MAXS = 1 << 18 | 1;

bool mark[MAXS];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, x;
  cin >> n >> x;
  mark[0] = true;
  vi a({0});

  FOR(i, 1, (1 << n)) {
    if (!mark[i ^ x]) {
      mark[i] = true;
      a.pb(i);
    }
  }
  cout << a.size() - 1 << "\n";
  FOR(i, 1, a.size()) { cout << (a[i] ^ a[i - 1]) << " "; }
  cout << "\n";
  return 0;
}
```

# E. Ehab and the Expected GCD Problem

不懂，官方题解说所有符合条件的序列的 gcd 为$2^x \times 3^y$，因为和相同时将数拆分为 2 和 3（最多 1 个 3）可
以获得最多的因数。

定义 dp[i][x][y]，表示到第 i 位，gcd 为$2^x \times 3^y$的方案数。

```c++
const int MAXN = 1e6 + 3;
int dp[MAXN][21][2];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;
  int p = 0;
  while ((1 << p) <= n) {
    ++p;
  }

  auto gao = [&](const int &x, const int &y) {
    ll t = n >> x;
    return y ? t / 3 : t;
  };
  auto add = [&](int &x, const int &y) {
    x += y;
    if (x >= MOD) {
      x -= MOD;
    }
  };

  dp[1][p - 1][0] = 1;
  dp[1][p - 2][1] = gao(p - 2, 1);
  FOR(i, 1, n) {
    FOR(x, 0, p) {
      FOR(y, 0, 2) {
        add(dp[i + 1][x][y], dp[i][x][y] * (gao(x, y) - i) % MOD);
        if (x) {
          add(dp[i + 1][x - 1][y],
              dp[i][x][y] * (gao(x - 1, y) - gao(x, y)) % MOD);
        }
        if (y) {
          add(dp[i + 1][x][y - 1],
              dp[i][x][y] * (gao(x, y - 1) - gao(x, y)) % MOD);
        }
      }
    }
  }
  cout << dp[n][0][0] << "\n";

  return 0;
}
```

# F. Ehab and the Big Finale

点分治，每次判断是在重心的上方还是下方。

```c++
const int MAXN = 2e5 + 3;

int interact(char oper, int u) {
  printf("%c %d\n", oper, u);
  fflush(stdout);
  int res;
  scanf("%d", &res);
  return res;
}

vi adj[MAXN];
bool deleted[MAXN];
int fa[MAXN], size[MAXN], dep[MAXN], depx;

void dfs(int u) {
  for (int v : adj[u]) {
    if (v == fa[u]) {
      continue;
    }
    fa[v] = u;
    dep[v] = dep[u] + 1;
    dfs(v);
  }
}

int sub_size, root, root_key;

void get_root(int u, int from) {
  int key = 0;
  size[u] = 1;
  for (int v : adj[u]) {
    if (deleted[v] || v == from) {
      continue;
    }
    get_root(v, u);
    size[u] += size[v];
    key = max(key, size[v]);
  }
  key = max(key, sub_size - key);
  if (key < root_key) {
    root = u;
    root_key = key;
    // debug("root = %d root_key = %d\n", root, root_key);
  }
  // debug("u = %d key = %d\n", u, key);
}

int gao(int u) {
  if (size[u] == 1) {
    return u;
  }
  sub_size = size[u];
  root = u;
  root_key = sub_size;
  get_root(u, 0);
  int dist = interact('d', root);
  if (dist == 0) {
    return root;
  }
  deleted[root] = true;
  if (dep[root] + dist == depx) {
    return gao(interact('s', root));
  } else {
    return gao(fa[root]);
  }
}

int main() {
  int n;
  scanf("%d", &n);

  FOR(i, 1, n) {
    int u, v;
    scanf("%d %d", &u, &v);
    adj[u].pb(v);
    adj[v].pb(u);
  }
  dfs(1);

  depx = interact('d', 1);
  size[1] = n;
  printf("! %d\n", gao(1));

  return 0;
}
```
