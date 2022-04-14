# 树上dsu总结


# dsu 树上启发式合并 刷题

## 套路代码（一）

```cpp
int son[M], dep[M], siz[M]; // son记录重儿子
// 寻找重儿子
void dfs(int u, int fa) {
	siz[u] = 1;
	dep[u] = dep[fa] + 1; // 记录深度，有些题不需要
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v == fa) continue;
		dfs(v, u);
		siz[u] += siz[v];
		if (siz[v] > siz[son[u]]) son[u] = v;
	}
}
int flag;
void cal(int u, int fa, int k) {
	// 计算
	// 遍历孩子，并利用 flag 避开重儿子
}

void dsu(int u, int fa, int keep) {
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v != fa && v != son[u]) dsu(v, u, 0);
	}
	if (son[u]) dsu(son[u], u, 1), flag = son[u]; // 如果存在重儿子,一定要最后计算重儿子
	cal(u, fa, 1); // 计算
	if (flag) flag = 0;
	if (!keep) cal(u, fa, -1); //删除
}
```

## 套路代码（二）

和（一）的本质是一样的，虽然（二）代码比较长，但感觉好理解也实用（对子树根结点有特殊处理的题有奇效）

```cpp
int son[M], dep[M], siz[M]; // son记录重儿子
// 寻找重儿子
void dfs(int u, int fa) {
	siz[u] = 1;
	dep[u] = dep[fa] + 1; // 记录深度，有些题不需要
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v == fa) continue;
		dfs(v, u);
		siz[u] += siz[v];
		if (siz[v] > siz[son[u]]) son[u] = v;
	}
}
void cal(int u, int fa, int k) {
	// 计算
}

void dsu(int u, int fa, int keep) {
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v != fa && v != son[u]) dsu(v, u, 0);
	}
	if (son[u]) dsu(son[u], u, 1); // 如果存在重儿子,一定要最后计算重儿子
    /* 加上本结点 */
    for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v != fa && v != son[u]) cal(v, u, 1); //避开重儿子
	}
	if (!keep) { // 删除
        /*减去本结点 */
        for (/*遍历下一个结点*/) {
            v = /*下一个结点*/;
            if (v != fa) cal(v, u, -1); // 注意，这里不用判重儿子
        }
    }
}
```



## 例题

### [CF600E Lomsat gelral](https://codeforces.com/problemset/problem/600/E)

```cpp
/*
题意：
有一棵 个结点的以 1 号结点为根的有根树。
每个结点都有一个颜色，颜色是以编号表示的，i 号结点的颜色编号为 ci
如果一种颜色在以 x 为根的子树内出现次数最多，称其在以 x 为根的子树中占主导地位。
显然，同一子树中可能有多种颜色占主导地位。
你的任务是对于每一个 i∈[1,n]，求出以 i 为根的子树中，占主导地位的颜色的编号和。
N<=1e5,ci<=n
*/
#include <algorithm>
#include <cstdio>
#include <cstring>
#define ll long long
#define rep(u, v, e) for (int i = head[u], v = e[i].to; ~i; i = e[i].next, v = e[i].to)
using namespace std;
const int N = 1e5 + 5, M = 2e5 + 5;

int head[N], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = -1; }

struct edges {
    int to, next;
    int w;
    void add(int t, int n, int w) {
        to = t, next = n, this->w = w;
    }
}edge[N << 1]; //无向图则需要乘2

inline void add(int u, int v, int w = 1) {
    edge[++cnt].add(v, head[u], w);
    head[u] = cnt;
}
int son[N], siz[N];
void dfs(int u, int fa) {
    siz[u] = 1;
    rep(u, v, edge) {
        if (v == fa) continue;
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v;
    }
}
ll col[N], ans[N], flag, num[N], now, sum, tot;

void cal(int u, int fa, int k) {
    num[col[u]] += k;
    if (k > 0) {
        if (num[col[u]] == tot) sum += col[u];
        else if (num[col[u]] > tot) tot = num[col[u]], sum = col[u];
    }
    rep(u, v, edge) {
        if (v == fa || v == flag) continue;
        cal(v, u, k);
    }
}

void dsu(int u, int fa, int keep) {
    rep(u, v, edge) {
        if (v == fa || v == son[u]) continue;
        dsu(v, u, 0);
    }
    if (son[u]) dsu(son[u], u, 1), flag = son[u];
    now = u;
    cal(u, fa, 1);
    ans[u] = sum;
    if (flag) flag = 0;
    if (!keep) cal(u, fa, -1), tot = 0;
}

int main() {
    int n;
    scanf("%d", &n);
    init();
    for (int i = 1; i <= n; ++i) scanf("%lld", col + i);
    for (int i = 1; i < n; ++i) {
        int u, v;
        scanf("%d %d", &u, &v);
        add(u, v), add(v, u);
    }
    dfs(1, 0);
    dsu(1, 0, 1);
    for (int i = 1; i <= n; ++i) printf("%lld ", ans[i]);
    return 0;
}
```

- 反思：本题第二次做，sum值必须要开全局且是单值，而不是数组（即记录每个节点的答案），而是如代码所示的做法，直接用ans在dsu内复制，用sum值不开数组开全局的好处就是会一直保留重儿子的信息‘

### [CF570D Tree Requests](https://codeforces.com/contest/570/problem/D)

```cpp
/* 
给定一个以1为根的n个节点的树，每个点上有一个字母(a−z)
每个点的深度定义为该节点到1号节点路径上的点数.
每次询问 a,b， 查询以 a 为根的子树内深度为 b 的节点上的字母重新排列之后是否能构成回文串.
*/
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;
const int N = 5e5 + 5;
vector<int> mp[N];
vector<pair<int, int>> q[N];
char s[N];
int col[N], dep[N], son[N], siz[N];

void dfs(int u) {
    siz[u] = 1;
    for (auto v : mp[u]) {
        dep[v] = dep[u] + 1;
        dfs(v);
        siz[u] += siz[v];
        if (siz[son[u]] < siz[v]) son[u] = v;
    }
}
int cnt[N][30]; // cnt[i][c] 深度为i的字符c出现次数
int ans[N];

void cal(int u, int k) {
    cnt[dep[u]][col[u]] += k;
    for (auto v : mp[u]) {
        cal(v, k);
    }
}

void dsu(int u, int keep) {
    for (auto v : mp[u]) if (v != son[u]) dsu(v, 0);
    if (son[u]) dsu(son[u], 1);
    cnt[dep[u]][col[u]] += 1;
    for (auto v : mp[u]) {
        if (v != son[u]) {
            cal(v, 1);
        }
    }
    for (auto& [d, id] : q[u]) {
        int num = 0;
        for (int i = 0; i < 26; ++i) num += cnt[d][i] & 1;
        ans[id] = num > 1 ? 0 : 1;
    }
    if (keep == 0) {
        cnt[dep[u]][col[u]] -= 1;
        for (auto v : mp[u]) {
            cal(v, -1);
        }
    }
}

int main() {
    int n, m;
    scanf("%d %d", &n, &m);
    for (int i = 2; i <= n; ++i) {
        int v;
        scanf("%d", &v);
        mp[v].push_back(i);
    }
    scanf("%s", s + 1);
    for (int i = 1; i <= n; ++i) {
        col[i] = s[i] - 'a';
    }
    dep[1] = 1;
    dfs(1);
    for (int i = 0; i < m; ++i) {
        int u, d;
        scanf("%d %d", &u, &d);
        q[u].push_back({d, i});
    }
    dsu(1, 1);
    for (int i = 0; i < m; ++i) {
        puts(ans[i] ? "Yes" : "No");
    }
    return 0;
}
```

### [CF208E Blood Cousins](https://codeforces.com/contest/208/problem/E)

```cpp
//给你一片森林，每次询问一个点与多少个点拥有共同的K级祖先
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;
const int N = 1e5 + 5;
vector<int> mp[N];
vector<pair<int, int>> q[N];
int dep[N], son[N], siz[N], fa[N][20];

void dfs(int u) {
    siz[u] = 1;
    for (auto v : mp[u]) {
        dep[v] = dep[u] + 1, fa[v][0] = u;
        for (int i = 1; (1 << i) <= dep[v]; ++i) {
            fa[v][i] = fa[fa[v][i - 1]][i - 1];
        }
        dfs(v);
        siz[u] += siz[v];
        if (siz[son[u]] < siz[v]) son[u] = v;
    }
}

int pth(int u, int p) { // 找u的p级祖先是谁
    if (dep[u] <= p) return 0;
    for (int i = 20; i >= 0; --i) {
        if (p >= (1 << i)) u = fa[u][i], p -= (1 << i);
    }
    return u;
}

int cnt[N]; // 深度为i的个数
int ans[N];

void cal(int u, int k) {
    cnt[dep[u]] += k;
    for (int v : mp[u]) {
        cal(v, k);
    }
}

void dsu(int u, int keep) {
    for (int v : mp[u]) if (v != son[u]) dsu(v, 0);
    if (son[u]) dsu(son[u], 1);
    cnt[dep[u]] += 1;
    for (int v : mp[u]) {
        if (v != son[u]) {
            cal(v, 1);
        }
    }
    for (auto [d, id] : q[u]) {
        if (cnt[d]) ans[id] = cnt[d] - 1;
        else ans[id] = 0; // 应该不会走这个判断
    }
    if (keep == 0) {
        cnt[dep[u]] -= 1;
        for (int v : mp[u]) cal(v, -1);
    }
}

int main() {
    int n, m;
    scanf("%d", &n);
    for (int i = 1; i <= n; ++i) {
        int fa;
        scanf("%d", &fa);
        mp[fa].push_back(i);
    }
    dfs(0);
    scanf("%d", &m);
    for (int i = 0; i < m; ++i) {
        int u, p;
        scanf("%d %d", &u, &p);
        int f = pth(u, p);
        if (f) q[f].push_back({p + dep[f], i}); // 将问题转化为以f为根结点的子树，距离0号结点的深度为p + dep[f]的结点个数
    }
    dsu(0, 1);
    for (int i = 0; i < m; ++i) {
        printf("%d ", ans[i]);
    }
    return 0;
}
```

### [CF246E Blood Cousins Return](https://codeforces.com/contest/246/problem/E)

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
#include <map>
using namespace std;
const int N = 1e5 + 5;
vector<int> mp[N];
vector<pair<int, int>> q[N];
int dep[N], son[N], siz[N], col[N];

void dfs(int u) {
    siz[u] = 1;
    for (auto v : mp[u]) {
        dep[v] = dep[u] + 1;
        dfs(v);
        siz[u] += siz[v];
        if (siz[son[u]] < siz[v]) son[u] = v;
    }
}

map<int, int> cnt[N << 1];
int ans[N];

void cal(int u, int k) {
    cnt[dep[u]][col[u]] += k;
    if (cnt[dep[u]][col[u]] == 0) cnt[dep[u]].erase(col[u]);
    for (int v : mp[u]) {
        cal(v, k);
    }
}

void dsu(int u, int keep) {
    for (int v : mp[u]) if (v != son[u]) dsu(v, 0);
    if (son[u]) dsu(son[u], 1);
    cnt[dep[u]][col[u]] += 1;
    for (int v : mp[u]) {
        if (v != son[u]) {
            cal(v, 1);
        }
    }
    for (auto [d, id] : q[u]) {
        ans[id] = cnt[d].size();
    }
    if (keep == 0) {
        cnt[dep[u]][col[u]] -= 1;
        if (cnt[dep[u]][col[u]] == 0) cnt[dep[u]].erase(col[u]);
        for (int v : mp[u]) cal(v, -1);
    }
}

int main() {
    int n, m, cnt = 0;
    scanf("%d\n", &n);
    map<string, int> ms;
    for (int i = 1; i <= n; ++i) {
        char ch[30];
        int fa;
        scanf("%s %d\n", ch, &fa);
        string s(ch);
        if (ms.count(s) == 0) ms[s] = ++cnt;
        col[i] = ms[s];
        mp[fa].push_back(i);
    }
    dfs(0);
    scanf("%d", &m);
    for (int i = 0; i < m; ++i) {
        int u, k;
        scanf("%d %d", &u, &k);
        q[u].push_back({k + dep[u], i});
    }
    dsu(0, 1);
    for (int i = 0; i < m; ++i) {
        printf("%d\n", ans[i]);
    }
    return 0;
}

```

### [CF1009F Dominant Indices](https://codeforces.com/contest/1009/problem/F)

这题的求解方式一开始想的是套个线段树+二分找，但发现直接记录最大的d(u,k)和最小的k就好了，这也突出了最后再计算重儿子的重要性

```cpp
/*
给定一棵以 1 为根，n 个节点的树。设 d(u,x) 为 u 子树中到 u 距离为 x 的节点数。
对于每个点，求一个最小的 k，使得 d(u,k)最大
*/
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

const int N = 1e6 + 5;

vector<int> mp[N];
int dep[N], siz[N], son[N];
void dfs(int u, int fa) {
    siz[u] = 1, dep[u] = dep[fa] + 1;
    for (int v : mp[u]) {
        if (v == fa) continue;
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v;
    }
}
int ans[N], cnt[N], res, maxd;// cnt[i] 深度为i的个数

void cal(int u, int fa, int k, int flag) {
    if (u == flag) return;
    cnt[dep[u]] += k;
    if (k > 0 && cnt[dep[u]] >= maxd) {
        if  (cnt[dep[u]] > maxd) maxd = cnt[dep[u]], res = dep[u];
        else if (res > dep[u]) res = dep[u];
    }
    for (int v : mp[u]) {
        if (v == fa) continue;
        cal(v, u, k, flag);
    }
}

void dsu(int u, int fa, int keep) {
    for (int v : mp[u]) {
        if (v == fa || v == son[u]) continue;
        dsu(v, u, 0);
    }
    if (son[u]) dsu(son[u], u, 1); // 最后再计算重儿子
    cal(u, fa, 1, son[u]);
    ans[u] = res - dep[u];
    if (keep == 0) {
        cal(u, fa, -1, 0);
        maxd = res = 0; // 置0，不用担心，因为最后计算重儿子，不会影响u父亲fa的子树的重儿子的maxd和res值
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    // freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    int n;
    scanf("%d", &n);
    for (int i = 1; i < n; ++i) {
        int u, v;
        scanf("%d %d", &u, &v);
        mp[u].push_back(v);
        mp[v].push_back(u);
    }
    dfs(1, 0);
    dsu(1, 0, 1);
    for (int i = 1; i <= n; ++i) {
        printf("%d\n", ans[i]);
    }
    return 0;
}
```

### [CF375D Tree and Queries](https://codeforces.com/contest/375/problem/D)

```cpp
// 问你以u为子树，颜色出现次数大于等于k的个数
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

const int N = 1e5 + 5;

vector<int> mp[N];
vector<pair<int, int>> q[N];
int dep[N], siz[N], son[N], col[N];
void dfs(int u, int fa) {
    siz[u] = 1;
    for (int v : mp[u]) {
        if (v == fa) continue;
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v;
    }
}
int ans[N], cnt[N], num[N]; // cnt[i] 颜色为i的个数, num[i] 颜色数量为i的数量

void cal(int u, int fa, int k, int flag) {
    if (u == flag) return;
    if (k == -1) num[cnt[col[u]]] -= 1;
    cnt[col[u]] += k;
    if (k == 1) num[cnt[col[u]]] += 1;
    for (int v : mp[u]) {
        if (v == fa) continue;
        cal(v, u, k, flag);
    }
}

void dsu(int u, int fa, int keep) {
    for (int v : mp[u]) {
        if (v == fa || v == son[u]) continue;
        dsu(v, u, 0);
    }
    if (son[u]) dsu(son[u], u, 1); // 最后再计算重儿子
    cal(u, fa, 1, son[u]);
    for (auto [k, id] : q[u]) ans[id] = num[k];
    if (keep == 0) cal(u, fa, -1, 0);
}

int main() {
    int n, m;
    scanf("%d %d", &n, &m);
    for (int i = 1; i <= n; ++i) scanf("%d", col + i);
    for (int i = 1; i < n; ++i) {
        int u, v;
        scanf("%d %d", &u, &v);
        mp[u].push_back(v);
        mp[v].push_back(u);
    }
    dfs(1, 0);
    for (int i = 0; i < m; ++i) {
        int u, k;
        scanf("%d %d", &u, &k);
        q[u].push_back({k, i});
    }
    dsu(1, 0, 1);
    for (int i = 0; i < m; ++i) printf("%d\n", ans[i]);
    return 0;
}
```
