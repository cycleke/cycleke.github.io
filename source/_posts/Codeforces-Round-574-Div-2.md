---
title: 'Codeforces Round #574 (Div. 2)'
tags:
  - codeforces
categories:
  - 训练
type: categories
abbrlink: fd9e4e65
date: 2019-07-30 09:16:48
---

| A    | B    | C    | D          | E             |
| :-:  | :-:  | :-:  | :-:        | :-:           |
| 贪心 | 数学 | dp   | 计数，组合 | 单调队列，rmq |
| 1100 | 1100 | 1400 | 1700       | 2100          |


<!--more-->

# A. Drinks Choosing

有两个就凑一对，之后落单的除二。

```c++
const int MAXN = 1e3 + 3;

int cnt[MAXN];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, k;
  cin >> n >> k;
  FOR(i, 0, n) {
    int x;
    cin >> x;
    ++cnt[x - 1];
  }

  int res = 0, odd = 0;
  FOR(i, 0, k) {
    res += cnt[i] / 2;
    odd += cnt[i] % 2;
  }
  res *= 2;
  res += (odd + 1) / 2;
  // dbg(odd);
  cout << res << "\n";

  return 0;
}
```

# B. Sport Mafia

解方程。

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, k;
  cin >> n >> k;

  double delta = 9.0 / 4 + 2LL * (k + n);
  cout << n - int(-1.5 + sqrt(delta) + 1e-9) << "\n";

  return 0;
}
```

# C. Basketball Exercise

dp，记录当前列取1，2和不取的最优情况。

```c++
const int MAXN = 1e5 + 3;

int h[2][MAXN];
ll dp[3][MAXN];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;

  FOR(i, 0, n) { cin >> h[0][i]; }
  FOR(i, 0, n) { cin >> h[1][i]; }

  dp[0][0] = h[0][0];
  dp[1][0] = h[1][0];
  dp[2][0] = 0;

  FOR(i, 1, n) {
    dp[0][i] = max(dp[1][i - 1], dp[2][i - 1]) + h[0][i];
    dp[1][i] = max(dp[0][i - 1], dp[2][i - 1]) + h[1][i];
    dp[2][i] = max(dp[0][i - 1], dp[1][i - 1]);
    dp[2][i] = max(dp[2][i], dp[2][i - 1]);
  }

  cout << max(max(dp[0][n - 1], dp[1][n - 1]), dp[2][n - 1]) << "\n";

  return 0;
}
```

# D. Submarine in the Rybinsk Sea

显然a和b对于答案的影响是相对独立的，只要记录每个长度的数的个数，之后分情况讨论。


```c++
const int MAXN = 1e5 + 3;
const int MAXL = 11;

string nums[MAXN];
int cnt[MAXL];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;

  FOR(i, 0, n) {
    cin >> nums[i];
    ++cnt[nums[i].length()];
  }

  auto first = [&](string num, int l) {
    int res = 0, n = num.length(), p = 1;

    reverse(ALL(num));

    FOR(i, 0, max(n, l)) {
      if (i < l) {
        p = p * 10LL % MOD;
      }
      if (i < n) {
        res = (res + 1LL * (num[i] - '0') * p) % MOD;
        p = p * 10LL % MOD;
      }
    }
    return res;
  };

  auto second = [&](string num, int l) {
    int res = 0, n = num.length(), p = 1;

    reverse(ALL(num));

    FOR(i, 0, max(n, l)) {
      if (i < n) {
        res = (res + 1LL * (num[i] - '0') * p) % MOD;
        p = p * 10LL % MOD;
      }
      if (i < l) {
        p = p * 10LL % MOD;
      }
    }
    return res;
  };

  int ans = 0;
  FOR(i, 0, n) {
    FOR(j, 0, MAXL) {
      if (cnt[j] == 0) {
        continue;
      }

      int t = first(nums[i], j);
      ans = (ans + 1LL * t * cnt[j]) % MOD;
      t = second(nums[i], j);
      ans = (ans + 1LL * t * cnt[j]) % MOD;
    }
  }

  cout << ans << "\n";
  return 0;
}
```

# E. OpenStreetMap

枚举矩阵求最小值就好了，可以用rmq，也可以用单调队列。

```c++
const int MAXN = 3e3 + 3;

int h[MAXN][MAXN], mi[MAXN][MAXN];
int que[MAXN], head, tail;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, m, a, b, x, y, z, g;
  cin >> n >> m >> a >> b;
  cin >> g >> x >> y >> z;

  FOR2(i, 0, n, j, 0, m) {
    h[i][j] = g;
    g = (1LL * x * g + y) % z;
  }

  FOR(i, 0, n) {
    head = tail = 0;
    FOR(j, 0, b) {
      while (head < tail && h[i][j] <= h[i][que[tail - 1]]) {
        --tail;
      }
      que[tail++] = j;
    }
    mi[i][b - 1] = h[i][que[head]];

    FOR(j, b, m) {
      if (j - que[head] >= b) {
        ++head;
      }
      while (head < tail && h[i][j] <= h[i][que[tail - 1]]) {
        --tail;
      }
      que[tail++] = j;
      mi[i][j] = h[i][que[head]];
    }
  }

  ll ans = 0;
  FOR(j, b - 1, m) {
    head = tail = 0;
    FOR(i, 0, a) {
      while (head < tail && mi[i][j] <= mi[que[tail - 1]][j]) {
        --tail;
      }
      que[tail++] = i;
    }
    ans += mi[que[head]][j];
    FOR(i, a, n) {
      if (i - que[head] >= a) {
        ++head;
      }
      while (head < tail && mi[i][j] <= mi[que[tail - 1]][j]) {
        --tail;
      }
      que[tail++] = i;
      ans += mi[que[head]][j];
    }
  }

  cout << ans << "\n";

  return 0;
}
```
