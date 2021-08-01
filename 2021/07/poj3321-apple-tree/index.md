# POJ3321 Apple Tree


- 题意：在一棵根为1的树上，每个节点初始化为1，每次指定某个结点上的数字如果一开始是1则变为0，反之变为1，并且每次询问以某个结点为根的子树上一共有多少个结点上的数字为1
- 这题用树链剖分就是一个板子题，但也可以用dfs序+线段树来写
- 具体做法就是用一个结构体记录每个节点dfs时时间戳的开始和结束，显然这个时间戳的区间便是以该结点为根的子树区间，然后就是线段树的区间查询和单点修改了

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cctype>
#include <cmath>
#include <string>
#define ll long long
#define rep(u, i, e) for (int i = head[u]; ~i; i = e[i].next)
#define pb push_back
#define debug(x) printf(#x" = %lld\n", (ll)x)
#define bug puts("bug")
#define umap unordered_map
#define uset unordered_set
#define ls (node << 1)
#define rs ((node << 1) | 1)
#define vi vector
#define all(x) x.begin(), x.end()

using namespace std;


/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-快读与快输-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
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
void getstr(string &s) {
    int c = getchar();
    while (c < 33 && c != -1) c = getchar();
    if (c == -1) exit(0);
    s.clear();
    for(;c > 32; c = getchar()) s.pb(c);
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
// void print(const string& s) { for (char c : s) putchar(c); }
template <typename T>
void print(T x, char c) { print(x); putchar(c); }
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-快读与快输-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-不可修改区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
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
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-不可修改区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-可修改区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
/*↓可修改区*/
// 自定义结构体
struct pii {
    int x, y;
    // int node, l, r;
    bool operator< (const pii& j) {
        return x != j.x ? x < j.x : y < j.y;
    }
};
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-可修改区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-全局变量区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
const int mod = 1e9 + 7;
const int inf = (1LL << 31) - 1;
const ll INF = 1e18;
// const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
int tim, seg[N << 2];
pii p[N];
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-全局变量区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-模板代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
int head[N], cnt;

//初始化
void init(int n) { fill_n(head, n + 5, -1); cnt = -1; tim = 0; }

struct edges {
    int to, next;
    void add(int t, int n) {
        to = t, next = n;
    }
}edge[N << 1]; //无向图则需要乘2

inline void add(int u, int v) {
    edge[++cnt].add(v, head[u]);
    head[u] = cnt;
}

int op(int a, int b) { return a + b; }

void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = 1;
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 单点修改
void update(int indx, int l, int r, int node = 1) {
    if (l == r) { // 题意不同，这里更新操作不同
        seg[node] ^= 1; 
        return;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) update(indx, l, mid, ls);
    else update(indx, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 区间查找
int get(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    // push_down(l, r, node); // 保证单点的情况下这句话可以注释掉
    int mid = (l + r) >> 1;
    int ret = 0; // 题意不同，初始化不同
    if (ql <= mid) ret = get(ql, qr, l, mid, ls);
    if (qr > mid) ret = op(get(ql, qr, mid + 1, r, rs), ret);
    return ret;
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-模板代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-函数代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void dfs(int u, int fa) {
    p[u].x = ++tim;
    rep(u, i, edge) {
        int v = edge[i].to;
        if (v == fa) continue;
        dfs(v, u);
    }
    p[u].y = tim;
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-函数代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-主代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void problem() {
    int n = IO();
    init(n);
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        add(u, v), add(v, u);
    }
    dfs(1, -1);
    build(1, n);
    int m = IO();
    string q;
    while (m--) {
        getstr(q);
        int u = IO();
        if (q[0] == 'Q') print(get(p[u].x, p[u].y, 1, n), '\n');
        else update(p[u].x, 1, n);
    }
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-主代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






int main() {
    // int t = 1;
    // t = IO();
    // while (t--) problem();
    while (1) problem();
    return 0;
}

```


