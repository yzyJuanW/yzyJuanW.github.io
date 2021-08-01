# AcWing260买票


- 题意：有N个人排队，每一个人都有一个val来对应，每一个后来人都会插入当前队伍的某一个位置pos。要求把队伍最后的状态输出。
- 这道题如果是直接平衡树做的话直接就是 $O(nlogn)$ 的时间复杂度，但杀鸡焉用牛刀
- 因为当前插队的人只会影响后面的人，固我们可以考虑倒过来插队，假设当前正在插入第 $x$ 个人，则一定是在前 $x-1$ 个相对位置中挑一个位置插入，且一旦插入后其相对前 $x-1$ 个人的位置不会再改变。
- 固我们可以这样做：先从最后一个人（即第 $n$ 个人）开始插，在其前 $n-1$ 个人中找其位置，然后此位置将固定为第 $n$ 个人的位置，再来给第 $n-1$ 个插入位置，此时是在前 $n-2$ 个人中找相对位置，注意这时候已经被第 $n$ 个人占用的位置是直接忽略的，以此来寻找第 $n-1$ 个人的位置。后面类推
- 这个过程模拟起来便是用一个数组，开始时全部初始化为1，当要找第 $x$ 个位置时，就找前缀和为 $x$ 的位置，将此位置置为0，这时对于下一个要找的位置 $y$ 依旧是找前缀和恰好（注意是恰好）为 $y$ 的位置，再将其置为0，以此类推，一直为第1个人找到位置
- 上诉过程是可以用线段树（或者树状数组）的单点修改来实现，至于找前缀和恰好为 $x$ 的位置可以用二分加查询来找，固这样的时间复杂度为 $O(nlog^2n)$
- 但这样的时间复杂度我不能接受，可以观察我们找前缀和恰好为 $x$ 的步骤，二分是没有必要的，可以在线段树内找这个位置（线段树的强大），这样时间复杂度就是 $O(nlogn)$
- 具体看代码线段树函数部分

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
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
pii p[N << 1];
int seg[N << 3], que[N << 1];
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-全局变量区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-模板代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/

/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-模板代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-函数代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/

void push_up(int node) {
    seg[node] = seg[ls] + seg[rs];
}

void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = 1;
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    push_up(node);
}

void modify(int k, int val, int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = 0, que[l] = val;
        return;
    }
    int mid = (l + r) >> 1;
    if (seg[ls] >= k) modify(k, val, l, mid, ls); // 找前缀和恰好为k的
    else modify(k - seg[ls], val, mid + 1, r, rs);
    push_up(node);
}
/*↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑-函数代码区-↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑*/






/*↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓-主代码区-↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓*/
void problem() {
    int n = IO();
    for (int i = 0; i < n; ++i) {
        p[i].x = IO(), p[i].y = IO();
    }
    reverse(p, p + n);
    build(1, n);
    for (int i = 0; i < n; ++i) {
        modify(p[i].x + 1, p[i].y, 1, n);
    }
    for (int i = 1; i <= n; ++i) print(que[i], i == n ? '\n' : ' ');
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




