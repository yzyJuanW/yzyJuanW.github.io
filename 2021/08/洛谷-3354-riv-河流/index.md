# 洛谷 3354 Riv 河流


- 题意：一颗树上有很多点，0号点为根结点，边有边权即距离，点有点权即该点产量，点产生的运到一个建有伐木场的点的费用是其点到点的距离乘上产量（0号结点一开始就有伐木场，其余结点没有），问你给m个结点建造伐木场，最后最小运送费用为多少？

- 一开始状态定义错了，定义 `dp[u][k][1/0]` 表示以u为根的子树，建造k个伐木场，该点建/不建伐木场的最小费用，交上去只有52分，想了一下，虽然这样定义转移是能转，但是有后效性
- 后来看了一下别人的思路，发现自己状态可以加一维来消除后效性
- 定义 `dp[u][k][fa][1/0]` 表示以u为根的子树，建造k个伐木场，fa为u的某个祖先，且该祖先为距离u点最近的建造了伐木场的点，而该点建/不建伐木场的产生的最小费用（**不包括u点运到fa的费用**）
- 转移

```cpp
k = i + j
dp[u][k][fa][0] = min{
    dp[u][k][fa][0],
    dp[u][i][fa][0] + dp[v][j][fa][0] + (dep[v] - dep[fa]) * w[v],
    dp[u][i][fa][0] + dp[v][j][fa][1]
}
dp[u][k][fa][1] = min{
    dp[u][k][fa][1],
    dp[u][i][fa][1] + dp[v][j][u][0] + (dep[v] - dep[u]) * w[v],
    dp[u][i][fa][1] + dp[v][j][u][1]
}
```

- 初始化dp看代码
- 至于fa可以利用一个vector来记录，最后回溯去掉便可
- 具体看代码函数区

```cpp
#include <bits/stdc++.h>
#define ll long long
#define rep(u, i, e) for (int i = head[u]; ~i; i = e[i].next)
#define pb push_back
#define dprint(x) printf(#x" = %lld\n", (ll)x)
#define dcout(x) cout << #x << " = " << x << endl
#define bug puts("bug")
#define umap unordered_map
#define uset unordered_set
#define vi vector
#define all(x) x.begin(), x.end()

using namespace std;


/*++++++++++++++++++++++++++++++快读与快输++++++++++++++++++++++++++++++*/
inline ll IO() {
    long long x = 0;
    int f = 0, c = getchar();
    for (; !isdigit(c) && c != -1; c = getchar()) {
        if (c == '-') f = 1;
    }
    if (c == -1) exit(0);
    for (; isdigit(c); c = getchar()) {
        x = (x << 1) + (x << 3) + (c ^ '0');
    }
    return f ? -x : x;
}
int getstr(string &s) {
    int c = getchar();
    while (c < 33 && c != -1) c = getchar();
    if (c == -1) exit(0);
    s.clear();
    for(;c > 32; c = getchar()) s.pb(c);
    return s.size();
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
void print(const string& s) { for (char c : s) putchar(c); }
template <typename T>
void print(T x, char c) { print(x); putchar(c); }
/*-----------------------------快读与快输-----------------------------*/






/*++++++++++++++++++++++++++++++不可修改区++++++++++++++++++++++++++++++*/
// gcd
template <typename T>
T gcd(T a, T b, T m = 0) { while (b) m = a % b, a = b, b = m; return a; }
// 快速幂
ll powf(ll a, ll b, ll p, ll res = 1) {
    for (a %= p; b; b >>= 1, a = a * a % p) if (b & 1) res = res * a % p;
    return res;
}
// 数组读入
template <typename T>
inline void fill_w(T *a, int cnt) {  for (int i = 0; i < cnt; ++i) a[i] = IO();}
/*------------------------------不可修改区------------------------------*/






/*++++++++++++++++++++++++++++++可修改区++++++++++++++++++++++++++++++*/
// 自定义结构体
struct pii {
    int x, y;
    bool operator< (const pii& j) {
        return x != j.x ? x < j.x : y < j.y;
    }
};
/*------------------------------可修改区------------------------------*/






/*++++++++++++++++++++++++++++++全局变量区++++++++++++++++++++++++++++++*/
const int mod = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll INF = 1e18;
const double eps = 1e-5, PI = acos(-1.0);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 2e2 + 10, M = 1e6 + 10;
int num[M], dep[N], dp[N][N][N][2], tmp[N][N][2];
vi<int> f;
int n, m;
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
int head[N], cnt;

//初始化
void init(int n) { fill_n(head, n + 5, -1); cnt = -1; }

struct edges {
    int to, next;
    int w;
    void add(int t, int n, int w) {
        to = t, next = n, this->w = w;
    }
}edge[N << 1]; //无向图则需要乘2

inline void add(int u, int v, int w) {
    edge[++cnt].add(v, head[u], w);
    head[u] = cnt;
}


/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
int dfs(int u) {
    f.pb(u);
    int sum = 1;
    for (auto fa : f) { // 初始化
        dp[u][0][fa][0] = 0;
        dp[u][1][fa][1] = 0;
    }
    rep(u, i, edge) {
        int v = edge[i].to;
        dep[v] = dep[u] + edge[i].w;
        int son = dfs(v);
        for (auto fa : f) {
            // if (fa == u) continue;
            for (int j = 0; j <= sum + son; ++j) {
                tmp[j][fa][0] = tmp[j][fa][1] = inf;
            }
            for (int j = 0; j <= sum; ++j) {
                for (int k = 0; k <= son; ++k) {
                    if (j + k > m) break;
                    tmp[j + k][fa][0] = min({
                        tmp[j + k][fa][0],
                        dp[u][j][fa][0] + dp[v][k][fa][0] + (dep[v] - dep[fa]) * num[v],
                        dp[u][j][fa][0] + dp[v][k][fa][1]
                    });
                    if (j > 0){
                        tmp[j + k][fa][1] = min({
                            tmp[j + k][fa][1],
                            dp[u][j][fa][1] + dp[v][k][u][0] + (dep[v] - dep[u]) * num[v],
                            dp[u][j][fa][1] + dp[v][k][u][1]
                        });
                    }
                }
            }
            for (int j = 0; j <= sum + son; ++j) {
                dp[u][j][fa][0] = tmp[j][fa][0];
                dp[u][j][fa][1] = tmp[j][fa][1];
            }
        }
        sum += son;
    }
    f.pop_back();
    return sum;
}
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    n = IO(), m = IO();
    init(n);
    for (int i = 1; i <= n; ++i) {
        num[i] = IO();
        int fa = IO(), w = IO();
        add(fa, i, w);
    }
    memset(dp, 0x3f, sizeof dp);
    dfs(0);
    print(dp[0][m][0][0]);
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```

- 当然本题还有一种多叉转二叉的方式
- 写了之后发现多叉转二叉在树形背包中的一些好处：
  - 可以避免一些无用的状态转移
  - 利用记忆化搜索强行变成了一种线性的dp的感觉
  - 记忆化时简单好懂
- 为了方便状态定义改了改

- 定义 `dp[u][k][fa][1/0]` 表示以u为根的子树，建造k个伐木场，fa为u的某个祖先，且该祖先为距离u点最近的建造了伐木场的点，而该点建/不建伐木场的产生的最小费用（**包括u点运到fa的费用**）
- 简单的来说就是把括号里的不包括改成了包括
- 实现起来还是不难的

```cpp
#include <bits/stdc++.h>
#define ll long long
#define rep(u, i, e) for (int i = head[u]; ~i; i = e[i].next)
#define pb push_back
#define dprint(x) printf(#x" = %lld\n", (ll)x)
#define dcout(x) cout << #x << " = " << x << endl
#define bug puts("bug")
#define umap unordered_map
#define uset unordered_set
#define vi vector
#define all(x) x.begin(), x.end()

using namespace std;


/*++++++++++++++++++++++++++++++快读与快输++++++++++++++++++++++++++++++*/
inline ll IO() {
    long long x = 0;
    int f = 0, c = getchar();
    for (; !isdigit(c) && c != -1; c = getchar()) {
        if (c == '-') f = 1;
    }
    if (c == -1) exit(0);
    for (; isdigit(c); c = getchar()) {
        x = (x << 1) + (x << 3) + (c ^ '0');
    }
    return f ? -x : x;
}
int getstr(string &s) {
    int c = getchar();
    while (c < 33 && c != -1) c = getchar();
    if (c == -1) exit(0);
    s.clear();
    for(;c > 32; c = getchar()) s.pb(c);
    return s.size();
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
void print(const string& s) { for (char c : s) putchar(c); }
template <typename T>
void print(T x, char c) { print(x); putchar(c); }
/*-----------------------------快读与快输-----------------------------*/






/*++++++++++++++++++++++++++++++不可修改区++++++++++++++++++++++++++++++*/
// gcd
template <typename T>
T gcd(T a, T b, T m = 0) { while (b) m = a % b, a = b, b = m; return a; }
// 快速幂
ll powf(ll a, ll b, ll p, ll res = 1) {
    for (a %= p; b; b >>= 1, a = a * a % p) if (b & 1) res = res * a % p;
    return res;
}
// 数组读入
template <typename T>
inline void fill_w(T *a, int cnt) {  for (int i = 0; i < cnt; ++i) a[i] = IO();}
/*------------------------------不可修改区------------------------------*/






/*++++++++++++++++++++++++++++++可修改区++++++++++++++++++++++++++++++*/
// 自定义结构体
struct pii {
    int l = -1, r = -1, d, w;
    // int x, y;
    // bool operator< (const pii& j) {
    //     return x != j.x ? x < j.x : y < j.y;
    // }
};
/*------------------------------可修改区------------------------------*/






/*++++++++++++++++++++++++++++++全局变量区++++++++++++++++++++++++++++++*/
const int mod = 1e9 + 7;
const int inf = 0x3f3f3f3f;
const ll llf = 1e18;
const double eps = 1e-5, PI = acos(-1.0);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 2e2 + 10, M = 1e6 + 10;
int dep[N], dp[N][N][N];
pii node[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
void init_deep(int u) {
    int lson = node[u].l;
    while (lson != -1) {
        dep[lson] = dep[u] + node[lson].d;
        init_deep(lson);
        lson = node[lson].r;
    }
}

int dfs(int u, int k, int fa) {
    int& ret = dp[u][k][fa];
    if (ret != -1) return ret;
    int ls = node[u].l, rs = node[u].r;
    ret = inf;
    for (int i = 0; i <= k; ++i) {
        int tmp = 0;
        if (ls != -1) tmp = dfs(ls, i, fa);
        if (rs != -1) tmp += dfs(rs, k - i, fa);
        ret = min(tmp + (dep[u] - dep[fa]) * node[u].w, ret);
        if (i < k) {
            tmp = 0;
            if (ls != -1) tmp = dfs(ls, i, u);
            if (rs != -1) tmp += dfs(rs, k - i - 1, fa);
            ret = min(tmp, ret);
        }
    }
    return ret;
}
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) {
        node[i].w = IO();
        int fa = IO();
        node[i].d = IO();
        node[i].r = node[fa].l;
        node[fa].l = i;
    }
    init_deep(0);
    memset(dp, -1, sizeof dp);
    print(dfs(0, m, 0));
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```


