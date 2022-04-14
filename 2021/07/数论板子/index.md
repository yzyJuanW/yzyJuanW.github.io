# 数论板子


### 1. gcd与lcm

```cpp
typedef long long ll;
//最大公因数，公约数
ll gcd(ll a, ll b, ll m = 1) { while(b) m = a % b, a = b, b = m; return a; }
//最小公倍数
ll lcm(ll a, ll b) { return a / gcd(a, b) * b; }
```

### 2. ex_gcd

```cpp
/*
通解为
x' = x * c / gcd + (b / gcd) * k
y' = y * c / gcd - (a / gcd) * k
*/
typedef long long ll;
// 扩展欧几里得算法核心函数
void exgcd(ll a, ll b, ll &g, ll &x, ll &y) {
    if (!b) {
        g = a, x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, g, y, x);
    y -= x * (a / b);
}
/**
 * 此函数为 求解 ax + by = c
 * 返回 x的最小正整数解
 * 返回 -1 说明无解
*/
ll minx(ll a, ll b, ll c) {
    ll x, y, g;
    exgcd(a, b, g, x, y);
    if (c % g != 0) return -1;
    ll t = abs(b / g);
    x = (x * c / g % t + t) % t;
    return x == 0 ? x + t : x; // 返回最小正整数解
}
```

### 3. 素数筛

#### 埃式筛

```cpp
const int M = 1e5 + 5;
int pri[M], cnt = 0;
bool isp[M];
// 复杂度O(nloglogn)
// true 为非素数， false 为素数
void table_prime() {
	isp[0] = isp[1] = true;
    for (int i = 2; i < M; i++) {
        if (isp[i]) continue;
        pri[cnt++] = i;
        for (int j = i + i; j < M; j += i) isp[j] = true;
    }
}
```

#### 线性筛

```cpp
// 复杂度O(n)
const int M = 1e5 + 10;
vector<int> pri;
int minp[M]; // 存放x的最小素因子
void table(int n = 1e5) { // if x >= 2 && minp[x] == x 则为素数
    for (int i = 1; i <= n; ++i) minp[i] = i;
    for (int i = 2; i <= n; ++i) {
        if (minp[i] == i) pri.push_back(i);
        for (int p : pri) {
            if (p * i > n) break;
            minp[p * i] = p;
            if (i % p == 0) break;
        }
    }
}
```

### 4. 逆元

#### 线性版

```cpp
typedef long long ll;
// 时间复杂度O(n)
void fny(const int &n, ll *inv, const ll mod) {
    inv[0] = inv[1] = 1;
    for (ll i = 2; i <= n; i++) {
        inv[i] =((mod - mod / i) * inv[mod % i]) % mod;
    }
}
```

#### 扩欧版

```cpp
typedef long long ll;
void exgcd(ll a, ll b, ll &g, ll &x, ll &y) {
    if (!b) {
        g = a, x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, g, y, x);
    y -= x * (a / b);
}

// ax=1(mod m)
ll inverse(ll a, ll m) {//扩展欧几里得法求逆元，返回-1代表没有逆元
    ll g, x, y;
    exgcd(a, m, g, x, y);
    return g == 1 ? (x % m + m) % m : -1;
}
```

#### 费马小定理版

```cpp
//a ^ (p - 1) = 1 (mod p), p为素数
//a ^ (p - 2) = a ^ (-1) (mod p)
//a 的逆元为 a ^ (p - 2)
typedef long long ll;
ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}

ll inverse(ll a, ll m) {
    return ksm(a, m - 2, m);
}
```

### 5. 快速幂

```cpp
typedef long long ll;
ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}
```

### 6. 矩阵快速幂

#### 结构体版

```cpp
const ll MOD = 1e9 + 7;
#define MO(x) ((x) % MOD)

struct Mat {
    ll mat[10][10];
    int n; // n * n 阶矩阵
    Mat(int n = 2) : n(n) { memset(mat, 0, sizeof mat); }//记得修改
    void to_one() {
        for (int i = 0; i < n; i++)
            mat[i][i] = 1;
    }
    Mat operator*(const Mat a) const {
        Mat res;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                ll sum = 0;
                for (int k = 0; k < n; k++) {
                    sum += MO(this->mat[i][k] * a.mat[k][j]);
                }
                res.mat[i][j] = MO(sum);
            }
        }
        return res;
    }
    ll *operator[](int x) { return mat[x]; }
};


Mat pow_f(Mat a, ll b) { //a ^ b次幂
    Mat ans;
    ans.to_one();
    while (b) {
        if (b & 1) ans = ans * a;
        a = a * a;
        b >>= 1;
    }
    return ans;
}
```

#### vector重载运算符版

```cpp
#define ll long long
#define vi vector<ll>
#define Mat vector<vi>
const int X = 6;
const ll mod = 1e9 + 7;
Mat operator* (Mat a, Mat b) {
    Mat res(X, vi(X, 0));
    for (int i = 0; i < X; ++i)
        for (int j = 0; j < X; ++j)
            for (int k = 0; k < X; ++k)
                res[i][j] = (res[i][j] + a[i][k] * b[k][j] % mod) % mod;
    return res;
}
// 要保证矩阵A不为0
Mat operator^ (Mat a, long long b) {
    Mat res(X, vi(X, 0));
    for (int i = 0; i < X; ++i) res[i][i] = 1;
    while (b) {
        if (b & 1) res = res * a;
        a = a * a, b >>= 1;
    }
    return res;
}
```

### 7. 高斯消元

高斯消元做多了会发现，容易被卡常，有几种解决办法，将vector换成数组，如果是异或高斯则可以用bitset优化，

#### 普通浮点数高斯消元，洛谷模板题

```cpp
// 洛谷模板题
#include <algorithm>
#include <cstdio>
#include <vector>

using namespace std;
const int N = 110;
const double eps = 1e-6; // 用来控制进度
// 普通的高斯消元是将矩阵转化成上三角的形式，再回带求出答案
double ans[N]; // 用来记录答案
int gauss(int n, int m, vector<vector<double>> &a) { // n行m+1列增广矩阵，下标从0开始
    int r, c; // r为当前行和c为当前列
    for (r = c = 0; c < m && r < n; ++ c) {
        int maxr = r; // 记录最大
        for (int i = r + 1; i < n; ++i) if (abs(a[i][c]) > abs(a[maxr][c]))
            maxr = i; // 寻找从当前行开始向下走的当前列中的绝对值最大值
        if (r ^ maxr) swap(a[r], a[maxr]); // 如果不是当前行，则交换两行
        if (abs(a[r][c]) < eps) continue; // 如果当前行当前列的最大值为0则不作消元
        for (int i = m; i >= c; --i) a[r][i] /= a[r][c]; // 将当前行的当前列开始到最后一列
        for (int i = r + 1; i < n; ++i) {
            if (abs(a[i][c]) < eps) continue; // 如果改行的当前列已经是0，则不作消元
            for (int j = m; j >= c; --j) { // 逆向消元，可以少开一个变量
                a[i][j] -= a[r][j] * a[i][c];
            }
        }
        ++r;
    }
    if (r < n) { // 无穷解 或者 无解
        for (int i = r; i < n; ++r) if (abs(a[i][n]) < eps) return 0;
        return -1; // 无穷解
    }
    for (int i = n - 1; ~i; --i) { // 回带
        for (int j = i + 1; j < n; ++j) {
            a[i][n] -= a[i][j] * ans[j];
        }
        ans[i] = a[i][n];
    }
    return 1; // 唯一解
}
vector<vector<double>> a;
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 0; i < n; ++i) {
        a.push_back({});
        for (int j = 0; j <= n; ++j) {
            double x;
            scanf("%lf", &x);
            a[i].push_back(x);
        }
    }
    if (gauss(n, n, a) == 1) {
        for (int i = 0; i < n; ++i) printf("%.2f\n", ans[i]);
    } else puts("No Solution");
    return 0;
}
```

#### 浮点数高斯约旦消元法， 洛谷模板题

```cpp
// 洛谷模板题
#include <cstdio>
#include <cmath>
#include <vector>

using namespace std;
const double eps = 1e-6;
double ans[110]; // 记录答案
int gauss_j(int n, int m, vector<vector<double>> &a) { // n行m+1列增广矩阵，下标从0开始
    int r, c; // r为当前行和c为当前列
    for (r = c = 0; c < m && r < n; ++c) { // 
        int maxr = r; // 记录最大值
        for (int i = r + 1; i < n; ++i) if (abs(a[i][c]) > abs(a[maxr][c]))
            maxr = i; // 寻找从当前行开始向下走的当前列中的绝对值最大值
        if (maxr ^ r) swap(a[r], a[maxr]); // 交换两行
        if (abs(a[r][c]) < eps) continue; // 如果为当前行中的当前列的值为0
        for (int i = 0; i < n; ++i) { // 约旦消元
            if (abs(a[i][c]) < eps || i == r) continue; // 如果是当前行或者改行的当前列已经是0
            for (int j = m; j >= c; --j) a[i][j] -= a[i][c] / a[r][c] * a[r][j];
        }
        ++r;
    }
    if (r < n) return 0; // 无解或者无穷解
    for (int i = 0; i < n; ++i) ans[i] = a[i][n] / a[i][i]; // 回带
    return 1;
}

vector<vector<double>> a;
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 0; i < n; ++i) {
        a.push_back({});
        for (int j = 0; j <= n; ++j) {
            double x;
            scanf("%lf", &x);
            a[i].push_back(x);
        }
    }
    if (gauss_j(n, n, a) == 1) {
        for (int i = 0; i < n; ++i) printf("%.2f\n", ans[i]);
    } else puts("No Solution");
    return 0;
}
```

#### 模意义下的高斯消元法，POJ - 2065 SETI 

```cpp
// POJ - 2065 SETI 
#include <cassert>
#include <vector>
#include <cstdio>
#include <algorithm>
#include <cstring>

using namespace std;

int gcd(int a, int b) { int m; while(b) m = a % b, a = b, b = m; return a; }
int lcm(int a, int b) { return a / gcd(a, b) * b; }
int p, ans[310]; // 记录答案

typedef long long ll;
ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}
int inv(int a, int m) {
    return ksm(a, m - 2, m);
}
//模意义下的高斯消元不需要用约旦的方式，因为整数不用考虑精度问题
// n行m+1列增广矩阵，下标从0开始
int gauss(int n, int m, vector<vector<int> > &a, const int &p) { // 传入模p
    int r, c; // 当前行和当前列
    for (r = c = 0; c < m && r < n; ++c) {
        int maxr = r; // 记录最大值
        for (int i = r + 1; i < n; ++i) if (abs(a[i][c]) > abs(a[maxr][c])) 
            maxr = i; // 寻找当前列中从当前行开始的绝对值的最大值
        if (maxr ^ r) swap(a[r], a[maxr]); // 交换两行
        if (!a[r][c]) continue; // 如果为0
        for (int i = r + 1; i < n; ++i) {
            if (!a[i][c]) continue; // 如果当前列中 改行已经为0
            int LCM = lcm(abs(a[i][c]), abs(a[r][c]));
            int x = LCM / abs(a[i][c]), y = LCM / abs(a[r][c]); // 使该行乘x，当前行乘y使得他们在当前列的数都变成同一个数
            if (a[i][c] * a[r][c] < 0) y = -y; // 如果有一个是负数
            for (int j = c; j <= m; ++j) 
                a[i][j] = ((a[i][j] * x - a[r][j] * y) % p + p) % p;
        }
        ++r;
    }
    for (int i = r; i < n; ++i) if (a[i][c]) return 0; // 无解
    if (r < m) return -1; // 无穷解
    for (int i = m - 1; ~i; --i) {// 回带
        int tmp = a[i][m];
        for (int j = i + 1; j < m; ++j) {
            if (!a[i][j]) continue;
            tmp -= ans[j] * a[i][j];
            tmp = ((tmp % p) + p) % p;
        }
        ans[i] = tmp * inv(a[i][i], p) % p; // 求逆元可以换成别的方式求
    }
    return 1;
}

vector<vector<int> > mat;
char str[110];
int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        mat.clear();
        scanf("%d %s\n", &p, str + 1);
        int n = strlen(str + 1);
        for (int i = 1; i <= n; ++i) {
            mat.push_back(vector<int>(n + 1, 0));
            if (str[i] != '*') mat[i - 1][n] = str[i] - 'a' + 1;
            for (int j = 0; j < n; ++j) mat[i - 1][j] = ksm(i, j, p);
        }
        gauss(n, n, mat, p);
        for (int i = 0; i < n; ++i) printf("%d ", ans[i]);
        puts("");
    }
    return 0;
}
```

#### 异或的高斯消元法（带解决自由变元的），POJ1681 Painter's Problem

```cpp
//POJ1681 Painter's Problem
#include <vector>
#include <cstdio>
#include <algorithm>

using namespace std;
const int N = 305;
int ans[N], freew[N]; // 记录答案和记录自由变元是哪些
//01矩阵，例如第一行为 a11 * x1 ^ a12 * x2 ^ ... ^ a1n * xn = y1的矩阵
// 初等行变换不再是某行加减某行，而是异或了
int gauss(int n, int m, vector<vector<int> > &a) { // n行m+1列增广矩阵，下标从0开始
    int num = 0, r, c; // 自由变元的个数 当前行 当前列
    for (int i = 0; i < n; ++i) ans[i] = freew[i] = 0; // 初始化答案和自由变元
    for (r = c = 0; r < n && c < m; ++c) {
        int maxr = r; // 记录最大值
        for (int i = r + 1; i < n && !a[maxr][c]; ++i) maxr = i; // 查找1
        if (maxr ^ r) swap(a[r], a[maxr]); // 交换两行
        if (!a[r][c]) {// 如果这个数是0， 则说明第c个变元是自由变元
            freew[num++] = c; // 记录自由变元
            continue;
        }
        for (int i = r + 1; i < n; ++i) { // 消元，消成一个上三角
            if (!a[i][c]) continue; //如果该行的当前列是0则该行不作消元
            for (int j = m; j >= c; --j) a[i][j] ^= a[r][j]; // 直接异或，和加法不同
        }
        ++r;
    }
    for (int i = r; i < n; ++i) if (a[i][m]) return -1; // 无解
    int cnt = 1 << (n - r), ret = 1 << 29; // 自由变元的取值方案数， 记录最小操作数
    for (int i = 0; i < cnt; ++i) { // 状态压缩求解
        int res = 0, indx = i; // 当前方案的最小操作数， 操作方案
        for (int j = 0; j < num; ++j, indx >>= 1) {
            ans[freew[j]] = indx & 1;
            res += indx & 1; // 如果是1则说明这个地方需要操作，固操作数+1
        }
        for (int j = r - 1; j >= 0; --j) {
            ans[j] = a[j][m]; // 记录答案
            for (int k = j + 1; k < m; ++k) {
                if (a[j][k]) ans[j] ^= ans[k]; // 如果这个数的系数不为0
            }
            res += ans[j]; // 增加操作数
        }
        ret = min(res, ret); // 取最小值
    }
    return ret;
}

vector<vector<int> > mat;
int n;
const int dx[] = {0, 1, 0, -1, 0};
const int dy[] = {0, 0, 1, 0, -1};
char g[30][30];
bool check(int r, int c) { return r >= 0 && c >= 0 && r < n && c < n; }
int main() {	
    int _;
    scanf("%d", &_);
    for (int t = 1; t <= _; ++t) {
        mat.clear();
        scanf("%d", &n);
        mat.resize(n * n, vector<int>(n * n + 1, 0));
        for (int i = 0, r = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j, ++r) {
                for (int k = 0; k < 5; ++k) {
                    int nr = i + dx[k], nc = j + dy[k];
                    if (check(nr, nc)) mat[r][nr * n + nc] = 1;
                }
            }
        }
        for (int i = 0, r = 0; i < n; ++i) {
            scanf("%s", g[i]);
            for (int j = 0; j < n; ++j, ++r) {
                if (g[i][j] == 'w') mat[r][n * n] = 1;
            }
        }
        int res = gauss(n * n, n * n, mat);
        printf("%d\n", res);
    }
    return 0;
}
```

#### 矩阵求逆，时间复杂度O($2 \times n^3$)，洛谷板子题

```cpp
// 洛谷板子题
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
typedef long long ll;
using namespace std;
const long long mod = 1e9 + 7;

ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}
// 模意义下的高斯消元不需要用约旦的方式，因为整数不用考虑精度问题
// 利用高斯消元，剩下的是上三角，所以要倒过来回带
// n行n + n列增广矩阵，下标从0开始，求矩阵逆，被卡常尝试将vector换成数组
int gauss(int n, vector<vector<ll>>& a, vector<vector<ll>>& ans) { 
    ans.resize(n, vector<ll>(n, 0));
    for (int i = 0; i < n; ++i) ans[i][i] = 1;
    for (int i = 0; i < n; ++i) {
        for (int j = i + 1; j < n; ++j) {
            if (a[j][i]) {
                swap(a[j], a[i]);
                swap(ans[j], ans[i]);
                break;
            }
        }
        if (!a[i][i]) return -1;
        ll x = ksm(a[i][i], mod - 2, mod);
        for (int j = 0; j < n; ++j) { // 此行都乘1/a[i][i]，使得a[i][i] = 1
            a[i][j] = a[i][j] * x % mod;
            ans[i][j] = ans[i][j] * x % mod;
        }
        for (int j = i + 1; j < n; ++j) {
            x = a[j][i]; // x为倍数即row_j - row_i * x
            for (int k = 0; k < n; ++k) {
                a[j][k] = ((a[j][k] - a[i][k] * x) % mod + mod) % mod;
                ans[j][k] = ((ans[j][k] - ans[i][k] * x) % mod + mod) % mod;
            }
        }
    }
    for (int i = n - 1; i >= 0; --i) { // 回带， 
        for (int j = i + 1; j < n; ++j) {// 不知道为什么这样写快一些
            ll x = a[i][j]; // row_i - row_j * x
            for (int k = 0; k < n; ++k) {
                a[i][k] = ((a[i][k] - a[j][k] * x) % mod + mod) % mod;
                ans[i][k] = ((ans[i][k] - ans[j][k] * x) % mod + mod) % mod;
            }
        }
    }
    return 1;
}
auto main() -> int {
    int n;
    scanf("%d", &n);
    vector<vector<ll>> vt(n, vector<ll>(n, 0));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            scanf("%lld", &vt[i][j]);
        }
    }
    vector<vector<ll>> ans;
    int res = gauss(n, vt, ans);
    if (res < 0) return puts("No Solution"), 0;
    for (auto& v : ans) {
        for (ll& x : v) {
            printf("%lld ", x);
        }
        puts("");
    }
    return 0;
}
```

### 8. 卢卡斯定理

#### lucas

```cpp
const ll mod = 10007;// 注意lucas算法，mod必须为质数
ll fac[mod + 10], inv[mod + 10];
void fny(const int &n, ll *inv, const ll mod) {
    fac[0] = fac[1] = inv[0] = inv[1] = 1;
    for (ll i = 2; i <= n; i++) {
        inv[i] =((mod - mod / i) * inv[mod % i]) % mod;
        fac[i] = fac[i - 1] * i % mod;
    }
}

ll comb(ll n, ll m) {
    if (m > n) return 0;
    return fac[n] * inv[fac[n - m] * fac[m] % mod] % mod;
}


ll lucas(ll n, ll m) { // 本函数不考虑comb的时间复杂度的话就是O(logmod)
    if (m == 0) return 1;
    if (n < mod) return comb(n, m);
    return comb(n % mod, m % mod) * lucas(n / mod, m / mod) % mod;
}
```

#### exlucas

```cpp
ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}
void exgcd(ll a, ll b, ll &g, ll &x, ll &y) {
    if (!b) {
        g = a, x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, g, y, x);
    y -= x * (a / b);
}

// ax=1(mod m)
ll inverse(ll a, ll m) {//扩展欧几里得法求逆元，返回-1代表没有逆元
    ll g, x, y;
    exgcd(a, m, g, x, y);
    return g == 1 ? (x % m + m) % m : -1;
}
ll crt(ll *a, ll *b, int n) {// x % b[i] = a[i], 返回最小的x， b[i]中互质， O(nlogn)
    ll mul = 1, ret = 0;
    for (int i = 0; i < n; ++i) mul *= b[i];
    for (int i = 0; i < n; ++i) {
        ll minlcm = mul / b[i];
        ll inv = inverse(minlcm, b[i]); // 求逆元
        ret = (ret +  minlcm * inv * a[i]) % mul;
    }
    return (ret + mul) % mul;
}

ll cal(ll n, ll p, ll pk) { // 计算n!中取出所有p因子后mod pk的结果
    if (n == 0) return 1;
    ll res = 1;
    for (int i = 1; i < pk; ++i) {
        if (i % p) res = res * i % pk;
    }
    res = ksm(res, n / pk, pk);
    int len = n % pk;
    for (int i = 1; i <= len; ++i) {
        if (i % p) res = res * i % pk;
    }
    return res * cal(n / p, p, pk) % pk;
}

ll comb(ll n, ll m, ll p, ll pk) {// 计算C(n, m) % pk的结果，其中p^k = pk
    if (n < m) return 0;
    ll up = cal(n, p, pk), down = cal(m, p, pk) * cal(n - m, p, pk) % pk, cnt = 0;
    for (ll i = n; i; i /= p) cnt += i / p;
    for (ll i = m; i; i /= p) cnt -= i / p;
    for (ll i = n - m; i; i /= p) cnt -= i / p;
    return up * inverse(down, pk) % pk * ksm(p, cnt, pk) % pk;
}
//直接调用此函数即可
ll exlucas(ll n, ll m, ll p) { // 计算C(n, m) % p，其中p不为质数
    ll b[50], a[50], len = 0, tmp = p;
    for (ll i = 2; i * i <= tmp; ++i) {
        if (tmp % i) continue;
        b[len] = 1;
        while (tmp % i == 0) b[len] *= i, tmp /= i;
        a[len] = comb(n, m, i, b[len]);
        len += 1;
    }
    if (tmp > 1) b[len] = tmp, a[len] = comb(n, m, tmp, tmp), len += 1;
    return crt(a, b, len);
}
```

### 9. 线性基

```cpp
struct LB {
    using ll = long long;
    ll d[65], cnt, num; // cnt 原序列的个数， num 基的个数
    bool re;
    LB () : cnt(0), num(0), re(false) { memset(d, 0, sizeof d); }
    // 添加一个数x
    void add(ll x) {
        ++cnt;
        for (int i = 60; ~i && x; --i) {
            if ((x >> i) & 1) {
                if (d[i]) x ^= d[i];
                else d[i] = x, x = 0, ++num, re = 0;
            }
        }
    }
    // 询问是否能异或出x
    bool check(ll x) {
        for (int i = 60; ~i && x; --i) {
            if ((x >> i) & 1) {
                if (d[i]) x ^= d[i];
                else return true;
            }
        }
        return false;
    }
    ll get_max() {
        ll res = 0;
        for (int i = 60; ~i; --i) {
            if ((d[i] ^ res) > res) res ^= d[i];
        }
        return res;
    }
    
    // 求的是线性基的异或最小值，不是原序列，否则要特判是否为0
    ll get_min() {
        for (int i = 0; i <= 60; ++i) {
            if (d[i]) return d[i];
        }
    }
    void rebuild() {
        for (int i = 0; i <= 60; ++i) {
            for (int j = 0; j < i; ++j) {
                if ((d[i] >> j) & 1) {
                    d[i] ^= d[j];
                }
            }
        }
        re = true;
    }
    ll k_th(ll k) {
        if (!re) rebuild();
        if (k == 1 && num < cnt) return 0; // 如果异或得到0
        if (num < cnt) --k;
        ll res = 0;
        for (int i = 0; i <= 60; ++i) {
            if (d[i]) {
                if (k & 1) res ^= d[i];
                k >>= 1;
            }
        }
        return res;
    }
};
```

### 10. 中国剩余定理

#### Crt

```cpp
ll crt(ll *a, ll *b, int n) {// x % b[i] = a[i], 返回最小的x， b[i]中互质， O(nlogn)
    ll mul = 1, ret = 0;
    for (int i = 0; i < n; ++i) mul *= b[i];
    for (int i = 0; i < n; ++i) {
        ll minlcm = mul / b[i];
        ll inv = inverse(minlcm, b[i]); // 求逆元
        ret = (ret +  minlcm * inv * a[i]) % mul;
    }
    return (ret + mul) % mul;
}
```

#### ExCrt

```cpp
ll mul(ll a, ll b, ll p) { // 快速乘
    if (a >= p) a %= p;
    if (b >= p) b %= p;
    if (p <= 1000000000) return a * b % p;
    if (p <= 1000000000000ll)
        return (((a * (b >> 20) % p) << 20)
                + (a * (b & ((1 << 20) - 1)))) % p;
    ll d = (ll)floor(a * (long double)b / p + 0.5);
    ll ret = (a * b - d * p) % p;
    if (ret < 0) ret += p;
    return ret;
}
void exgcd(ll a, ll b, ll &g, ll &x, ll &y) {
    if (!b) {
        g = a, x = 1, y = 0;
        return;
    }
    exgcd(b, a % b, g, y, x);
    y -= x * (a / b);
}
ll excrt(ll *a, ll *b, int n) {// x % b[i] = a[i], 返回最小的x，下标从0开始
    ll res = a[0], minlcm = b[0], x, y, g;
    for (int i = 1; i < n; ++i) {
        ll c = (a[i] - res % b[i] + b[i]) % b[i];
        exgcd(minlcm, b[i], g, x, y);
        if (c % g != 0) return -1; // 无解
        x = mul(x, c / g, b[i] / g);// 快速乘
        res += x * minlcm;
        minlcm *= b[i] / g;
        res = (res % minlcm + minlcm) % minlcm;
    }
    return (res % minlcm + minlcm) % minlcm;
}
```

### 11. 欧拉函数

#### 求单个数的欧拉函数

```cpp
int euler_phi(int n) {
    int ans = n;
    for (int i = 2; i * i <= n; ++i) {
        if (n % i == 0) {
            ans = ans / i * (i - 1);
            while (n % i == 0) n /= i;
        }
    }
    if (n > 1) ans = ans / n * (n - 1);
    return ans;
}
```

#### 线性复杂度O(n)

```cpp
const int M = 1e6 + 5;
int phi[M], minp[M];
vector<int> pri;
void table_phi(int n = 1e5) {
    for (int i = 1; i <= n; ++i) minp[i] = i;
    phi[1] = 1;
    for (int i = 2; i <= n; ++i) {
        if (minp[i] == i) pri.push_back(i), phi[i] = i - 1;
        for (int p : pri) {
            if (i * p > n) break;
            minp[i * p] = p;
            if (i % p == 0) {
                phi[i * p] = p * phi[i];
                break;
            }
            phi[i * p] = (p - 1) * phi[i];
        }
    }
}
```

### 12. 求组合数

#### 递推版，针对取余p为质数（如果有的话），且m较小的情况,O(m)

```cpp
ll comb(ll n, ll m) {
    if (n < m) return 0;
    ll ret = 1;
    for (int i = 1; i <= m; ++i) ret = ret * (n - i + 1) / i;
    return ret;
}
```

#### 素因子版，针对取余p不为质数，其n较小的情况，O(nlogn)

```cpp
// 计算分子分母的素数个数差来计算结果O(nlogn),需要用到质数表，和快速幂，cnt为质数个数
// 求C(n, m) % p的结果，其中n, m <= 1e6, p <= 1e9，注意，要打出小于n的所有质数
// p 理论上可以很大，但过大就可能需要快速乘了
ll comb(int n, int m, int p) { 
    if (n < m) return 0;
    ll res = 1;
    for (int i = 0; i < cnt && pri[i] <= n; ++i) {
        int num = 0;
        for (int j = n; j; j /= pri[i]) num += j / pri[i];
        for (int j = m; j; j /= pri[i]) num -= j / pri[i];
        for (int j = n - m; j; j /= pri[i]) num -= j / pri[i];
        res = res * ksm(pri[i], num, p) % p;
    }
    return res % p;
}
```

​	

### 13. FFT

```cpp
template <class T>
class Poly {// 时间复杂度O(nlogn), 若被卡常，可尝试手写复数
    #define cpd complex<double>
    constexpr static double PI = acos(-1.0);
    vector<int> rev;
    void fft(vector<cpd>& vc, int opt, int n) { // opt=1:FFT(系数式->点值式), opt=-1:IFFT
        for (int i = 0; i < n; ++i) if (i < rev[i]) swap(vc[i], vc[rev[i]]); // 位逆序置换
        for (int h = 2; h <= n; h <<= 1) { // 蝴蝶操作
            cpd wn(cos(PI * 2 / h), sin(PI * 2 / h) * opt); // 主n次单位根
            for (int j = 0; j < n; j += h) {
                cpd w(1, 0);
                for (int k = j; k < j + h / 2; ++k, w *= wn) {
                    cpd tmp = w * vc[k + h / 2], u = vc[k];
                    vc[k] = u + tmp, vc[k + h / 2] = u - tmp;
                }
            }
        }
        if (opt == 1) return;
        for (int i = 0; i < n; ++i) vc[i].real(vc[i].real() / n);
    }
public:
    vector<T> result; // 用来存最后的答案
    void convolution(const vector<T>& a, const vector<T> &b) { // 传入a和b两个多项式，是int就int
        int Alen = a.size(), Blen = b.size(), m = 1, n = 2;
        while (n < Alen + Blen) n <<= 1, m += 1;
        vector<cpd> A(n, cpd(0, 0)), B(n, cpd(0, 0));
        rev.resize(1, 0), result.clear();
        for (int i = 1; i < n; ++i) rev.push_back((rev[i >> 1] >> 1) | (i & 1) << (m - 1));
        for (int i = 0; i < n; ++i) {
            if (i < Alen) A[i].real(a[i]);
            if (i < Blen) B[i].real(b[i]);
        }
        fft(A, 1, n), fft(B, 1, n);
        for (int i = 0; i < n; ++i) A[i] *= B[i];
        fft(A, -1, n);
        // 根据题意自己改最后要的是什么
        for (int i = 0; i < Alen + Blen - 1; ++i) result.push_back(round(A[i].real())); 
    }
    #undef cpd
};
```

### 14. FNTT

```cpp
typedef long long ll;
ll ksm(ll a, ll b, const ll mod = 1e9 + 7) {// 返回a^b % mod
    if (a == 0 && b != 0) return 0;
    ll res = 1;
    for (a %= mod; b; b >>= 1, a = a * a % mod)
        if (b & 1) res = res * a % mod;
    return res;
}
template <class T>
class Poly { // 被卡常就用int+重写模数乘吧
    vector<int> rev;
    constexpr static ll g = 3, gi = 332748118, mod = 998244353; // g * gi % mod = 1
    void ntt(vector<ll>& vt, int opt, int n) { // opt=1:FFT, opt=-1:IFFT
        for (int i = 0; i < n; ++i) if (i < rev[i]) swap(vt[i], vt[rev[i]]); // 位逆序置换
        for (int i = 1; i < n; i <<= 1) {
            ll gn = ksm(opt ? g : gi, (mod - 1) / (i << 1), mod); // 单位原根
            for (int j = 0; j < n; j += (i << 1)) {
                ll g0 = 1;
                for (int k = 0; k < i; ++k, g0 = g0 * gn % mod) {
                    ll u = vt[j + k], tmp = g0 * vt[i + j + k] % mod;
                    vt[j + k] = (u + tmp) % mod;
                    vt[i + j + k] = (u - tmp + mod) % mod;
                }
            }
        }
    }
public:
    vector<T> result; // 用来存最后的答案
    void convolution(const vector<T>& a, const vector<T> &b) { // 传入a和b两个多项式
        int Alen = a.size(), Blen = b.size(), m = 1, n = 2;
        while (n < Alen + Blen) n <<= 1, m += 1;
        vector<ll> A(n, 0), B(n, 0);
        rev.resize(1, 0), result.clear();
        for (int i = 1; i < n; ++i) rev.push_back((rev[i >> 1] >> 1) | (i & 1) << (m - 1));
        for (int i = 0; i < n; ++i) {
            if (i < Alen) A[i] = (a[i] + mod) % mod;
            if (i < Blen) B[i] = (b[i] + mod) % mod;
        }
        ntt(A, 1, n), ntt(B, 1, n);
        for (int i = 0; i < n; ++i) A[i] = A[i] * B[i] % mod;
        ntt(A, 0, n);
        ll inv = ksm(n, mod - 2, mod);
        for (int i = 0; i < Alen + Blen - 1; ++i) result.push_back(A[i] * inv % mod);
    }
};
```

### 15. 分治FFT

```cpp
//给g[1~n], f[0] = 1, f[i] = sum{ f[i - j] * g[j] }, 求f[0~n], 
template <class T>
class PolyDivide { // 传入的g[0]可设为0
    vector<T> g;
    Poly<T> poly;
    constexpr static ll mod = 998244353; // 一般都是这个模数
    void cdq(int l, int r) {
        if (l == r) return;
        int mid = (l + r) >> 1;
        cdq(l, mid);
        vector<T> a(result.begin() + l, result.begin() + mid + 1); // 取f函数的[l, mid]范围的数
        vector<T> b(g.begin(), g.begin() + r - l + 1); // 去 g函数的 [0, r - l]范围的数
        poly.convolution(a, b); // 多项式乘
        auto& res = poly.result;
        for (int i = mid + 1; i <= r; ++i) result[i] = (result[i] + res[i - l]) % mod;
        cdq(mid + 1, r); // 注意cdq的顺序
    }
public:
    vector<T> result;
    void calculate(vector<T>& g) { // 传入 g[0~n]
        this->g = g;
        result.resize(g.size(), 0);
        result[0] = 1; // 初始化 f[0] = 1;
        cdq(0, g.size() - 1); // 计算 f[0~n]
    }
};
```


