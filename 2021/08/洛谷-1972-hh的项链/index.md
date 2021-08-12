# 洛谷-1972 HH的项链


- 题意：给你一个数组，每次询问你某个区间内不同数的个数
- 本题可以用离线+树状数组来做，常数小，速度快，解法移步至HDU3333刷题记录
- 利用主席树，最暴力的方式便是利用下标来构建一颗主席树，对于每个数，为其下标上的主席树+1，并记录本数上次出现的下标，为其在当前版本的主席树上的下标-1，最后的询问便是查询 `[l, r]` 区间版本的主席树中 `[l, r]` 区间内的和
- 具体看模板区代码（注意，洛谷本题数据加强，本人测试过，此解法在洛谷上加强的数据会超时2个，但在BZOJ上测试小数据范围（ $n \le 5e4$ ）可过）

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
const int N = 1e6 + 10, M = 1e6 + 10;
int a[N], ram, root[N], last[M];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
struct nodes {int l, r, sum = 0; } hjt[N * 35];
int build(int l, int r) {
    int now = ++ram;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    hjt[now].l = build(l, mid);
    hjt[now].r = build(mid + 1, r);
    return now;
}
int modify(int pre, int l, int r, int indx, int val) {
    int now = ++ram;
    hjt[now] = hjt[pre], hjt[now].sum += val;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    if (indx <= mid) hjt[now].l = modify(hjt[now].l, l, mid, indx, val);
    else hjt[now].r = modify(hjt[now].r, mid + 1, r, indx, val);
    return now;
}

int query(int tl, int tr, int ql, int qr, int l, int r) {
    if (ql <= l && r <= qr) return hjt[tr].sum - hjt[tl].sum;
    int mid = (l + r) >> 1, res = 0;
    if (ql <= mid) res = query(hjt[tl].l, hjt[tr].l, ql, qr, l, mid);
    if (qr > mid) res += query(hjt[tl].r, hjt[tr].r, ql, qr, mid + 1, r);
    return res;
}

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO();
    root[0] = build(1, n);
    for (int i = 1; i <= n; ++i) {
        a[i] = IO();
        root[i] = modify(root[i - 1], 1, n, i, 1);
        if (last[a[i]]) root[i] = modify(root[i], 1, n, last[a[i]], -1);
        last[a[i]] = i;
    }
    int m = IO();
    while (m --) {
        int l = IO(), r = IO();
        print(query(root[l - 1], root[r], l, r, 1, n), '\n');
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```

- 超时了两发，怎么办呢？
- 注意到，构建主席树时，每个数可能都会被构建两次主席树
- 不妨换个思路来构建主席树，我们每次询问 `[l, r]` 区间的不同数的个数，其实就是在找 `[l, r]` 区间内 `last[i] < l` 的数的个数，转换一下就是 `last[i] + 1 <= l` 的个数
- 这样的话，我们在构建主席树时，就可以不用在当前版本中该数的下标上来+1操作，而是在当前版本中该数上次出现的位置 + 1（即 `last[i] + 1`）上进行+1操作
- 每次询问 `[l, r]` 区间，其实就是查询 `[l, r]` 版本的主席树中 `[1, l]` 的区间的和
- 综上，我们对于每个数就可以做到只构建一次主席树了
- 在洛谷上跑一下，完全能过

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
const int N = 1e6 + 10, M = 1e6 + 10;
int a[N], ram, root[N], last[M];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
struct nodes {int l, r, sum = 0; } hjt[N * 25];
int build(int l, int r) {
    int now = ++ram;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    hjt[now].l = build(l, mid);
    hjt[now].r = build(mid + 1, r);
    return now;
}
int modify(int pre, int l, int r, int indx, int val) {
    int now = ++ram;
    hjt[now] = hjt[pre], hjt[now].sum += val;
    if (l == r) return now;
    int mid = (l + r) >> 1;
    if (indx <= mid) hjt[now].l = modify(hjt[now].l, l, mid, indx, val);
    else hjt[now].r = modify(hjt[now].r, mid + 1, r, indx, val);
    return now;
}

int query(int tl, int tr, int ql, int qr, int l, int r) {
    if (ql <= l && r <= qr) return hjt[tr].sum - hjt[tl].sum;
    int mid = (l + r) >> 1, res = 0;
    if (ql <= mid) res = query(hjt[tl].l, hjt[tr].l, ql, qr, l, mid);
    if (qr > mid) res += query(hjt[tl].r, hjt[tr].r, ql, qr, mid + 1, r);
    return res;
}

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO();
    root[0] = build(1, n);
    for (int i = 1; i <= n; ++i) {
        a[i] = IO();
        root[i] = modify(root[i - 1], 1, n, last[a[i]] + 1, 1);
        last[a[i]] = i;
    }
    int m = IO();
    while (m --) {
        int l = IO(), r = IO();
        print(query(root[l - 1], root[r], 1, l, 1, n), '\n');
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```


