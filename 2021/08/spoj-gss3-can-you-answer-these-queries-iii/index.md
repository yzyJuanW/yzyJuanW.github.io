# SPOJ GSS3 Can you answer these queries III


- 题意：给你一个数组，每次操作将x下标位的数改成y，每次询问区间 `[x, y]` 之间的最大子段和
- 一个另类的线段树维护区间信息的题（听说如果只有查询，可以直接用猫树来做）
- 线段树结点维护4个值：
  -  `maxv` ：记录当前结点区间的最大字段和
  -  `sum`：记录当前结点区间的和
  -  `l` ：记录当前结点区间中，从左端点开始（即左端点必选）的最大字段和
  -  `r`：记录当前结点区间中，从右端点开始的最大字段和
- 至于更新值的话就是
  -  `maxv` ：要不就是左孩子的 `maxv`  ，要不就是右孩子的 `maxv`， 又或者是左孩子的`r` 值加上 右孩子的 `l`值
  -  `sum`：直接区间求和更新
  -  `l` ：显然要不就是左孩子的 `l` 值或者是左孩子的 `sum` 值 加上右孩子的 `l` 值
  -  `r`：要不就是右孩子的 `r` 值或者是右孩子的 `sum` 值 加上左孩子的 `r` 值
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
#define ls (node << 1)
#define rs ((node << 1) | 1)
#define vi vector
#define all(x) x.begin(), x.end()

using namespace std;


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
void print(const string& s) { for (char c : s) putchar(c); }
template <typename T>
void print(T x, char c) { print(x); putchar(c); }
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
struct pii {
    // int x, y;
    int l, r, maxv, sum;
    pii() : l(-1e7), r(-1e7), maxv(-1e7), sum(-1e7) {}
    pii(int x) : l(x), r(x), maxv(x), sum(x) {}
};
const int mod = 1e9 + 7;
const int inf = (1LL << 31) - 1;
const ll INF = 1e18;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
int a[N];
pii seg[N << 2];
void push_up(int node) {
    seg[node].maxv = max({seg[ls].maxv, seg[rs].maxv,seg[ls].r + seg[rs].l});
    seg[node].sum = seg[ls].sum + seg[rs].sum;
    seg[node].l = max(seg[ls].l, seg[ls].sum + seg[rs].l);
    seg[node].r = max(seg[rs].r, seg[rs].sum + seg[ls].r);
}

void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = pii(a[l]);
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    push_up(node);
}


void modify(int indx, int val, int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = pii(val);
        return;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) modify(indx, val, l, mid, ls);
    else modify(indx, val, mid + 1, r, rs);
    push_up(node);
}

pii get(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    int mid = (l + r) >> 1;
    if (ql > mid) return get(ql, qr, mid + 1, r, rs);
    if (qr <= mid) return get(ql, qr, l, mid, ls);
    pii p1 = get(ql, qr, l, mid, ls);
    pii p2 = get(ql, qr, mid + 1, r, rs);
    pii res;
    res.maxv = max({p1.maxv, p2.maxv, p1.r + p2.l});
    res.sum = p1.sum + p2.sum;
    res.l = max(p1.l, p1.sum + p2.l);
    res.r = max(p2.r, p2.sum + p1.r);
    return res;
}
void problem() {
    int n = IO();\
    fill_w(a + 1, n);
    build(1, n);
    int m = IO();
    while (m--) {
        int q = IO(), x = IO(), y = IO();
        if (q == 1) print(get(x, y, 1, n).maxv, '\n');
        else modify(x, y, 1, n);
    }
}

int main() {
    // int t = 1;
    // t = IO();
    // while (t--) problem();
    while (1) problem();
    return 0;
}

```


