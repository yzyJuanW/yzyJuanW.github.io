# 洛谷 4137 mex


- 题意：给你一个数组，每次区间询问mex

- 这题用主席树比较好写，而且还是在线算法
- 利用权值线段树在每个权值上记录该数最后出现的下标，再次基础上加上可持续化，便是主席树了
- 最终查询 `[l, r]` 区间的mex时，则是在版本 `r` 的权值线段树中找下标小于 `l` 的最小的数即可
- 注意：
  - 本题不需要离散化，但若是需要离散化则需要同时将比原来数多1的数和0也同时离散化，因为答案必定是原数组中某个数+1或者是0
  - 权值线段树下标至少为1，我们让所有数先+1
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
const int N = 2e5 + 10, M = 1e6 + 10;
int a[N], ram, root[N], vis[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
struct nodes {int l, r, minv; } hjt[N * 25];

int modify(int pre, int l, int r, int val, int pos) {
    int now = ++ram;
    hjt[now] = hjt[pre];
    if (l == r) {
        hjt[now].minv = pos;
        return now;
    }
    int mid = (l + r) >> 1;
    if (val <= mid) hjt[now].l = modify(hjt[now].l, l, mid, val, pos);
    else hjt[now].r = modify(hjt[now].r, mid + 1, r, val, pos);
    hjt[now].minv = min(hjt[hjt[now].l].minv, hjt[hjt[now].r].minv);
    return now;
}

int query(int tr, int l, int r, int ql) {
    if (l == r) return l;
    int mid = (l + r) >> 1;
    if (hjt[hjt[tr].l].minv < ql) return query(hjt[tr].l, l, mid, ql);
    return query(hjt[tr].r, mid + 1, r, ql);
}

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), len = 0, m = IO();
    for (int i = 1; i <= n; ++i) {
        a[i] = IO() + 1;
        len = max(len, a[i] + 1);
    }
    for (int i = 1; i <= n; ++i) {
        root[i] = modify(root[i - 1], 1, len, a[i], i);
    }
    while (m --) {
        int l = IO(), r = IO();
        print(query(root[r], 1, len, l) - 1, '\n');
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```


