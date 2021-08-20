# Cf755F PolandBall and Gifts


- 题意：有 `n` 个人，第 `i` 个人想送给第 `p[i]` 个人一份礼物，保证 `p` 数组是一个排列且 `p[i] != i` ，如果 `i` 忘记带礼物，则 `i` 和 `p[i]` 都不会受到礼物，问恰好有 `k` 个人忘记带礼物，最少和最多有几个人收不到礼物
- 如果将送礼物的线路做成图，则将会行形成若干个环。
- 当一个人忘记带礼物时，则会有两个人收不到礼物
- 固最大值可以利用贪心来计算，设某个环的大小为 `x` ，若 `x` 偶数，则只需要让 `k / 2` 的人忘记带礼物便可让整个环的人多不能收到礼物；若 `x` 为奇数，则可以先让 `(x - 1) / 2` 个人忘记带礼物，此时还差一个人能够收到礼物，只需特判此人是否需要忘记带礼物才能达到恰好 `k` 个人忘记带便可
- 再来考虑最小值，这个也考虑贪心做，首先如果某个环的大小为 `x` , 若要环内的 `y` 个人忘记带礼物，则如果 `y < x` 那么最贪心的方式就是让连续 `y` 个人忘记带礼物，则有 `y + 1` 个人收不到礼物（这是最贪心的方式了）,若 `x == y` 则环内所有人都收不到礼物
- 固最小值的答案可以推得为：若某些环的大小和恰好为 `k` ，则最小值为 `k`，否则最小值为 `k + 1`
- 对于最小值，问题转化为若干个大小为 `v` ，数量为 `c` 的数，能否凑齐恰好为 `k` 的数，此为多重背包
- 本题数据太大，尝试过用二进制优化的多重背包，发现复杂度过不去
- 后来想想二进制优化后产生的01背包对于凑数问题可以用bitset优化（具体看代码实现）,速度可观，完全能过
- 代码看主代码区部分便可

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
const double eps = 1e-5, PI = acos(-1.0);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
int len[M], vis[M], f[M], fa[M], siz[M];
bitset<M> dp;
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/
class UF {
public:
    int n;
    // 当前连通分量数目
    int cnt;
    vector<int> size;
    vector<int> parent;

    UF(int _n): n(_n + 1), cnt(_n + 1), size(_n + 1, 1), parent(_n + 1) {
        int i = 0;
        for (auto &x : parent) x = i++;
    }
    
    int findset(int x) {
        return parent[x] == x ? x : parent[x] = findset(parent[x]);
    }
    
    bool unite(int x, int y) {
        x = findset(x);
        y = findset(y);
        if (x == y) {
            return false;
        }
        if (size[x] < size[y]) {
            swap(x, y);
        }
        parent[y] = x;
        size[x] += size[y];
        --cnt;
        return true;
    }
    
    bool conn(int x, int y) {
        x = findset(x);
        y = findset(y);
        return x == y;
    }
};
/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO(), maxv = 0, cnt = 0, k = m;
    UF un(n);
    for (int i = 1; i <= n; ++i) {
        int p = IO();
        un.unite(i, p);
    }
    for (int i = 1; i <= n; ++i) {
        int fa = un.findset(i);
        if (vis[fa]) continue;
        vis[fa] = 1;
        int tot = un.size[fa];
        len[tot] += 1;
        if (tot / 2 <= k) k -= tot / 2, maxv += (tot / 2) * 2;
        else maxv += k * 2, k = 0;
        if (tot % 2 == 1) cnt += 1;
    }
    maxv += min(k, cnt);
    vi<int> v;
    for (int i = 1; i <= n; ++i) {
        if (len[i]) {
            int y = 1;
            while (len[i]) {
                y = min(y, len[i]);
                v.pb(y * i);
                len[i] -= y;
                y += y;
            }
        }
    }
    dp[0] = 1;
    for (size_t i = 0; i < v.size(); ++i) {
        dp |= (dp << v[i]);
    }
    print(m + !dp[m], ' ');
    print(maxv, '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}

```




