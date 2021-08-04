# AcWing 246 区间最大公约数


- 题意：给你一个数组，有区间修改和区间查询gcd
- 显然，这题是没法直接区间修改的
- 需要用到一个在数论中常见的gcd结论：$gcd(a_1, a_2, a_3, \dots a_n) = gcd(a_1, a_2 - a_1, a_3 - a_2\dots a_n - a_{n- 1})$
- 观察上面的式子后发现：本来的a序列变成了他的差分序列，由差分序列的性质可知，若原序列区间加减，只需要在差分序列中区间两端加减便可。例如，我要在原序列中 `[l, r]` 区间中每一个数都加一个 `d` ，则只需要在差分序列中下标为 `l` 的数值加 `d` ，下标为 `r + 1` 的数值减 `d` 即可
- 固，我们只需要将原序列的差分序列建立一个线段树即可
- 这要做还不能完全解决这道题，利用差分序列使我们更好地操作，但不要忘了，我们还有查询
- 这里的查询是不能直接利用线段树的区间查询的，观察上面的式子，差分序列第一个数并没有变，所以我们要保留每一个数的上一个数是多少（注意题中区间修改操作会改变这个值），在区间 `[l, r]` 查询前，我们只需要将 `l` 下标的数组减去 `l - 1` 的数值再做区间查询即可
- 具体看代码（这里线段树保存了两个值，一个是区间的gcd，一个是原序列的值val，考虑到不用求和，因为求和可能会爆longlong，固用非叶子结点的val来当做lazy值。所以本线段树中树的建立、区间查询、单点修改都是针对gcd的，而区间修改、单点查询都是针对原序列val值的）

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
#define lb(x) ((x) & (-x))
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
const int inf = (1LL << 31) - 1;
const ll INF = 1e18;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 5e5 + 10, M = 1e6 + 10;
ll a[N], b[N], lazy[N << 2], val[N << 2], seg[N << 2];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = b[l], val[node] = a[l];
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = gcd(abs(seg[ls]), abs(seg[rs]));
}

void modify_point(int indx, ll v, int l, int r, int node = 1) {
    if (l == r) {
        seg[node] += v;
        return;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) modify_point(indx, v, l, mid, ls);
    else modify_point(indx, v, mid + 1, r, rs);
    seg[node] = gcd(abs(seg[ls]), abs(seg[rs]));
}

void push_down(int node) {
    if (val[node] == 0) return;
    val[ls] += val[node], val[rs] += val[node];
    val[node] = 0;
}

void modify_range(int ql, int qr, ll v, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) {
        val[node] += v;
        return;
    }
    push_down(node);
    int mid = (l + r) >> 1;
    if (ql <= mid) modify_range(ql, qr, v, l, mid, ls);
    if (qr > mid) modify_range(ql, qr, v, mid + 1, r, rs);
}

ll get_point(int indx, int l, int r, int node = 1) {
    if (l == r) return val[node];
    push_down(node);
    int mid = (l + r) >> 1;
    if (indx <= mid) return get_point(indx, l, mid, ls);
    else return get_point(indx, mid + 1, r, rs);
}

ll get_range(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return abs(seg[node]);
    int mid = (l + r) >> 1;
    ll res = 0;
    if (ql <= mid) res = get_range(ql, qr, l, mid, ls);
    if (qr > mid) res = gcd(res, get_range(ql, qr, mid + 1, r, rs));
    return res;
}



/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) a[i] = IO();
    for (int i = n; i; --i) b[i] = a[i] - a[i - 1];
    build(1, n, 1);
    string s;
    while (m--) {
        getstr(s);
        int l = IO(), r = IO();
        if (s[0] == 'Q') {
            ll x = 0;
            if (l > 1) x = get_point(l - 1, 1, n);
            if (x) modify_point(l, x, 1, n);
            print(get_range(l, r, 1, n), '\n');
            if (x) modify_point(l, -x, 1, n);
        } else {
            ll d = IO();
            modify_range(l, r, d, 1, n);
            modify_point(l, d, 1, n);
            if (r < n) modify_point(r + 1, -d, 1, n);
        }
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    int t = 1;
    // // t = IO();
    while (t--) problem();
    // // while (1) problem();
    return 0;
}
```


