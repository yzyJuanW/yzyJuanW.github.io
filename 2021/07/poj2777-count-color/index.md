# POJ2777 Count Color


- 给你一个长度为L的纸，初始化纸的颜色为1，每次回选取一端区间将其用x颜色覆盖，每次询问一端区间中有多少不同的颜色
- 观察到颜色的数量不多，可以用每个数的二进制位来记录当前有哪些颜色，更新结点直接用或运算便可，然后就是非常普通的线段树的区间修改与查询，直接套板子改改交上去1A了

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
int tim, seg[N << 2], lazy[N << 2];
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-全局变量区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-模板代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
int op(int a, int b) { return a | b; }

void push_down(int l, int r, int node) {
    if (!lazy[node]) return; // 这里如果0也有意义的话多开一个数组标记
    lazy[ls] = lazy[node], lazy[rs] = lazy[node];
    seg[ls] = lazy[node];
    seg[rs] = lazy[node];
    lazy[node] = 0;
}

void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = 2;
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 区间修改
void update(int ql, int qr, int v, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) { // 题意不同，这里更新操作不同
        lazy[node] = 1 << v;
        seg[node] = 1 << v;
        return;
    }
    push_down(l, r, node);
    int mid = (l + r) >> 1;
    if (ql <= mid) update(ql, qr, v, l, mid, ls);
    if (qr > mid) update(ql, qr, v, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 区间查找
int get(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    push_down(l, r, node); // 保证单点的情况下这句话可以注释掉
    int mid = (l + r) >> 1;
    int ret = 0; // 题意不同，初始化不同
    if (ql <= mid) ret = get(ql, qr, l, mid, ls);
    if (qr > mid) ret = op(get(ql, qr, mid + 1, r, rs), ret);
    return ret;
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-模板代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-函数代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
int cal(int x) {
    int ans = 0;
    while (x) {
        ans += 1;
        x &= (x - 1);
    }
    return ans;
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-函数代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-主代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void problem() {
    int n = IO(), t = IO(), m = IO();
    build(1, n);
    string q;
    while (m--) {
        getstr(q);
        int l = IO(), r = IO();
        if (l > r) swap(l, r);
        if (q[0] == 'C') update(l, r, IO(), 1, n);
        else print(cal(get(l, r, 1, n)), '\n');
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


