# POJ 1742 Coins


- 题意：给你一些硬币面值为 $a_1, a_2, a_3...$个数为 $c_1, c_2, c_3...$，问你1到m有多少个价格可以凑成
- 多重背包，一开始觉得必须要单调队列才能过了，后来看了别人的思路，发现是自己的格局小了
- 我们发现如果一个数可以凑成则不需要对其进行转移了
- 设 `dp[i]` 表示 `i` 是否能够凑成，1表示能，0表示不能
- 用 `num[i]` 表示当前硬币面值凑成花费了多少个，固转移时需要判断有没有超过个数
- 看代码好懂

```cpp
#include <cctype>
#include <cstdio>
#include <cstring>
#include <string>
#include <vector>
#include <algorithm>
#include <bitset>
#include <cmath>
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
// void print(const string& s) { for (char c : s) putchar(c); }
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
const double eps = 1e-5, PI = acos(-1.0);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e5 + 10, M = 1e6 + 10;
int a[110], c[110], dp[N],  num[N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), m = IO();
    if (n + m == 0) exit(0);
    fill_w(a + 1, n), fill_w(c + 1, n);
    fill_n(dp, m + 1, 0);
    dp[0] = 1;
    for (int i = 1; i <= n; ++i) {
        fill_n(num, m + 1, 0);
        for (int j = a[i]; j <= m; ++j) {
            if (!dp[j] && dp[j - a[i]] && num[j - a[i]] < c[i]) {
                dp[j] = 1;
                num[j] = num[j - a[i]] + 1;
            }
        }
    }
    int ans = 0;
    for (int i = 1; i <= m; ++i) {
        ans += dp[i];
    }
    print(ans, '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}

```


