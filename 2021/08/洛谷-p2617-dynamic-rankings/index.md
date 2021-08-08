# 洛谷 P2617


- 题意：求解一个区间中的第k大，本题带单点修改
- 考虑如果是静态主席树的话，其本质是利用了前缀和思想，但我们知道前缀和只能查询不能再短时间内单点修改，这个时候就可以考虑不用前缀和来维护主席树了。
- 想到可以单点修改又可以区间查询的数据结构就有线段树和树状数组了
- 为了方便起见，我们用树状数组来维护主席树
- 想象树状数组每个下标都是一个主席树，这样就可以做到单点树修改和区间树查询了
- 值得注意的是：之前的前缀和主席树是当前树的相同结点是继承上一棵树的，他是利用前一个树来创建一颗新的树。但我们用树状数组就是更新式的，他并没有用到上一棵树的信息，同样还是试想树状数组每一个下标都是一颗树，每次都是更新那棵树，而不是创一颗树，这样理解会好一些。
- 主要直接看模板代码区

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
    int q, x, y, k;
    // int x, y;
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
const int N = 2e4 + 10, M = 1e6 + 10;
int a[N], ram, root[N], rnk[N << 1];
set<int> ms;
umap<int, int> mp;
pii p[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
struct nodes{
    int l = 0, r = 0, sum = 0;
}hjt[N * 200];

void push_up(int indx) {
    hjt[indx].sum = hjt[hjt[indx].l].sum + hjt[hjt[indx].r].sum;
}

int modify(int pre, int l, int r, int indx, int val) {
    int now = (pre == 0 ? ++ram : pre);
    if (l == r) {
        hjt[now].sum += val;
        return now;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) hjt[now].l = modify(hjt[now].l, l, mid, indx, val);
    else hjt[now].r = modify(hjt[now].r, mid + 1, r, indx, val);
    push_up(now);
    return now;
}


int query(vi<int>& tl, vi<int>& tr, int l, int r, int k) {
    if (l == r) return l;
    int mid = (l + r) >> 1, res = 0;
    for (int x : tl) res -= hjt[hjt[x].l].sum;
    for (int x : tr) res += hjt[hjt[x].l].sum;
    if (k <= res) {
        for (int& x : tl) x = hjt[x].l;
        for (int& x : tr) x = hjt[x].l;
        return query(tl, tr, l, mid, k);
    }
    for (int& x : tl) x = hjt[x].r;
    for (int& x : tr) x = hjt[x].r;
    return query(tl, tr, mid + 1, r, k - res);
}

void modify_tree(int indx, int val, int n, int len) {
    int x = mp[a[indx]];
    while (indx <= n) {
        root[indx] = modify(root[indx], 1, len, x, val);
        indx += lb(indx);
    }
}

int query_tree(int l, int r, int k, int len) {
    vi<int> tl, tr;
    // 预处理有哪些树状数组的主席树要求和
    for (int i = l - 1; i > 0; i -= lb(i)) tl.pb(root[i]); 
    for (int i = r; i > 0; i -= lb(i)) tr.pb(root[i]);
    return query(tl, tr, 1, len, k);
}
/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) {
        a[i] = IO();
        ms.insert(a[i]);
    }
    for (int i = 1; i <= m; ++i) {
        string s;
        getstr(s);
        if (s[0] == 'Q') {
            p[i].q = 1, p[i].x = IO(), p[i].y = IO();
            p[i].k = IO();
        } else {
            p[i].q = 2, p[i].x = IO(), p[i].y = IO();
            ms.insert(p[i].y);
        }
    }
    int cnt = 0;
    for (int x : ms) mp[x] = ++cnt, rnk[cnt] = x;
    // 初始化建树，和寻常不同，这个树是更新式的，而不是继承式的（直接连接上一棵树的子结点，更新不一样的子结点）
    for (int i = 1; i <= n; ++i) modify_tree(i, 1, n, cnt);
    for (int i = 1; i <= m; ++i) {
        if (p[i].q ==  1) {
            print(rnk[query_tree(p[i].x, p[i].y, p[i].k, cnt)], '\n');
        } else {
            modify_tree(p[i].x, -1, n, cnt);
            a[p[i].x] = p[i].y;
            modify_tree(p[i].x, 1, n, cnt);
        }
    }
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}
```


