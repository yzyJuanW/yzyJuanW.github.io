# HDU 2476


- 题意：给你两个字符串a、b，你每次可以把一个区间内的所有字符变成一个字符，问让a字符串变成b字符串的最少操作次数

- 首先肯定要知道区间中从无到b在此区间中的字符串的最少操作次数，设为 `f[l][r]`
- 再来就是从a字符串操作成b字符串的最少操作次数，设 `dp[i]` 表示前 `i` 长度字符串，由a变为b的最少操作次数
- 则dp的转移为
  - 若 `a[i] != b[i]` ，则 `dp[i] = f[1][i]`
  - 若 `a[i] = b[i]` ， 则 `dp[i] = dp[i - 1]` ,当前这个位置不用操作
  - 最后有点类似划分dp了 ： `dp[i] = min(dp[i], dp[j] + f[j + 1][i])` 其中 `0 <= j < i`
- 而 `f[l][r]` 的转移则为
  - 若 `b[l] != b[r]` ，则 `f[l][r] = min(f[l + 1][r], f[l][r - 1]) + 1`
  - 若 `b[l] = b[r]` ，则 `f[l][r] = min(f[l + 1][r], f[l][r - 1])`
  - 最后便是区间dp了：`f[l][r] = min(f[l][r], f[l][k] + f[k + 1][r])` 其中 `l <= k < r`

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
const int N = 1e5 + 10, M = 1e6 + 10;
int dp[110], f[110][110];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/

/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    string a, b;
    int n = getstr(a);
    getstr(b);
    a = " " + a, b = " " + b;
    for (int i = n; i >= 1; --i) {
        f[i][i] = 1;
        for (int j = i + 1; j <= n; ++j) { // [i, j]
            f[i][j] = min(f[i + 1][j], f[i][j - 1]);
            if (b[i] != b[j]) f[i][j] += 1;
            for (int k = i; k < j; ++k) {
                f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j]);
            }
        }
    }
    for (int i = 1; i <= n; ++i) {
        dp[i] = f[1][i];
        if (a[i] == b[i]) dp[i] = dp[i - 1];
        for (int j = 0; j < i; ++j) {
            dp[i] = min(dp[i], dp[j] + f[j + 1][i]);
        }
    }
    print(dp[n], '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = 1;
    // t = IO();
    // while (t--) problem();
    while (1) problem();
    return 0;
}

```


