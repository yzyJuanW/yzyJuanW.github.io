# Cf366C Dima and Salad


- 题解：给你两个数组，当 `a[i]` 被选时， `b[i]` 必选，问你最终选出a数组的和恰好是b数组的k倍的最大a数组的和，不存在则输出-1
- 题中需要我们做的是选出一个下标集合使得 $\sum a_i \div \sum b_i = k$
- 思维转换一下：$\sum a_i - k \sum b_i = 0$
- 得到： $\sum(a_i - k\times b_i) = 0$
- 根据题意可以转化为：给你每个物品质量为 `a[i] - k * b[i]` 且价值为 `a[i]` 的物品，问你背包容量为0的最大价值为多少
- 需要注意的是可能有质量有负数，只需要每个质量加一个偏移量便可
- 直接看代码的主代码区

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
int a[110], b[110], dp[2][N];
/*------------------------------全局变量区------------------------------*/






/*++++++++++++++++++++++++++++++模板代码区++++++++++++++++++++++++++++++*/

/*------------------------------模板代码区------------------------------*/






/*++++++++++++++++++++++++++++++函数代码区++++++++++++++++++++++++++++++*/
int real(int x) {
    return x + 10100;
}
/*------------------------------函数代码区------------------------------*/






/*++++++++++++++++++++++++++++++主代码区++++++++++++++++++++++++++++++*/
void problem() {
    int n = IO(), k = IO();
    fill_w(a + 1, n);
    fill_w(b + 1, n);
    memset(dp, 0xf7, sizeof dp);
    dp[0][real(0)] = 0;
    int now = 1, pre = 0;
    for (int i = 1; i <= n; ++i) {
        int v = a[i] - k * b[i];
        for (int j = -10000; j <= 10000; ++j) {
            dp[now][real(j)] = max(dp[pre][real(j)], dp[pre][real(j - v)] + a[i]);
        }
        swap(now, pre);
    }
    print(dp[pre][real(0)] == 0 ? -1 : dp[pre][real(0)], '\n');
}
/*------------------------------主代码区------------------------------*/






int main() {
    // int t = IO();
    while (1) problem();
    return 0;
}

```




