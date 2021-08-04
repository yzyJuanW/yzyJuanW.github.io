# HDU 1421 搬寝室


- 题意：给你一个长度为n的序列，让你在里面挑2k个数出来，然后进行两两配对，配对后的得分为两个数只差的平方，先让你求出最小的得分和
- 首先，两个数的配对方式要贪心选，即要最小化，则只需要挑两个大小接近的数即可，固要将原数组排序
- 排序后，一个数若选的话，则另一个配对的数一定是其相邻的数
- 一个数选与不选，最后选出2k个数，这个解释十分像是一个01背包问题
- 设 `dp[i][j]` 为前 `i` 个数中，选 `2*j`  个数的最优解
- 则转移方程为:
  - `dp[i][j] = max(dp[i - 1][j], dp[i - 2][j - 1] + (a[i] - a[i - 1]) * (a[i] - a[i - 1]) )`
- 第二维大于0的状态全部初始化为正无穷
- 代码直接看主代码区

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
const int inf = 0x3f3f3f3f;
const ll INF = 1e17;
const double eps = 1e-5, PI = acos(-1);
const int dx[] = {-1, 0, 1, 0, -1, 1, 1, -1}; // 上左下右，顺时针
const int dy[] = {0, 1, 0, -1, 1, 1, -1, -1};
const int N = 1e3 + 10, M = 1e6 + 10;
int a[N << 1], dp[N << 1][N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
int cal(int a) { return a * a; }
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), k = IO();
    fill_w(a + 1, n);
    sort(a + 1, a + 1 + n);
    fill_n(dp[0] + 1, k, inf);
    fill_n(dp[1] + 1, k, inf);
    for (int i = 2; i <= n; ++i) {
        fill_n(dp[i] + 1, k, inf);
        for (int j = 1; j <= i / 2 && j <= k; ++j) {
            dp[i][j] = min(dp[i - 1][j], dp[i - 2][j - 1] + cal(a[i] - a[i - 1]));
        }
    }
    print(dp[n][k], '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    // freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    // int t = 1;
    // t = IO();
    // while (t--) problem();
    while (1) problem();
    return 0;
}
```


