# Cf786B Legacy


- 题意：建图，1表示u、v之间连一条有向边权值为w，2表示u向[l, r]区间内所有点连一条权值为w的有向边，3表示[l, r]区间内的所有点向u连一条权值为w的有向边

- 直接线段树优化建图便可，具体做法就是建一个根结点连向子结点的**出树**，再建一个子结点连向根结点的**入树**，相连的是每个节点的内存池编号。
- 每个结点表示一个区间，若是点与区间相连，直接树上查询区间，使点向相应区间的编号建边，反之编号向点建边
- 最后直接在这两个树构成的图上跑最短路便可
- 值得扩展的是，如果是区间与区间相连权值为w，直接新编一个点，先使前区间与该点相连，权值为w，再使该点与后区间相连权值为0
- 本题只有点与点连，点与区间连
- 直接看代码

```cpp
#include <bits/stdc++.h>
#define ll long long
#define rep(u, i, e) for (int i = head[u]; ~i; i = e[i].next)
#define pb push_back
#define debug(x) printf(#x" = %lld\n", (ll)x)
#define bug puts("bug")
#define umap unordered_map
#define uset unordered_set
#define vi vector
#define all(x) x.begin(), x.end()
#define ls (node << 1)
#define rs ((node << 1) | 1)
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
    s.clear();
    int c = getchar();
    while (c < 33 && c != -1) c = getchar();
    if (c == -1) exit(0);
    for(;c > 32; c = getchar()) s.pb(c);
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
void print(const string& s) { for (char c : s) putchar(c); }
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
    bool operator< (const pii& j) {
        return x != j.x ? x < j.x : y < j.y;
    }
};
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-可修改区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-全局变量区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
const int mod = 1e9 + 7;
const int inf = (1LL << 31) - 1;
const ll INF = 1e17;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 2e5 + 1e3, M = 1e6 + 10;
bool vis[N << 4];
ll dis[N << 4];
int head[N << 4], cnt;
int ram; // 内存池编号
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-全局变量区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-模板代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/

//初始化
void init(int n) { fill_n(head, n + 5, -1); cnt = -1; }

struct edges {
    int to, next;
    ll w;
    void add(int t, int n, ll w) {
        to = t, next = n, this->w = w;
    }
}edge[N << 4]; //无向图则需要乘2

inline void add(int u, int v, ll w) {
    edge[++cnt].add(v, head[u], w);
    head[u] = cnt;
}

struct qnode{
    int v;
    ll w;
    qnode(int v = 0, ll w = 0) : v(v), w(w) {}
    bool operator< (const qnode &t) const { return w > t.w; }
};

void dij(int n, int s) {//n 为顶点数， m 为边数
    for (int i = 0; i <= n; ++i) dis[i] = INF, vis[i] = 0;
    dis[s] = 0;
    priority_queue<qnode> heap;
    heap.push(qnode(s, dis[s]));
    while (heap.size()) {
        int u = heap.top().v;
        heap.pop();
        if (vis[u]) continue;
        vis[u] = true;
        for (int i = head[u]; ~i; i = edge[i].next) {
            int v = edge[i].to;
            ll w = edge[i].w;
            if (!vis[v] && dis[u] + w < dis[v]) { //松弛
                dis[v] = dis[u] + w;
                heap.push(qnode(v, dis[v]));
            }
        }
    }
}

/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-模板代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-函数代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
struct nodes{
    int l, r;
}seg[N << 4];
int build(int l, int r, int flag) { // flag 0 in, 1 out
    if (l == r) return l;
    int mid = (l + r) >> 1, now = ++ram;
    seg[now].l = build(l, mid, flag);
    seg[now].r = build(mid + 1, r, flag);
    if (flag) add(now, seg[now].l, 0), add(now, seg[now].r, 0); //out
    else add(seg[now].l, now, 0), add(seg[now].r, now, 0); // in
    return now;
}
// flag 0 u -> [ql, qr], 1 [ql, qr] -> u
void modify(int u, ll w, int ql, int qr, int l, int r, int node, int flag) {
    if (ql <= l && r <= qr) {
        // printf("%d %d %d %lld %d %d\n", u, l, r, w, flag, node);
        flag ? add(node, u, w) : add(u, node, w);
        return;
    }
    int mid = (l + r) >> 1;
    if (ql <= mid) modify(u, w, ql, qr, l, mid, seg[node].l, flag);
    if (qr > mid) modify(u, w, ql, qr, mid + 1, r, seg[node].r, flag);
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-函数代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-主代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void problem() {
    int n = IO(), m = IO(), s = IO();
    ram = n;
    init((n << 2) + 100);
    int rootin = build(1, n, 0), rootout = build(1, n, 1);
    while (m--) {
        int q = IO(), u = IO(), l, r, v;
        if (q == 1) {
            v = IO();
            add(u, v, IO());
        } else if (q == 2) {
            l = IO(), r = IO();
            modify(u, IO(), l, r, 1, n, rootout, 0);
        } else {
            l = IO(), r = IO();
            modify(u, IO(), l, r, 1, n, rootin, 1);
        }
    }
    dij(ram, s);
    for (int i = 1; i <= n; ++i) print(dis[i] < INF ? dis[i] : -1, ' ');
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
