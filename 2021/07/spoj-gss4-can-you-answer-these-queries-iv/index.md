# SPOJ GSS4 Can you answer these queries IV


- 给你一个区间的数，每次询问时询问第x和第y（包括）个数之间的数的和，每次操作是将第x和第y（包括）个数之间的数都开方
- 直接暴力修改就好了，每个数最多不会被开方超过6次，直接暴力当成单点修改便可，当然如果这个数已经是1了，就没必要开方了，固如果一个区间上的和恰好为其区间的长度，则这个区间的数是不用开方的了
- 直接线段树暴力

```cpp
#include <bits/stdc++.h>
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
ll arr[N], seg[N << 2];
int kase, flag;
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-全局变量区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-模板代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
ll op(ll a, ll b) { return a + b; }
void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = arr[l];
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 区间修改
void update(int ql, int qr, int l, int r, int node = 1) {
    if (seg[node] == r - l + 1) return;
    if (l == r) {
        seg[node] = (ll)sqrt(seg[node]);
        return;
    }
    int mid = (l + r) >> 1;
    if (ql <= mid) update(ql, qr, l, mid, ls);
    if (qr > mid) update(ql, qr, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}

// 区间查找
ll get(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    // push_down(l, r, node); // 保证单点的情况下这句话可以注释掉
    int mid = (l + r) >> 1;
    ll ret = 0; // 题意不同，初始化不同
    if (ql <= mid) ret = get(ql, qr, l, mid, ls);
    if (qr > mid) ret = op(get(ql, qr, mid + 1, r, rs), ret);
    return ret;
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-模板代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-函数代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/

/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-函数代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-主代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void problem() {
    int n = IO();
    fill_w(arr + 1, n);
    if (flag) puts("");
    else flag = 1;
    printf("Case #%d:\n", ++kase);
    build(1, n);
    int m = IO();
    while (m--) {
        int q = IO(), l = IO(), r = IO();
        if (l > r) swap(l, r);
        if (q == 0) update(l, r, 1, n);
        else print(get(l, r, 1, n), '\n');
    }
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-主代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






int main() {
    while (1) problem();
    return 0;
}

```


