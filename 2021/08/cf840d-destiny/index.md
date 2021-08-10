# Cf840D Destiny


- 题意：给你一个数组，问你区间 `[l,r]` 中出现次数超过 `(r - l + 1) / k` 次的最小的数，不存在输出-1
- 在修改一下普通主席树的询问便可，先从主席树的左边开始找，如果左边的个数和都小于了 `(r - l + 1) / k` 那就不用找了，但如果找到了，则右子树就不用找了
- 直接看模板区代码

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
const int inf = (1LL << 31) - 1;
const ll INF = 1e18;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 3e5 + 10, M = 1e6 + 10;
int a[N], ram, root[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
struct nodes {int l, r, sum = 0; } hjt[N * 30];
int build(int l, int r) {
    int now = ++ram;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    hjt[now].l = build(l, mid);
    hjt[now].r = build(mid + 1, r);
    return now;
}
int modify(int pre, int l, int r, int indx) {
    int now = ++ram;
    hjt[now] = hjt[pre], hjt[now].sum += 1;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    if (indx <= mid) hjt[now].l = modify(hjt[now].l, l, mid, indx);
    else hjt[now].r = modify(hjt[now].r, mid + 1, r, indx);
    return now;
}

int query(int tl, int tr, int l, int r, int k) {
    if (hjt[tr].sum - hjt[tl].sum <= k) return -1;
    if (l == r) return l;
    int mid = (l + r) >> 1;
    int res = hjt[hjt[tr].l].sum - hjt[hjt[tl].l].sum, ret = -1;
    if (res >= k) ret = query(hjt[tl].l, hjt[tr].l, l, mid, k);
    if (ret == -1) ret = query(hjt[tl].r, hjt[tr].r, mid + 1, r, k);
    return ret;
}
/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    root[0] = build(1, n);
    for (int i = 1; i <= n; ++i) {
        a[i] = IO();
        root[i] = modify(root[i - 1], 1, n, a[i]);
    }
    while (m--) {
        int l = IO(), r = IO(), k = IO();
        k = (r - l + 1) / k;
        print(query(root[l - 1], root[r], 1, n, k), '\n');
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```


