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
void table() {
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
int pri[M], cnt = 0;
bool isp[M];
// true 为非素数， false 为素数
void table() {
    isp[0] = isp[1] = 1;
    for (int i = 2; i < M; i++) {
        if (!isp[i]) pri[cnt++] = i;
        for (int j = 0; j < cnt && i * pri[j] < M; j++) {// 如果M较大需要注意i*pri[j]会爆int
            isp[i * pri[j]] = 1;
            if (!(i % pri[j])) break;
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
ll pow_f(ll a, ll b, const ll mo) {
    ll ans = 1;
    a %= mo;
    while (b) {
        if (b & 1) ans = (ans * a) % mo;
        a = (a * a) % mo;
        b >>= 1;
    }
    return ans;
}

ll inverse(ll a, ll m) {
    return pow_f(a, m - 2, m);
}
```

### 5. 快速幂

```cpp
#define ll long long
ll powf(ll a, ll b, const ll mod) {// 返回a^b % mod
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
int gauss(int n, int m, vector<vector<double>> &a) { // n行m + 1列的增广矩阵
    int r, c; // 当前行和当前列
    for (r = c = 0; c < m && r < n; ++ c) {
        int maxr = r; // 记录最大
        for (int i = r + 1; i < n; ++i) if (abs(a[i][c]) > abs(a[maxr][c]))
            maxr = i; // 寻找从当前行开始向下走的当前列中的绝对值最大值
        if (r ^ maxr) swap(a[r], a[maxr]); // 如果不是当前行，则交换两行
        if (abs(a[r][c]) < eps) continue; // 如果当前行当前列的最大值为0则不作消元
        for (int i = m; i >= c; --i) a[r][i] /= a[r][c]; // 将当前行的从当前列开始到最后一列
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
int gauss_j(int n, int m, vector<vector<double>> &a) { // n行m + 1列增广矩阵
    int r, c; // 当前行当前列
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

int powf(int a, int b, const int mod, int ans = 1) {
    a %= mod;
    while (b) {
        if (b & 1) ans = ans * a % mod;
        b >>= 1, a = a * a % mod;
    }
    return ans;
}
int inv(int a, int m) {
    return powf(a, m - 2, m);
}
//模意义下的高斯消元不需要用约旦的方式，因为整数不用考虑精度问题
// n 行 m + 1列的增广矩阵，从0开始
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
    for (int i = m - 1; ~i; --i) {
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
            for (int j = 0; j < n; ++j) mat[i - 1][j] = powf(i, j, p);
        }
        int res = gauss(n, n, mat, p);
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
int gauss(int n, int m, vector<vector<int> > &a) { // n行m + 1列的增广矩阵
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
ll powf(ll a, ll b, const ll mod) {// 返回a^b % mod
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
    res = powf(res, n / pk, pk);
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
    return up * inverse(down, pk) % pk * powf(p, cnt, pk) % pk;
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
ll mul(ll a, ll b, ll p) {
    ll res = 0;
    for (a %= p; b; b >>= 1) {
        if (b & 1) res = (res + a) % p;
        a = (a + a) % p;
    }
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
ll excrt(ll *a, ll *b, int n) {// x % b[i] = a[i], 返回最小的x
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

#### 线性复杂度O(n)

```cpp
void getphi() {
    phi[1] = 1;
    for (int i = 2; i < M; ++i) {
        if (!isp[i]) pri[cnt++] = i, phi[i] = i - 1;
        for (int j = 0; j < cnt && i * pri[j] < M; ++j) {
            isp[i * pri[j]] = 1;
            if (i % pri[j] == 0) {
                phi[i * pri[j]] = pri[j] * phi[i];
                break;
            } else {
                phi[i * pri[j]] = (pri[j] - 1) * phi[i];
            }
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
// 计算分子分母的素数个数差来计算结果O(nlogn),需要用到质数表，和快速幂
ll comb(int n, int m, int p) {// 求C(n, m) % p的结果，其中n, m <= 1e6, p <= 1e9
    if (n < m) return 0;
    ll res = 1;
    for (int i = 0; i < cnt && pri[i] <= n; ++i) {
        int num = 0;
        for (int j = n; j; j /= pri[i]) num += j / pri[i];
        for (int j = m; j; j /= pri[i]) num -= j / pri[i];
        for (int j = n - m; j; j /= pri[i]) num -= j / pri[i];
        res = res * powf(pri[i], num, p) % p;
    }
    return res;
}
```

​	
