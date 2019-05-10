---
title: "Codeforces Round #556 (Div. 1)"
tags:
  - codeforces
categories:
  - 训练
type: categories
abbrlink: 8a2a745a
date: 2019-05-09 23:43:07
---

|     A      |  B   |   C    |
| :--------: | :--: | :----: |
| 构造，数论 |  dp  | 线段树 |
|    1300    | 2200 |  2900  |

<!--more-->

# A. Prefix Sum Primes

为偶数的质数只有 2，所以我们可以首先构造 2,3，之后挨个加 2，生成所有奇数，再将所有 1 放在后面。

```python
input()
a = sorted(map(int, input().split()))[::-1]
a[1:1] = a.pop(),
print(*a)
```

# B. Three Religions

定义$ dp[a][b][c] $为三个字符串匹配的长度分别为 a,b,c 时所需最小长度。
预处理每个位置下一个字符的位置就可以$ O(1) $ 转移。
每次添加字符会对另外两个字符串和新添加的那个字符的匹配产生影响，所以只用枚举 $ O(L^2) $个状态。

```cpp
const int MAXN = 1e5 + 3;
const int MAXT = 255;
const int ALPHA_SIZE = 26;

char s[MAXN], t[3][MAXT];
int dp[MAXT][MAXT][MAXT], nxt[MAXN][ALPHA_SIZE], lt[3];

int main() {

  // freopen("input.txt", "r", stdin);

  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, m;
  cin >> n >> m >> s;

  FOR(i, 0, ALPHA_SIZE) { nxt[n][i] = nxt[n + 1][i] = n; }
  REP(i, 0, n) FOR(j, 0, ALPHA_SIZE) {
    nxt[i][j] = (s[i] == 'a' + j) ? i : nxt[i + 1][j];
  }

  auto gao = [&](int a, int b, int c) {
    int &x = dp[a][b][c];
    x = n;
    if (a > 0) {
      x = min(x, nxt[dp[a - 1][b][c] + 1][t[0][a - 1] - 'a']);
    }
    if (b > 0) {
      x = min(x, nxt[dp[a][b - 1][c] + 1][t[1][b - 1] - 'a']);
    }
    if (c > 0) {
      x = min(x, nxt[dp[a][b][c - 1] + 1][t[2][c - 1] - 'a']);
    }
  };

  dp[0][0][0] = -1;
  while (m--) {
    char op;
    int idx;
    cin >> op >> idx;
    // assert(op == '+' || op == '-');
    --idx;

    if (op == '+') {
      char ch;
      cin >> ch;
      t[idx][lt[idx]++] = ch;

      int ed0 = lt[0];
      int ed1 = lt[1];
      int ed2 = lt[2];
      int bg0 = idx != 0 ? 0 : ed0;
      int bg1 = idx != 1 ? 0 : ed1;
      int bg2 = idx != 2 ? 0 : ed2;

      FOR(i, bg0, ed0 + 1) FOR2(j, bg1, ed1 + 1, k, bg2, ed2 + 1) {
        gao(i, j, k);
      }
    } else {
      --lt[idx];
    }

    cout << (dp[lt[0]][lt[1]][lt[2]] < n ? "Yes\n" : "No\n");
  }
  return 0;
}
```

# C. Tree Generator™

对于每个节点而言，它的深度就是匹配到它左括号时栈的大小，所以可以将问题转换为给出一个数列 a，求 $ max \lbrace a[i] + a[j] - 2 * a[k] \rbrace $。
对于这个问题，我们就可以用线段树维护。
~~~PS:括号序列长度为 2N，我以前可是用这个坑过别人的 emmmmm~~~

```cpp

const int MAXN = 2e5 + 3;

struct Data {
  int prefix;
  int min_dep, max_dep;
  int max_lv, max_vr, max_lvr;
  Data AddDepth(int delta) const {
    return (Data){prefix + delta, min_dep + delta, max_dep + delta,
                  max_lv - delta, max_vr - delta,  max_lvr};
  }
};

struct Node {
  Data data;
  Node *left, *right;
} node_pool[MAXN * 2], *root;

static Data NewLeftData() { return (Data){1, 0, 1, 0, 1, 1}; }

static Data NewRightData() { return (Data){-1, -1, 0, 2, 1, 1}; }

static Data operator+(const Data &lhs, const Data &rhs) {
  Data rhs_added = rhs.AddDepth(lhs.prefix);

  return (Data){
      rhs_added.prefix,
      min(lhs.min_dep, rhs_added.min_dep),
      max(lhs.max_dep, rhs_added.max_dep),
      max({lhs.max_lv, rhs_added.max_lv, lhs.max_dep - 2 * rhs_added.min_dep}),
      max({lhs.max_vr, rhs_added.max_vr, -2 * lhs.min_dep + rhs_added.max_dep}),
      max({lhs.max_lvr, rhs_added.max_lvr, lhs.max_lv + rhs_added.max_dep,
           lhs.max_dep + rhs_added.max_vr})};
}

char bracket[MAXN];

void build(Node *&u, int l, int r) {
  static Node *it = node_pool;
  u = it;
  ++it;
  if (l == r) {
    u->data = (bracket[l] == '(') ? NewLeftData() : NewRightData();
  } else {
    int mid = (l + r) / 2;
    build(u->left, l, mid);
    build(u->right, mid + 1, r);
    u->data = u->left->data + u->right->data;
  }
}

void modify(Node *&u, int l, int r, const int &pos, const char &ch) {
  // assert(u != NULL);
  if (l == r) {
    u->data = (ch == '(') ? NewLeftData() : NewRightData();
    return;
  }
  int mid = (l + r) / 2;
  if (pos <= mid) {
    modify(u->left, l, mid, pos, ch);
  } else {
    modify(u->right, mid + 1, r, pos, ch);
  }
  u->data = u->left->data + u->right->data;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);

  int n, m;
  cin >> n >> m >> bracket;
  n = 2 * n - 3;
  build(root, 0, n);
  cout << root->data.max_lvr << "\n";

  while (m--) {
    int a, b;
    cin >> a >> b;
    --a, --b;
    modify(root, 0, n, a, bracket[b]);
    modify(root, 0, n, b, bracket[a]);
    swap(bracket[a], bracket[b]);

    cout << root->data.max_lvr << "\n";
  }

  return 0;
}
```
