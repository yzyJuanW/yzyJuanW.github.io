# 动态规划套路板子


### 1. 树形dp

#### 树的最大独立集

```cpp
/*
Loj 10160
每个点都有一个快乐值，子结点和父节点不能同时被选，问你最大的快乐值
dp[i][0]表示第i号结点不选时最大的快乐值
dp[i][1]表示第i号结点选时的最大的快乐值
*/
#include <iostream>
#include <algorithm>
#include <cstdio>
#include <cctype>
#include <cstring>
using namespace std;
const int M = 6e3 + 5;
inline long long IO() {} // 快读略
struct es{
    int to, nxt;
}e[M << 1];
int head[M], cnt;
inline void init() { memset(head, -1, sizeof head); cnt = 0; }
inline void add(int u, int v) {
    e[++cnt] = {.to = v, .nxt = head[u]};
    head[u] = cnt;
}

int n, h[M], vis[M], dp[M][2];

void dfs(int u) {
    dp[u][1] = h[u];
    for (int i = head[u]; ~i; i = e[i].nxt) {
        int v = e[i].to;
        dfs(v);
        dp[u][0] += max(dp[v][0], dp[v][1]);
        dp[u][1] += dp[v][0];
    }

}

int main() {
	int n = read();
    for (int i = 1; i <= n; ++i) h[i] = read();
    int u = read(), v = read();
    init();
    while (u | v) {
        add(v, u);
        vis[u] = true;
        u = read(), v = read();
    }
    int root = 0;
    for (int i = 1; i <= n; ++i) {
        if (!vis[i]) root = i;
    }
    dfs(root);
    printf("%d\n", max(dp[root][0], dp[root][1]));
    return 0;
}
```

#### 树的最小支配集

```cpp
/*
Loj 10157
每个点都有点权，一个点可以看守连着他的边上的点，选一些点出来，使他们能够看守整颗树上所有的点
问你最小选出来的权值
dp[0][i]表示i点被选上，则其 += min({dp[0][son], dp[2][son], dp[1][son]})
dp[1][i]表示i点没被选上，但是其父亲被选上了，则其 += min(dp[0][son], dp[2][son])
dp[2][i]表示i点没被选上，但是去其中某几个儿子被选上了，注意这个比较难转移，转移方式如下
先求出所有儿子min(dp[0][son], dp[1][son])的总和，然后在递归完后选出最小是那个儿子的dp[0][son]
即dp[2][u] = min(dp[2][u], sum - min(dp[2][v], dp[0][v]) + dp[0][v]);这行
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;
#define ll long long
#include <cctype>
inline long long IO() {} // 快读略

const int maxn = 1e5, maxm = 1e5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt, dis[maxn];

//初始化
void init() {
    memset(head, -1, sizeof head);
    // memset(vis, false, sizeof vis);
    cnt = 0;
}

struct edges {
    int to, next;
    int w;
    edges(int to = 0, int next = -1, int w = 0) : to(to), next(next), w(w) {}
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v, int w = 0) {
    if (cnt == 0) init();
    edge[++cnt] = edges(v, head[u], w);
    head[u] = cnt;
}
const int M = 1e4, inf = 0x3f3f3f3f;
int n, m, dp[3][M], vis[M], c[M];

void dfs(int u, int fa) {
    dp[0][u] = c[u], dp[2][u] = inf;
    int sum = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        dfs (v, u);
        dp[0][u] += min({dp[0][v], dp[2][v], dp[1][v]});
        if (fa != -1) dp[1][u] += min(dp[2][v], dp[0][v]);
        sum += min(dp[2][v], dp[0][v]);
    }
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        dp[2][u] = min(dp[2][u], sum - min(dp[2][v], dp[0][v]) + dp[0][v]);
    }
}


int main() {
    n = IO();
    init();
    for (int i = 0; i < n; ++i) {
        int u = IO(), w = IO(), k = IO();
        c[u] = w;
        while (k--) {
            int v = IO();
            add(u, v), add(v, u);
        }
    }
    dfs(1, -1);
    printf("%d", min(dp[0][1], dp[2][1]));
    return 0;
}
```

#### 树的最小点覆盖

```cpp
/*
Loj10156
每个点都能看到他所连着的边，问你选出最少的点使树上所有的边都能被看到
dp[0][i]表示不选i点的最小选择数 则其 += dp[1][son]
dp[1][i]表示选i点的最小选择数，则其 += min(dp[0][son], dp[1][son])
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;
#define ll long long
#include <cctype>
inline long long IO() {} // 快读略

const int maxn = 1e5, maxm = 1e5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt, dis[maxn];

//初始化
void init() {
    memset(head, -1, sizeof head);
    // memset(vis, false, sizeof vis);
    cnt = 0;
}

struct edges {
    int to, next;
    int w;
    edges(int to = 0, int next = -1, int w = 0) : to(to), next(next), w(w) {}
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v, int w = 0) {
    if (cnt == 0) init();
    edge[++cnt] = edges(v, head[u], w);
    head[u] = cnt;
}
const int M = 1e4;
int n, m, dp[2][M], vis[M];

void dfs(int u, int fa) {
    dp[1][u] = 1;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        dfs(v, u);
        dp[1][u] += min(dp[0][v], dp[1][v]);
        dp[0][u] += dp[1][v];
    }
}

int main() {
    n = IO();
    for (int i = 0; i < n; ++i) {
        int u = IO(), k = IO();
        while (k--) {
            int v = IO();
            add(u, v), add(v, u);
        }
    }
    dfs(0, -1);
    // printf("%d\n", ans);
    printf("%d", min(dp[0][0], dp[1][0]));
    return 0;
}
```

#### 树的直径

- 两次搜索法
  - 该方法只适用于边权为正整数情况
  - **第一次搜索**从任意一点出发找到最远点s
  - **第二次搜索**从s点出发找到最远点，并记录到达此点的距离，就是直径
- 一次DP法
  - 一次DP法又分两种，两者各有好处
  - 一种是下面例题中的方式，核心就是记录从一个点出发的**最远点**和**次远点**
  - 另一种是上述的优化

```cpp
dp[N]; // 记录到达结点的最远点
ans; //记录直径
void dfs(int u, int fa) {
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == u) continue;
        dfs(v);
        ans = max(ans, dp[u] + dp[v] + edge[i].w);
        dp[u] = max(dp[u], dp[v] + edge[i].w);
    }
}
```

- 例题

```cpp
/*
Loj 10159
树的直径：树上最长路径
本题需要求出所有直径（直径可能不唯一）上的所有点
解决方法：每次递归算出结点到其儿子中的最长路径和次长路径，然后相加
维护好全局变量ans，最终答案就是ans
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;
#define ll long long
#include <cctype>
inline long long IO() {} // 快读略

const int maxn = 2e5 + 5, maxm = 2e5 + 5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt, dis[maxn];

//初始化
void init() {
    memset(head, -1, sizeof head);
    // memset(vis, false, sizeof vis);
    cnt = 0;
}

struct edges {
    int to, next;
    int w;
    edges(int to = 0, int next = -1, int w = 0) : to(to), next(next), w(w) {}
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v, int w = 0) {
    if (cnt == 0) init();
    edge[++cnt] = edges(v, head[u], w);
    head[u] = cnt;
}
const int M = 2e5, inf = 0x3f3f3f3f;
int dp[M], t[M], ans = 0, d1[M], d2[M];
vector<int> res;

int dfs(int u, int fa) {
    d1[u] = 0, d2[u] = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        int len = dfs(v, u) + 1;
        if (len >= d1[u]) {
            d2[u] = d1[u], d1[u] = len;
        } else if (len > d2[u]) {
            d2[u] = len;
        }
    }
    ans = max(ans, d1[u] + d2[u]);
    return d1[u];
}

void dfs(int u, int fa, int d) {
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        if (d1[v] == d) dfs(v, u, d - 1), res.push_back(v);
    }
}

void solve(int u, int fa) {
    if (d1[u] + d2[u] == ans) {
        res.push_back(u);
        if (d1[u] != d2[u]) dfs(u, fa, d2[u] - 1);
        dfs(u, fa, d1[u] - 1);
    }
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        solve(v, u);
    }
}

int main() {
    int n = IO();
    init();
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        add(u, v), add(v, u);
    }
    dfs(0, -1);
    solve(0, -1);
    sort(res.begin(), res.end());
    auto x = unique(res.begin(), res.end());
    auto i = res.begin();
    while (i != x) {
        printf("%d\n", *i);
        i++;
    }
    return 0;
}
```

#### 树的重心

- 树的重心的一些重要性质：
- 一棵树最少有一个重心，最多有两个重心，若有两个重心，则他们相邻（即连有直接边）
- 树上所有点到某个点的距离和里，到重心的距离和最小；若有两个重心，则其距离和相同
- 若以重心为根，则所有子树的大小都不超过整棵树的一半
- 在一棵树上添加或删除一个叶子节点，其重心最多平移一条边的距离
- 两棵树通过连一条边组合成新树，则新树重心在原来两棵树的重心的连线上

```cpp
/*
Poj 1655
树的重心：重心是指树中的一个结点，如果将这个结点删除后剩余的各个连通块中结点数的最大值最小，则称为树的重心
本题需要求出重心，如果有多个输出最小编号的结点，并输出重心被删除后连通块结点数的最大值
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cctype>
inline long long IO() {} // 快读略
using namespace std;

const int maxn = 1e5 + 5, maxm = 2e5 + 5, inf = 0x3f3f3f3f;

int head[maxn], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = 0; }

struct edges {
    int to, next;
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v) {
    edge[++cnt].to = v, edge[cnt].next = head[u];
    head[u] = cnt;
}
int ans, siz;
int dfs(int u, int fa, const int &n) {
    int tot = 1, num = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        int tmp = dfs(v, u, n);
        tot += tmp, num = max(num, tmp);
    }
    int res = max(n - tot, num);
    if (siz >= res) {
        if (siz == res) ans = min(u, ans);
        else ans = u, siz = res;
    }
    return tot;
}
void solve() {
    int n = IO();
    init(), siz = inf;
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        add(u, v), add(v, u);
    }
    dfs(1, -1, n);
    printf("%d %d\n", ans, siz);
}

int main() {
    int t = IO();
    while (t--) solve();
    return 0;
}
```

#### 树的中心

```cpp
/*
例题：无
树的中心：找出一个点，使该点到其他点的最远距离最小，则这个点就是树的中心
解题思路：
从u点到其他点的最远距离分为两类
1.从u点向下走的最远距离，用d1[u]表示
2.从u点向上走的最远距离，用up[u]表示
则从u点到其他点的最远距离就是 max(d1[u], up[u]);
则中心到其他点的最远距离就是ans = min{dp[i]}
其中d1[u]可用求树的直径的方法求出
记得同时维护d2[u]即次长距离，和维护最长的路是哪个儿子
关于up的计算方式要用父亲节点来更新儿子节点，与求d1是相反的
如果u的儿子结点son在最长的路径上则
    up[son] = w[son] + max(up[u], d2[u])
否则
    up[son] = w[son] + max(up[u], d1[u])
输入
5
2 1 1
3 2 1
4 3 1
5 1 1
输出中心到其他节点的最长长度
2
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

const int maxn = 1e5, maxm = 2e5, inf = 0x3f3f3f3f;

int head[maxn], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = 0; }

struct edges {
    int to, next;
    int w;
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v, int w) {
    edge[++cnt].to = v, edge[cnt].next = head[u];
    edge[cnt].w = w, head[u] = cnt;
}

int d1[maxn], d2[maxn], maxv[maxn], up[maxn];
int dfs1(int u, int fa) {
    d1[u] = d2[u] = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        int d = dfs1(v, u) + edge[i].w;
        if (d >= d1[u]) {
            d2[u] = d1[u], d1[u] = d;
            maxv[u] = v;
        } else if (d > d2[u]) {
            d2[u] = d;
        }
    }
    return d1[u];
}
void dfs2(int u, int fa) {
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        if (maxv[u] == v) {
            up[v] = max(up[u], d2[u]) + edge[i].w;
        } else {
            up[v] = max(up[u], d1[u]) + edge[i].w;
        }
        dfs2(v, u);
    }
}

int main() {
    int n;
    init();
    scanf("%d\n", &n);
    for (int i = 1; i < n; ++i) {
        int u, v, w;
        scanf("%d %d %d\n", &u, &v, &w);
        add(u, v, w), add(v, u, w);
    }
    dfs1(1, -1);
    dfs2(1, -1);
    int res = 0x3f3f3f3f;
    for (int i = 1; i <= n; ++i) {
        res = min(res, max(up[i], d1[i]));
    }
    printf("%d", res);
    return 0;
}

```

#### 依赖背包问题

```cpp
/*
Loj 10154选课
学生不可能学完大学开设的所有课程，因此必须在入学时选定自己要学的课程。
每个学生可选课程的总数是给定的。请找出一种选课方案使得你能得到的学分最多，
并满足先修课优先的原则。假定课程间不存在时间上的冲突。

输入的第一行包括两个正整数 ，分别表示待选课程数和可选课程数。
接下来  行每行描述一门课，课号依次为 。每行两个数，依次表示这门课先修课课号（若不存在，则该项值为 ）和该门课的学分。
输出一行，表示实际所选课程学分之和。

dp[i][j]代表第i门课程选j个课的最大学分和
题中的课程号是从1开始的，并不是一颗树，而是森林，我们假设有一个课程0，连接所有森林的根结点
*/
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define pb push_back
using namespace std;
#define ll long long
#include <cctype>
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}

const int maxn = 1e4, maxm = 1e4;
const int INF = 0x3f3f3f3f;

const int M = 310;
int n, m, dp[M][M], tmp[M], w[M];
vector<int> mp[M];
int dfs(int u) {
    dp[u][1] = w[u];//初始化
    int num = 1;
    for (int v : mp[u]) {
        int siz = dfs(v);
        for (int i = 1;  i <= num; ++i) tmp[i] = dp[u][i];
        for (int i = 1;  i <= num; ++i) {
            for (int j = 0; j <= siz; ++j) {
                dp[u][i + j] = max(dp[u][i + j], tmp[i] + dp[v][j]);
            }
        }
        num += siz;
    }
    return num; //返回包括自己加上子树有多少个节点
}

int main() {
    n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) {
        int u = IO();
        mp[u].pb(i), w[i] = IO();
    }
    dfs(0);
    printf("%d", dp[0][m + 1]); // 假设0号结点必选
    return 0;
}
```

#### 基环树dp

#### 换根

### 最长上升子序列

#### 朴素动态规划

```cpp
dp[N], ans = 1;
for (int i = 1; i <= n; ++i) {
	dp[i] = 1;
	for (int j = 1; j < i; ++j) {
		if (a[j] < a[i]) { // 严格上升用 < , 否则用 <=
		dp[i] = max(dp[i], dp[j] + 1);
    	}
    }
	ans = max(ans, dp[i]);
}
```

#### 贪心

```cpp
// 坑（未补）
```

#### 树状数组（或线段树）优化动态规划（直接优化）

```cpp
inline long long IO() {}

using namespace std;
const int N = 1e5 + 5, M = 1e6 + 5, inf = 1e9;

int a[M];
unordered_map<int, int> mp;
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
#define lb(x) (x & (-x))
int bit[M], n;
int get(int i) {
    int res = 0;
    while (i) {
        res = max(res, bit[i]), i -= lb(i);
    }
    return res;
}
void update(int i, int v, int& len) {
    while(i <= len) {
        bit[i] = max(bit[i], v), i += lb(i);
    }
}
void problem() {
    int n = IO();
    vector<int> arr;
    for (int i = 0; i < n; ++i) a[i] = IO(), arr.push_back(a[i]);
    sort(arr.begin(), arr.end());
    arr.erase(unique(arr.begin(), arr.end()), arr.end());
    int cnt = 0, res = 0, len = arr.size();
    for (int x : arr) mp[x] = ++cnt;
    for (int i = 0; i < n; ++i) {
        int ans = get(mp[a[i]] - 1) + 1;
        res = max(ans, res);
        update(mp[a[i]], ans, len);
    }
    print(res);
}

int main() {
    int n = 1;
    while (n--) {
        problem();
    }
    return 0;
}
```

#### 树状数组（或线段树）优化（将值排序，求下标的最长上升子序列）

```cpp
#define ll long long
#define all(x) x.begin(), x.end()
inline long long IO() {}

using namespace std;
const int N = 1e5 + 5, M = 1e6 + 5, inf = 1e9;

int num[N], sum[410];
ll a[210];
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}

struct pii{
    int x, indx;
}p[N];

#define lb(x) (x & (-x))
int bit[N], n;

int get(int i) {
    int res = 0;
    while (i) {
        res = max(res, bit[i]), i -= lb(i);
    }
    return res;
}

void update(int i, int v) {
    while(i <= n) {
        bit[i] = max(bit[i], v), i += lb(i);
    }
}

void problem() {
    n = IO();
    for (int i = 0; i < n; ++i) p[i].x = IO(), p[i].indx = i + 1;
    sort(p, p + n, [] (pii &i, pii &j) {
        if (i.x == j.x) return i.indx > j.indx;
        return i.x < j.x;
    });
    int res = 0;
    for (int i = 0; i < n; ++i) {
        int ans = get(p[i].indx - 1) + 1;
        res = max(ans, res);
        update(p[i].indx, ans);
    }
    print(res);
}

int main() {
    int n = 1;
    while (n--) {
        problem();
    }
    return 0;
}
```


