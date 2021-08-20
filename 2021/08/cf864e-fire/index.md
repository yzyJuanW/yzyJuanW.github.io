# Cf864E


- 题意：着火了，要拯救有n个物品，对于每个物品的价值为 `p[i]` ，拯救出来所需要的时间为 `t[i]`， 但过了时间 `d[i]` 后该物品便无价值了，输出能获得物品的最大价值和拯救物品的顺序（输出其中一种即可）

- 这道题看是能看出是01背包，但是状态的转移需要花一些心思
- 首先物品的拯救的顺序不能乱来，可以贪心来搞，优先考虑快要无价值的物品
- 然后便是非常朴素的01背包问题和求具体方案了

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
    // int x, y;
    int t, d, p, i;
    bool operator< (const pii& j) {
        return d < j.d;
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
const int N = 1e5 + 10, M = 1e6 + 10;
int dp[110][2005];
pii p[110];
vi<int> res;
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
void solve(int n, int m) {
    if (m == 0 || n == 0) {
        print(res.size(), '\n');
        reverse(all(res));
        for (int x : res) print(x, ' ');
        exit(0);
    }
    if (m < p[n].d && m >= p[n].t && dp[n][m] == dp[n - 1][m - p[n].t] + p[n].p) {
        res.pb(p[n].i);
        solve(n - 1, m - p[n].t);
        res.pop_back();
    }
    solve(n - 1, m);
}
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO();
    for (int i = 1; i <= n; ++i) {
        p[i].t = IO(), p[i].d = IO(), p[i].p = IO();
        p[i].i = i;
    }
    sort(p + 1, p + 1 + n);
    int ans = 0, m = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j <= 2000; ++j) {
            dp[i][j] = dp[i - 1][j];
            if (j < p[i].d && j >= p[i].t) {
                dp[i][j] = max(dp[i][j], dp[i - 1][j - p[i].t] + p[i].p);
            }
            if (i == n && dp[i][j] > ans) ans = dp[i][j], m = j;
        }
    }
    print(ans, '\n');
    solve(n, m);
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}

```


