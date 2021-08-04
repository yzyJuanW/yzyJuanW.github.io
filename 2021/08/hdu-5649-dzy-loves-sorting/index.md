# HDU 5649 DZY Loves Sorting


- 给你一个排列的数组，操作m次，要不就是将 `[l, r]` 区间升序，要不就是降序，最后问某个下标的值是多少
- 一道思维题，可以这么做，二分一个答案 `x`，多开一个数组 `b` ，将原数组比 `x` 小的位置在数组 `b`  中设置为0，大于等于 `x` 的位置在数组 `b`  中设置为1，然后针对题意中的操作，对数组 `b`  进行区间查询和区间修改操作，即如果升序，则将该区间前面修改为0，后面修改为1，降序同理，最后查看题中问的下标是1还是0
- 感觉此思路还是挺明朗的，剩下的就是套个常规的线段树板子改改就好了

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
#define ls (node << 1)
#define rs ((node << 1) | 1)
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
/*↓可修改区*/
// 自定义结构体
struct pii {
    int q, l, r;
    // bool operator< (const pii& j) {
    //     return x != j.x ? x < j.x : y < j.y;
    // }
};
/*------------------------------可修改区------------------------------*/






/*++++++++++++++++++++++++++++++全局变量区++++++++++++++++++++++++++++++*/
const int mod = 1e9 + 7;
const int inf = (1LL << 31) - 1;
const ll INF = 1e18;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
int a[N], b[N], seg[N << 2], lazy[N << 2], vis[N << 2];
pii ask[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
int op(int a, int b) { return a + b; }
// 题意不同，函数内部不同
void push_down(int l, int r, int node) {
    if (!vis[node]) return; // 这里如果0也有意义的话多开一个数组标记
    int mid = (l + r) >> 1;
    lazy[ls] = lazy[node], lazy[rs] = lazy[node];
    seg[ls] = (mid - l + 1) * lazy[node];
    seg[rs] = (r - mid) * lazy[node];
    vis[ls] = vis[rs] = 1, vis[node] = 0;
}
// 初始化
void build(int l, int r, int node) {
    if (l == r) {
        seg[node] = b[l], vis[node] = 0;
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]), vis[node] = 0;
}
// 区间修改
void update(int ql, int qr, int v, int l, int r, int node) {
    if (ql <= l && r <= qr) { // 题意不同，这里更新操作不同
        lazy[node] = v, vis[node] = 1;
        seg[node] = (r - l + 1) * v;
        return;
    }
    push_down(l, r, node);
    int mid = (l + r) >> 1;
    if (ql <= mid) update(ql, qr, v, l, mid, ls);
    if (qr > mid) update(ql, qr, v, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}
// 区间查找
int get(int ql, int qr, int l, int r, int node) {
    if (ql <= l && r <= qr) return seg[node];
    push_down(l, r, node); // 保证单点的情况下这句话可以注释掉
    int mid = (l + r) >> 1;
    int ret = 0; // 题意不同，初始化不同
    if (ql <= mid) ret = get(ql, qr, l, mid, ls);
    if (qr > mid) ret = op(get(ql, qr, mid + 1, r, rs), ret);
    return ret;
}
/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
void check(int n, int m, int x) {
    for (int i = 1; i <= n; ++i) b[i] = (a[i] >= x);
    build(1, n, 1);
    for (int i = 0; i < m; ++i) {
        int q = ask[i].q, l = ask[i].l, r = ask[i].r, len = r - l + 1;
        int tot = get(l, r, 1, n, 1);
        if (tot == 0 || tot == len) continue;
        if (!q) {
            update(l, r - tot, 0, 1, n, 1); // [l, r - tot] = 0
            update(r - tot + 1, r, 1, 1, n, 1); // [r - tot + 1, r] = 1
        } else {
            update(l, l + tot - 1, 1, 1, n, 1); // [l, l + tot - 1] = 1
            update(l + tot, r, 0, 1, n, 1); // [l + tot, r] = 0
        }
    }
}
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    fill_w(a + 1, n);
    for (int i = 0; i < m; ++i) {
        ask[i].q = IO(), ask[i].l = IO(), ask[i].r = IO();
    }
    int k = IO(), l = 1, r = n, ans = -1;
    while (l <= r) {
        int mid = (l + r) >> 1;
        check(n, m, mid);
        if (get(k, k, 1, n, 1)) ans = mid, l = mid + 1;
        else r = mid - 1;
    }
    print(ans, '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
    int t = 1;
    t = IO();
    while (t--) problem();
    // while (1) problem();
    return 0;
}

```


