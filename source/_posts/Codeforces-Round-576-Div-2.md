---
title: 'Codeforces Round #576 (Div. 2)'
tags:
  - codeforces
categories:
  - 训练
abbrlink: f96b9e58
date: 2019-07-31 18:26:32
---

| A    | B    | C      | D     | E    | F   |
| :-:  | :-:  | :-:    | :-:   | :-:  | :-: |
| 模拟 | 数学 | 双指针 | splay | 图论 | dp  |
| 1000 | 1000 | 1700   | 1600  | 2200 | 2400 |

<!--more-->

# A. City Day

因为x,y很小，直接模拟就行了。

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, x, y;
  cin >> n >> x >> y;

  vi a(n);
  FOR(i, 0, n) { cin >> a[i]; }

  FOR(i, 0, n) {
    int st = max(0, i - x);
    int ed = min(n - 1, i + y);

    bool flag = true;
    FOR(j, st, i) {
      if (a[j] <= a[i]) {
        flag = false;
        break;
      }
    }
    FOR(j, i + 1, ed + 1) {
      if (a[j] <= a[i]) {
        flag = false;
        break;
      }
    }

    if (!flag) {
      continue;
    }

    cout << i + 1 << "\n";
    break;
  }

  return 0;
}
```


# B. Water Lily

初中几何题。

```c++
int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  cout << fixed << setprecision(10);

  ld H, L;
  cin >> H >> L;
  cout << (L * L - H * H) / 2 / H << "\n";

  return 0;
}
```

# C. MP3

算出k值，之后算出区间不同值$\leq 2^k$的数字最多的区间,双指针维护。

```c++
const int MAXN = 4e5 + 3;

map<int, int> cnt;
pii a[MAXN];

int main() {

  // freopen("input.txt", "r", stdin);

  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n, I;
  cin >> n >> I;

  int k = 8 * I / n;

  FOR(i, 0, n) {
    int x;
    cin >> x;
    ++cnt[x];
  }

  // dbg(k);
  int m = 0;
  for (auto p : cnt) {
    a[m++] = p;
  }

  if (ceil(log(m) / log(2)) <= k) {
    cout << "0\n";
    return 0;
  }

  sort(a, a + m);
  int L = 1 << k, it = 0;
  int ans = 0, cur = 0;

  FOR(i, 0, m) {
    while (it < m && it - i + 1 <= L) {
      cur += a[it++].second;
    }
    ans = max(ans, cur);
    cur -= a[i].second;
  }

  cout << n - ans << "\n";
  return 0;
}
```

# D. Welfare State

单点修改或者将所有小于x的值改为x。

可以用splay维护, 也可以用分块。

```c++
const int MAXN = 2e5 + 3;

struct Node {
  int val, lazy;
  Node *fa, *ch[2];
} node_pool[MAXN], *pool_it, *root, *mp[MAXN], *nil;

void change(Node *u, int val) { u->val = u->lazy = val; }

void push_down(Node *u) {
  if (~u->lazy) {
    if (u->ch[0] != nil) {
      change(u->ch[0], u->lazy);
    }
    if (u->ch[1] != nil) {
      change(u->ch[1], u->lazy);
    }
    u->lazy = -1;
  }
}

void rot(Node *u) {
  Node *f = u->fa, *ff = f->fa;
  int d = u == f->ch[1];
  push_down(f);
  push_down(u);
  if ((f->ch[d] = u->ch[d ^ 1]) != nil)
    f->ch[d]->fa = f;
  if ((u->fa = ff) != nil)
    ff->ch[f == ff->ch[1]] = u;
  u->ch[d ^ 1] = f, f->fa = u;
}

void splay(Node *u, Node *target) {
  for (Node *f; u->fa != target; rot(u))
    if ((f = u->fa)->fa != target)
      ((u == f->ch[1]) ^ (f == f->fa->ch[1])) ? rot(u) : rot(f);
  if (target == nil)
    root = u;
}

void del(Node *u) {
  splay(u, nil);
  push_down(u);
  if (u->ch[0] == nil)
    root = u->ch[1], root->fa = nil;
  else {
    Node *v = u->ch[0];
    for (push_down(v); v->ch[1] != nil; push_down(v = v->ch[1]))
      ;
    splay(v, u);
    if ((v->ch[1] = u->ch[1]) != nil)
      v->ch[1]->fa = v;
    v->fa = nil;
    root = v;
  }
}

void ins(Node *u) {
  if (root == nil) {
    root = u;
    return;
  }

  Node *f = root;
  while (true) {
    int d = f->val < u->val;
    push_down(f);
    if (f->ch[d] == nil) {
      f->ch[d] = u;
      u->fa = f;
      break;
    } else {
      f = f->ch[d];
    }
  }
  splay(u, nil);
}

Node *find_it;
void find(Node *u, const int &val) {
  if (u == nil) {
    return;
  }
  push_down(u);
  if (val >= u->val) {
    find_it = u;
    find(u->ch[1], val);
  } else {
    find(u->ch[0], val);
  }
}

void all_down(Node *u) {
  if (u == nil) {
    return;
  }
  push_down(u);
  all_down(u->ch[0]);
  all_down(u->ch[1]);
}
int main() {

  // freopen("input.txt", "r", stdin);

  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;

  pool_it = node_pool;
  nil = pool_it++;
  nil->ch[0] = nil->ch[1] = nil->fa = nil;
  nil->val = nil->lazy = -1;
  root = nil;

  FOR(i, 0, n) {
    mp[i] = pool_it++;
    mp[i]->ch[0] = mp[i]->ch[1] = mp[i]->fa = nil;
    cin >> mp[i]->val;
    mp[i]->lazy = -1;

    ins(mp[i]);
  }

  int q;
  cin >> q;
  FOR(i, 0, q) {
    int op;
    cin >> op;
    if (op == 1) {
      int p, x;
      cin >> p >> x;
      del(mp[p - 1]);
      mp[p - 1]->val = x;
      mp[p - 1]->lazy = -1;
      mp[p - 1]->ch[0] = mp[p - 1]->ch[1] = mp[p - 1]->fa = nil;
      ins(mp[p - 1]);
    } else {
      int x;
      cin >> x;

      find_it = nil;
      find(root, x);
      if (find_it != nil) {
        splay(find_it, nil);
        push_down(root);
        root->val = x;
        if (root->ch[0] != nil) {
          change(root->ch[0], x);
        }
      }
    }
    // all_down(root);
    // FOR(i, 0, n) { cout << mp[i]->val << " "; }
    // cout << "\n";
  }

  all_down(root);
  FOR(i, 0, n) { cout << mp[i]->val << " "; }
  cout << "\n";
  return 0;
}
```

# E. Matching vs Independent Set

因为一共有3n个点,只用找出n个点,所以一定有解。

```c++
const int MAXN = 1e5 + 5;

bool mark[MAXN * 3];

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  MUL_CASE {
    int n, m;
    cin >> n >> m;

    vi ans;
    ans.clear();
    fill(mark, mark + 3 * n + 1, false);
    FOR(i, 0, m) {
      int u, v;
      cin >> u >> v;
      if (!mark[u] && !mark[v]) {
        mark[u] = mark[v] = true;
        ans.pb(i + 1);
      }
    }

    if (ans.size() >= n) {
      cout << "Matching\n";
      FOR(i, 0, n) { cout << ans[i] << " \n"[i == n - 1]; }
    } else {
      cout << "IndSet\n";
      int cnt = 0;
      FOR(i, 1, 3 * n + 1) {
        if (!mark[i]) {
          ++cnt;
          cout << i << " ";
          if (cnt == n) {
            cout << "\n";
            break;
          }
        }
      }
    }
  }

  return 0;
}
```

# F. Rectangle Painting 1

简单dp，感觉比E还要简单一点。

```c++

const int MAXN = 51;
char s[MAXN][MAXN];
int f[MAXN][MAXN][MAXN][MAXN];

int dfs(int x1, int y1, int x2, int y2) {
  if (x1 == x2 && y1 == y2) {
    return s[x1][y1] == '#' ? 1 : 0;
  
}
  if (~f[x1][y1][x2][y2]) {
    return f[x1][y1][x2][y2];
  
}
  int &ret = f[x1][y1][x2][y2];
  ret = max(y2 - y1 + 1, x2 - x1 + 1);

  FOR(i, x1, x2) {
    ret = min(ret, dfs(x1, y1, i, y2) + dfs(i + 1, y1, x2, y2));
  
}
  FOR(i, y1, y2) {
    ret = min(ret, dfs(x1, y1, x2, i) + dfs(x1, i + 1, x2, y2));
  
}

  debug("%d %d %d %d ret=%d\n", x1, y1, x2, y2, ret);
  return ret;

}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
  cout.tie(0);

  int n;
  cin >> n;
  FOR(i, 0, n) { cin >> s[i]; }
  memset(f, -1, sizeof(f));
  cout << dfs(0, 0, n - 1, n - 1) << "\n";
  return 0;

}
```
