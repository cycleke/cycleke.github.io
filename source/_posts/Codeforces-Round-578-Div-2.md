---
title: 'Codeforces Round #578 (Div. 2)'
tags:
  - codeforces
categories:
  - 训练
abbrlink: e7a2aeeb
date: 2019-08-12 09:50:51
---


| A    | B    | C    | D      | E   | F        |   |
| :-:  | :-:  | :-:  | :-:    | :-: | :-:      |   |
| 模拟 | 贪心 | 数学 | 前缀和 | kmp | dfs,数学 |   |
|      |      |      |        |     |          |   |

<!--more-->

# A. Hotelier

## 题意
给出长度为n的字符串，L表示有一个顾客从左边开始找房间，R表示有一个顾客从右边开始找房间，
'0'~'9'表示相应的房间的客人离开，最后输出每个房间的情况。

## 题解
暴力模拟

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  int n;
  string s;
  cin >> n >> s;
  vi a(10, 0);
  for (char ch : s) {
    if (ch == 'L') {
      FOR(i, 0, 10) {
        if (a[i] == 0) {
          a[i] = 1;
          break;
        }
      }
    } else if (ch == 'R') {
      REP(i, 0, 10) {
        if (a[i] == 0) {
          a[i] = 1;
          break;
        }
      }
    } else {
      a[ch - '0'] = 0;
    }
  }
  for (int x : a) {
    cout << x;
  }
  cout << "\n";
  return 0;
}
```

# B. Block Adventure

## 题意
n列方块，每列有 $ h_i $ 个方块。你有一个袋子初始有m个方块，容量无限，你初始在第1列。
你可以从当前所在列拿或放方块，如果$| h_i - h_{i+1}| \leq k$，你可以到下一列，问你能否到第n列。

## 题解
贪心，每次在当前列取尽量多的块。

```c++
const int MAXN = 105;

int h[MAXN];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  MUL_CASE {
    int n, k;
    ll m;
    cin >> n >> m >> k;
    FOR(i, 0, n) { cin >> h[i]; }
    FOR(i, 0, n - 1) {
      if (h[i] >= h[i + 1] - k) {
        m += h[i] - max(h[i + 1] - k, 0);
      } else if (h[i] + m < h[i + 1] - k) {
        m = -1;
        break;
      } else {
        m -= h[i + 1] - k - h[i];
      }
    }
    if (m >= 0) {
      cout << "YES\n";
    } else {
      cout << "NO\n";
    }
  }

  return 0;
}
```

# C. Round Corridor

## 题意
一个圆盘有两层，内层有n块，外层有m块，内外层之间无墙，层内有墙。
q次询问，问两个区域能否到达。

## 题解
每$(n / (n, m), m / (n, m))$块可以相通。

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  ll n, m;
  int q;
  cin >> n >> m >> q;
  ll g = __gcd(n, m);

  auto block = [&](ll x, ll y) {
    if (x == 1) {
      return (y - 1) / (n / g);
    } else {
      return (y - 1) / (m / g);
    }
  };

  while (q--) {
    ll sx, sy, ex, ey;
    cin >> sx >> sy >> ex >> ey;
    if (block(sx, sy) == block(ex, ey)) {
      cout << "YES\n";
    } else {
      cout << "NO\n";
    }
  }

  return 0;
}
```

# D. White Lines

## 题意
给出n * n的黑白方阵，你可以将一个k * k的矩阵变白。问最多可以有多少行/列全白。

## 题解
枚举k * k的矩阵，其对应的答案为除开此矩阵的白行/列+矩阵有多少行(列)左右(上下)都是白色。

```c++

const int MAXN = 2e3 + 5;

char s[MAXN][MAXN];
int cols[MAXN], rows[MAXN];
bool l[MAXN][MAXN], r[MAXN][MAXN], u[MAXN][MAXN], d[MAXN][MAXN], mark[MAXN];
int sc[MAXN][MAXN], sr[MAXN][MAXN];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  int n, k;
  cin >> n >> k;
  FOR(i, 1, n + 1) { cin >> (s[i] + 1); }

  FOR(i, 1, n + 1) {
    int x = 1;
    FOR(j, 1, n + 1) {
      if (s[i][j] == 'B') {
        x = 0;
        break;
      }
    }
    rows[i] = rows[i - 1] + x;
  }
  FOR(i, 1, n + 1) {
    int x = 1;
    FOR(j, 1, n + 1) {
      if (s[j][i] == 'B') {
        x = 0;
        break;
      }
    }
    cols[i] = cols[i - 1] + x;
  }

  fill(mark + 1, mark + n + 1, 1);
  FOR(i, 1, n + 1) {
    FOR(j, 1, n + 1) {
      u[i][j] = mark[j];
      if (s[i][j] == 'B') {
        mark[j] = 0;
      }
    }
  }
  fill(mark + 1, mark + n + 1, 1);
  FOR(j, 1, n + 1) {
    FOR(i, 1, n + 1) {
      l[i][j] = mark[i];
      if (s[i][j] == 'B') {
        mark[i] = 0;
      }
    }
  }
  fill(mark + 1, mark + n + 1, 1);
  REP(i, 1, n + 1) {
    FOR(j, 1, n + 1) {
      d[i][j] = mark[j];
      if (s[i][j] == 'B') {
        mark[j] = 0;
      }
    }
  }
  fill(mark + 1, mark + n + 1, 1);
  REP(j, 1, n + 1) {
    FOR(i, 1, n + 1) {
      r[i][j] = mark[i];
      if (s[i][j] == 'B') {
        mark[i] = 0;
      }
    }
  }
  /*

  FOR(i, 1, n + 1) { debug("%d rows:%d cols:%d\n", i, rows[i], cols[i]); }

  FOR2(i, 1, n + 1, j, 1, n + 1) {
    debug("(%d,%d) l:%d r:%d u:%d d:%d\n", i, j, l[i][j], r[i][j], u[i][j],
          d[i][j]);
  }
  */
  FOR2(i, 1, n + 1, j, 1, n + 1) {
    if (j >= k) {
      sr[i][j] = sr[i - 1][j] + (l[i][j - k + 1] && r[i][j]);
    }
    if (i >= k) {
      sc[i][j] = sc[i][j - 1] + (u[i - k + 1][j] && d[i][j]);
    }
    // debug("(%d,%d) sr:%d sc:%d\n", i, j, sr[i][j], sc[i][j]);
  }

  int ans = 0;
  FOR2(i, k, n + 1, j, k, n + 1) {
    ans =
        max(ans, rows[i - k] + rows[n] - rows[i] + sr[i][j] - sr[i - k][j] +
                     cols[j - k] + cols[n] - cols[j] + sc[i][j] - sc[i][j - k]);
  }
  cout << ans << "\n";

  return 0;
}
```

# E. Compress Words

## 题意
给出n个单词，将单词依次连接。
A和B连接回会删除B中suffix(A)和prefix(B)的最长相同部分。输出最终字符串。

## 题解
通过kmp可以求出LCSP。

```c++
void get_next(char *S, int *nxt, int n) {
  nxt[0] = -1;
  int j = -1;
  for (int i = 1; i < n; ++i) {
    while ((~j) && S[j + 1] != S[i]) {
      j = nxt[j];
    }
    nxt[i] = (S[j + 1] == S[i]) ? (++j) : j;
  }
}

int pattern(char *S, char *T, int *nxt, int n, int m) {
  int j = -1;
  for (int i = 0; i < m; ++i) {
    while ((~j) && S[j + 1] != T[i]) {
      j = nxt[j];
    }
    j += S[j + 1] == T[i];
  }
  return j;
}

const int MAXL = 1e6 + 5;
char s[MAXL], ans[MAXL];
int nxt[MAXL];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  int n;
  cin >> n;
  cin >> ans;
  int l2 = strlen(ans);
  FOR(i, 1, n) {
    cin >> s;
    int l1 = strlen(s);
    get_next(s, nxt, l1);
    int pl = min(l1, l2);
    int j = pattern(s, ans + l2 - pl, nxt, l1, pl);
    FOR(k, j + 1, l1) { ans[l2++] = s[k]; }
  }
  cout << ans << "\n";
  return 0;
}
```

# F. Graph Traveler

## 题意
n个点，每个点有$ k_i $和 $ m_i $出边。
每次旅行有初值c，每到达点v，c+=v，之后沿$ e[v][c mod m_i] $继续。
q次询问，问从x出发，初值为y，问有多少个点可以无限被访问。

## 题解
因为$ m_i < 10 $，所以可以将所有的权值对2250取模，所有状态数变为2250n，可以dfs算出答案。

```c++
const int MAXN = 1e3;
const int LCM = 2520;

int k[MAXN], e[10], ans[MAXN][LCM];
pii nxt[MAXN][LCM], mark[MAXN][LCM];

void dfs(int x, int y, const pii &st) {
  mark[x][y] = st;
  int _x = nxt[x][y].first;
  int _y = nxt[x][y].second;

  if (mark[_x][_y] == pii(-1, -1)) {
    dfs(_x, _y, st);
    ans[x][y] = ans[_x][_y];
  } else {
    if (mark[_x][_y] == st) {
      int cx = x;
      int cy = y;
      static set<int> s;
      s.clear();
      do {
        s.insert(cx);
        _x = nxt[cx][cy].first;
        _y = nxt[cx][cy].second;
        cx = _x;
        cy = _y;
      } while (cx != x || cy != y);
      ans[x][y] = s.size();
    } else {
      ans[x][y] = ans[_x][_y];
    }
  }
}

int main() {

  // freopen("input.txt", "r", stdin);

  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);
  cout << fixed << setprecision(10);

  int n;
  cin >> n;
  FOR(i, 0, n) {
    cin >> k[i];
    k[i] %= LCM;
    if (k[i] < 0) {
      k[i] += LCM;
    }
  }
  FOR(i, 0, n) {
    int m;
    cin >> m;
    FOR(j, 0, m) {
      cin >> e[j];
      --e[j];
    }
    FOR(j, 0, LCM) {
      int c = (k[i] + j) % LCM;
      int v = e[c % m];
      nxt[i][j] = {v, c};
    }
  }

  memset(ans, -1, sizeof(ans));
  memset(mark, -1, sizeof(mark));
  FOR(i, 0, n) {
    FOR(j, 0, LCM) {
      if (mark[i][j] != pii(-1, -1)) {
        continue;
      }
      dfs(i, j, {i, j});
    }
  }

  int q;
  cin >> q;
  while (q--) {
    int x, y;
    cin >> x >> y;
    cout << ans[x - 1][(y % LCM + LCM) % LCM] << "\n";
  }

  return 0;
}
```
