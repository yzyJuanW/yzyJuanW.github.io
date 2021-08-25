# 狂补dp Atcoder Educational DP Contest(全)


- 狂补dp Atcoder Educational DP Contest(全)

### 注意

- 为了减少篇幅，本博客代码部分只放主代码部分，其余省略快读、快输、头文件等代码，即把代码看做伪代码更佳

## A - Frog 1

- 幼儿级别的dp，直接dp便可

```cpp
void problem() {
    int n = IO();
	for (int i = 0; i < n; ++i) num[i] = IO();
    for (int i = 1; i < n; ++i) {
        dp[i] = dp[i - 1] + abs(num[i] - num[i - 1]);
        if (i >= 2) dp[i] = min(dp[i], dp[i - 2] + abs(num[i] - num[i - 2]));
    }
    print(dp[n - 1]);
}
```

## B - Frog 2

- 还是幼儿级别，比A题多了一重循环

```cpp
void problem() {
    int n = IO(), k = IO();
    for (int i = 0; i < n; ++i) num[i] = IO();
    for (int i = 1; i < n; ++i) {
        dp[i] = dp[i - 1] + abs(num[i] - num[i - 1]);
        for (int j = 2; j <= min(k, i); ++j) {
            if (i >= j) dp[i] = min(dp[i], dp[i - j] + abs(num[i] - num[i - j]));
        }
    }
    print(dp[n - 1]);
}
```

## C - Vacation

- 根据题意定义一个二维dp，第二维表示当前选的是哪个，然后转移就很简单了

```cpp
void problem() {
    int n = IO();
    for (int i = 1; i <= n; ++i) a[i] = IO(), b[i] = IO(), c[i] = IO();
    for (int i = 1; i <= n; ++i) {
        dp[i][0] = max(dp[i - 1][1], dp[i - 1][2]) + a[i];
        dp[i][1] = max(dp[i - 1][0], dp[i - 1][2]) + b[i];
        dp[i][2] = max(dp[i - 1][1], dp[i - 1][0]) + c[i];
    }
    print(max({dp[n][0], dp[n][1], dp[n][2]}));
}
```

## D - Knapsack 1

- 01背包问题

```cpp
void problem() {
	int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) w[i] = IO(), v[i] = IO();
    for (int i = 1; i <= n; ++i) {
        for (int j = m; j >= w[i]; --j) dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
    }
    print(dp[m]);
}
```

## E - Knapsack 2

- 01背包+换意dp
- 原本定义 `dp[i]` 表示容量不超过 `i` 能装的最大价值
- 由于背包容量过大，但我们不难发现物品的价值不大，可以对状态定义进行换意
- 即定义 `dp[i]` 表示装价值为 `i` 物品所花费的最小容量

```cpp
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) w[i] = IO(), v[i] = IO();
    fill_n(dp, N, INF);
    dp[0] = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 100000; j >= v[i]; --j) {
            dp[j] = min(dp[j], dp[j - v[i]] + w[i]);
        }
    }
    for (int i = 100000; i >= 0; --i) {
        if (dp[i] <= m) {
            print(i);
            break;
        }
    }
}
```

## F - LCS

-  LCS+dp求具体方案
- dp求具体方案我还是喜欢递归找，复杂度O($n$)

```cpp
void solve(int n, int m) {
    if (n == 0 || m == 0) {
        reverse(all(ans));
        for (char& c : ans) putchar(c);
        exit(0);
        return;
    }
    if (dp[n][m] == dp[n - 1][m]) solve(n - 1, m);
    else if (dp[n][m] == dp[n][m - 1]) solve(n, m - 1);
    else {
        ans.push_back(a[n]);
        solve(n - 1, m - 1);
    }
}

void problem() {
    scanf("%s\n%s", a + 1, b + 1);
    int n = strlen(a + 1), m = strlen(b + 1);
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (a[i] == b[j]) dp[i][j] = dp[i - 1][j - 1] + 1;
            else dp[i][j] = max(dp[i][j - 1], dp[i - 1][j]);
        }
    }
    solve(n, m);
}
```

## G - Longest Path

- 拓扑dp
- 可以bfs转移，但我是dfs转移的
- 值得注意的是，状态定义为 `dp[i]` 表示从 `i` 点出发能走的最远距离好转移一些

```cpp
int dfs(int u) {
    if (dp[u] != -1) return dp[u];
    int& res = dp[u] = 0;
    for (int& v : mp[u]) {
        res = max(res, dfs(v) + 1);
    }
    return res;
}
void problem() {
    int n = IO(), m = IO();
    for (int i = 0; i < m; ++i) {
        int u = IO(), v = IO();
        mp[u].pb(v);
        ++du[v];
    }
    fill_n(dp + 1, n, -1);
    for (int i = 1; i <= n; ++i) if (!du[i]) ans = max(dfs(i), ans);
    print(ans, '\n');
}
```

## H - Grid 1

- 走格子，老dp模型了

```cpp
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) scanf("%s", g[i] + 1);
    dp[0][1] = 1;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            if (g[i][j] != '.') continue;
            dp[i][j] = (dp[i][j - 1] + dp[i - 1][j]) % mod;
        }
    }
    print(dp[n][m]);
}
```

## I - Coins

- 简单的一个概率dp
- 定义 `dp[i][j]`  表示前 `i` 个硬币中人头朝上的个数为 `j` 的概率

```cpp
void problem() {
    int n = IO();
    for (int i = 1; i <= n; ++i) scanf("%lf", p + i);
    dp[0][0] = 1;
    for (int i = 1; i <= n; ++i) {
        dp[i][0] = dp[i - 1][0] * (1.0 - p[i]);
        for (int j = 1; j <= i; ++j) {
            dp[i][j] += dp[i - 1][j - 1] * p[i] + dp[i - 1][j] * (1.0 - p[i]);
        }
    }
    double ans;
    for (int i = 1; i <= n; ++i) {
        if (i > n - i) ans += dp[n][i];
    }
    printf("%.9f\n", ans);
}
```

## J - Sushi

- 期望dp
- 突破口就是每个盘子上的寿司不超过3个，计数寿司个数为 `i` 的盘子有几个
- 定义 `dp[i][j][k]` 表示剩下 `i` 个盘子上面只有1个寿司， `j` 个盘子上面只有2个寿司， `k` 个盘子只有3个寿司的期望值
- 转移的话，递归记忆化转移比较方便

```cpp
double dfs(int a, int b, int c) {
    if (a + b + c == 0) return 0;
    if (vis[a][b][c]) return dp[a][b][c];
    vis[a][b][c] = 1;
    double tot = a + b + c;
    double &res = dp[a][b][c] = n * 1.0 / tot;
    if (a) res += dfs(a - 1, b, c) * a / tot;
    if (b) res += dfs(a + 1, b - 1, c) * b / tot;
    if (c) res += dfs(a, b + 1, c - 1) * c / tot;
    return res;
}

void problem() {
	n = IO();
    int num[4] = {0};
    for (int i = 0; i < n; ++i) {
        int x = IO();
        ++num[x];
    }
    printf("%.10f", dfs(num[1], num[2], num[3]));
}
```

## K - Stones

- 基础博弈dp

```cpp
void problem() {
    int n = IO(), k = IO();
    for (int i = 0; i < n; ++i) a[i] = IO();
    dp[0] = 0;
    for (int i = 1; i <= k; ++i) {
        int ok = 0;
        for (int j = 0; j < n; ++j) {
            if (i < a[j]) continue;
            if (dp[i - a[j]] == 0) {
                ok = 1; // 如果有一个必输点，则他必赢
                break;
            }
        }
        dp[i] = ok;
    }
    puts(dp[k] ? "First" : "Second");
}
```

## L - Deque

- 区间dp+思维
- 转变一下思路，先手希望 `X-Y` 最大，后手希望 `X-Y` 最小，即后手希望 `Y-X`最大，即都希望自己的分数减去对手的分数达到最大
- 设 `dp[l][r]` 表示 `[l, r]` 区间中先手分数减去后手分数的最大值，转移的话，便是用决策 加上 负数的dp值
- 即 `dp[l][r] = max(a[r] + (-dp[l][r - 1]), a[l] + (-dp[l + 1][r]))`

```cpp
void problem() {
    int n = IO();
    for (int i = 1; i <= n; ++i) a[i] = IO(), dp[i][i] = a[i];
    for (int i = n; i; --i) {
        for (int j = i; j <= n; ++j) {
            dp[i][j] = max(a[j] - dp[i][j - 1], a[i] - dp[i + 1][j]);
        }
    }
    print(dp[1][n]);
}
```

## M - Candies

- 多重背包问题
- 直接三重循环的话会超时，像这样的代码

```cpp
dp[0][0] = 1;
for (int i = 1; i <= n; ++i) {
    for (int j = 0; j <= m; ++j) {
        for (int k = 0; k <= min(a[i], j); ++k) {
            dp[i][j] += dp[i - 1][j - k];
        }
    }
}
```

- 但我们不难发现第三重循环是可以优化掉的
- 分析 `dp[i][j]` 他的转移只会从 `dp[i - 1][x]` （其中 `j - a[i] <= x <= j` ）转移过来，固我们可以用前缀和与滑动窗口的思想来进行优化掉第三重循环

```cpp
void problem() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) a[i] = IO();
    dp[0][0] = 1;
    for (int i = 1; i <= n; ++i) {
        ll sum = 0;
        for (int j = 0; j <= m; ++j) {
            sum += dp[i - 1][j];
            dp[i][j] = (sum + mod) % mod;
            if (j >= a[i]) sum -= dp[i - 1][j - a[i]];
        }
    }
    print(dp[n][m]);
}
```

## N - Slimes

- 区间dp入门题

```cpp
void problem() {
    int n = IO();
    memset(dp, 0x3f, sizeof dp);
    for (int i = 1; i <= n; ++i) a[i] = IO(), sum[i] = sum[i - 1] + a[i], dp[i][i] = 0;
    for (int i = n; i >= 1; --i) {
        for (int j = i; j <= n; ++j) { // [i, j] 区间
            for (int k = i; k < j; ++k) {
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j] + sum[j] - sum[i - 1]);
            }
        }
    }
    print(dp[1][n]);
}
```

## O - Matching

- 状压dp
- 本质就是二分图完美匹配的方案数
- 设 `dp[i][j]` 表示正在决策第 `i` (下标从0开始)个男生，且前 `i` 个男生匹配好的女生为 `j`（二进制）
- 转移用刷表法转移比较好

```cpp
ll dp[22][(1 << 21) + 10];
vector<int> vt[22];
 
int count(int x) {
    int res = 0;
    while (x) {
        x &= x - 1;
        res++;
    }
    return res;
}
 
int m[25][25];
void problem() {
    int n = IO();
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            m[i][j] = IO();
        }
    }
    dp[0][0] = 1;
    int len = 1 << n;
    for (int i = 0; i < len; ++i) vt[count(i)].push_back(i);
    for (int i = 0; i < n; ++i) {
        for (int j : vt[i]) {
            for (int k = 0; k < n; ++k) {
                if (j & (1 << k)) continue;
                if (!m[i][k]) continue;
                dp[i + 1][j ^ (1 << k)] += dp[i][j];
                dp[i + 1][j ^ (1 << k)] %= mod;
            }
        }
    }
    print(dp[n][len - 1]);
}
```

## P - Independent Set

- 树上dp
- 有点类似树上独立集了

```cpp
vector<int> mp[N];
ll dp[N][2]; // i号结点为0/1色的方案数
 
void dfs(int u, int fa) {
    dp[u][0] = dp[u][1] = 1;
    for (int& v : mp[u]) {
        if (v == fa) continue;
        dfs(v, u);
        dp[u][1] = dp[v][0] * dp[u][1] % mod;
        dp[u][0] = dp[u][0] * (dp[v][0] + dp[v][1]) % mod;
    }
}
 
 
void problem() {
    int n = IO();
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        mp[u].pb(v), mp[v].pb(u);
    }
    dfs(1, 0);
    printf((dp[1][0] + dp[1][1]) % mod);
}
```

## Q - Flowers

- LIS的变种，需要用线段树优化

```cpp
const int N = 2e5 + 5;
struct pairs {
    int indx, h;
    ll a;
}p[N];
 
ll seg[N << 2];
 
void update(int indx, ll v, int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = v;
        return;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) update(indx, v, l, mid, node << 1);
    else update(indx, v, mid + 1, r, node << 1 | 1);
    seg[node] = max(seg[node << 1], seg[node << 1 | 1]);
}
 
ll get(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    int mid = (l + r) >> 1;
    ll ret = 0;
    if (ql <= mid) ret = get(ql, qr, l, mid, node << 1);
    if (qr > mid) ret = max(ret, get(ql, qr, mid + 1, r, node << 1 | 1));
    return ret;
}
 
void problem() {
    int n = IO();
    for (int i = 1; i <= n; ++i) {
        p[i].indx = i;
        p[i].h = IO();
    }
    for (int i = 1; i <= n; ++i) p[i].a = IO();
    sort(p + 1, p + 1 + n, [](pairs& i, pairs& j) {
        return i.h < j.h;
    });
    ll ans = 0;
    for (int i = 1; i <= n; ++i) {
        ll maxv = get(1, p[i].indx, 1, n) + p[i].a;
        update(p[i].indx, maxv, 1, n);
        ans = max(maxv, ans);
    }
    printf(ans);
}
```

## R - Walk

- 不多说，考的就是矩阵乘法在图中的意义（结论题）
- 矩阵A的n次幂表示走n次x到y的方案数，对于本题就是求矩阵的k次幂，再求结果矩阵上各个点上的和即可

```cpp
#define vi vector<ll>
#define Mat vector<vi>
 
Mat operator* (Mat a, Mat b) {
    Mat res(N, vi(N, 0));
    for (int i = 0; i < N; ++i) 
        for (int j = 0; j < N; ++j)
            for (int k = 0; k < N; ++k)
                res[i][j] = (res[i][j] + a[i][k] * b[k][j] % mod) % mod;
    return res;
}
 
Mat operator^ (Mat a, long long b) {
    Mat res(N, vi(N, 0));
    for (int i = 0; i < N; ++i) res[i][i] = 1;
    while (b) {
        if (b & 1) res = res * a;
        a = a * a, b >>= 1;
    }
    return res;
}
 
void problem() {
    ll n = IO(), k = IO();
    Mat mat(N, vi(N, 0));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            mat[i][j] = IO();
        }
    }
    Mat res = mat ^ k;
    ll ans = 0;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            ans = (res[i][j] + ans) % mod;
        }
    }
    printf(ans);
}
```

## S - Digit Sum

- 数位dp入门题
- 对于数位dp，记忆化搜比较好理解

```cpp
int num[N], len, n;
 
ll dp[N][2][101];
ll dfs(int x, int limit, int m) {
    ll &res = dp[x][limit][m];
    if (~res) return res;
    if (x == 0) return res = (m == 0);
    res = 0;
    int up = (limit ? num[x] : 9);
    for (int i = 0; i <= up; ++i) {
        res += dfs(x - 1, limit && i == up, (m + i) % n);
        res %= mod;
    }
    return res;
}
 
void problem() {
    string s;
    cin >> s >> n;
    len = s.size();
    for (int i = 0, j = len; i < len; ++i, --j) num[j] = s[i] - '0';
    memset(dp, -1, sizeof dp);
    print(((dfs(len, 1, 0) - 1) % mod + mod) % mod);
}
```

## T - Permutation

- 比较思维的一道dp题
- 设 `dp[i][j]` 表示前 `i` 个排列中（包括 `i` ）最后一位放的是 `j` 的方案数
- 转移的话要注意，当前第 `i` 个放的 `j` ,转移利用到的第 `i - 1` 的排列中将小于 `j` 的视为小于 `j` ，大于等于 `j` 的视为严格大于 `j`
- 由于转移的时候是要三重循环，但草稿可以发现第三种循环可以优化掉的，看代码就懂了

```cpp
int n;
string s;
ll dp[N][N];
void problem() {
    cin >> n >> s;
    dp[1][1] = 1;
    for (int i = 2; i <= n; ++i) {
        if (s[i - 2] == '<') {
            dp[i][1] = 0;
            for (int j = 2; j <= i; ++j) {
                dp[i][j] = (dp[i - 1][j - 1] + dp[i][j - 1]) % mod;
            }
        } else {
            dp[i][i] = 0;
            for (int j = i - 1; j; --j) {
                dp[i][j] = (dp[i - 1][j] + dp[i][j + 1]) % mod;
            }
        }
    }
    ll ans = 0;
    for (int i = 1; i <= n; ++i) {
        ans = (ans + dp[n][i]) % mod;
    }
    cout << ans << endl;
}
```

## U - Grouping

- 状压dp
- 本题对于状压刚入门的童鞋可能比较难，但通过这题只要记住状压是怎么枚举所有子集的便可
- 这里写一遍，还要注意，枚举的复杂度为 O($3^n$)

```cpp
for (int s1 = s; s1; s1 = (s1 - 1) & s) {
    int s2 = s ^ s1;
    // s1 与 s2 互为 s 的补集，且s1与s2不会重复，即利用s1与s2可以不重不漏地枚举完所有子集
}
```

- 本题要的是真子集，所以要改改

```cpp
void problem() {
    int n = IO(), m = 1 << n;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            a[i][j] = IO();
        }
    }
    for (int i = 1; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (((i >> j) & 1) == 0) continue;
            for (int k = j + 1; k < n; ++k) {
                if (((i >> k) & 1) == 0) continue;
                dp[i] += a[j][k];
            }
        }
        for (int j = i & (i - 1); j; j = (j - 1) & i) {
            dp[i] = max(dp[i], dp[j] + dp[j ^ i]);
        }
    }
    print(dp[m - 1]);
}
```

## V - Subtree

- 换根dp
- 换根过程中，考虑到模数可能不为质数，所以要记录每颗子树的前后缀积，

```cpp
void dfs(int u, int fa) {
    dp[u] = 1;
    pre[u].push_back(1);
    for (int v : edg[u]) {
        if (v == fa) continue;
        dfs(v, u);
        dp[u] = dp[u] * (1 + dp[v]) % m;
        pre[u].pb(dp[u]);
    }
    for (int i = (int)edg[u].size() - 1; i >= 0; --i) {
        int v = edg[u][i];
        if (v == fa) continue;
        ll tmp = (dp[v] + 1) % m;
        if (last[u].size()) tmp = last[u].back() * tmp % m;;
        last[u].pb(tmp);
    }
    reverse(all(last[u]));
    last[u].pb(1);
}
 
void solve(int u, int fa, ll sub) {
    int indx_pre = 0, indx_last = 1;
    for (int v : edg[u]) {
        if (v == fa) continue;
        ll tmp = pre[u][indx_pre++] * last[u][indx_last++] % m;
        // printf("%d %d\n", u, tmp);
        f[v] = (sub * tmp % m + 1) * dp[v] % m;
        solve(v, u, (tmp * sub + 1) % m);
    }
}

void problem() {
    n = IO(), m = IO();
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        edg[u].pb(v), edg[v].pb(u);
    }
    dfs(1, 0);
    f[1] = dp[1];
    solve(1, 0, 1);
    for (int i = 1; i <= n; ++i) print(f[i], '\n');
}
```

## W - Intervals

- 线段树优化dp
- 这题有点意思，一开始没发现要数据结构优化，怎么想都觉得是一个O($n^3$)的dp，后来发现可以用线段树优化成O($nlogn$)
- 设 `dp[i]` 表示前 `i` 长度的字符串中，第 `i` 个位置必填 1 的最大价值
- 然后先看转移方程，设结构体 `p` 为题中的区间

$$
dp[i] = dp[j] + p[k].a
$$

$$
其中 0 <= j < i
$$

$$
并且 j < p[k].l \le i
$$

$$
且p[k].r \ge i
$$

- 不难发现，暴力的话，这样转移是 O($n^3$)
- 但我们会发现对于一个 `p[k].l <= i` 的区间，所有 `j < p[k].l` 的 `dp[j]` 都必须要加上这个区间，然后就很容易想到将所有区间按左端点升序排序，然后线段树进行转移便可，让区间 `[0, p[k].l - 1]` 的所有dp值加上 `p[k].a` ， **记得加完要减回去**
- 看代码简单易懂

```cpp
#define ls node << 1
#define rs  (ls) | 1
struct pii {
    int x, y, v;
    bool operator< (const pii& j) {
        return x != j.x ? x < j.x : y < j.y;
    }
};
struct nodes{
    ll maxv, lazy;
};

const int N = 1e5 + 10, M = 1e6 + 10;
ll dp[M << 1];
pii q[N << 1];
nodes seg[N << 3];
vector<int> pos[N << 1];

void push_down(int node) {
    ll &tmp = seg[node].lazy;
    if (!tmp) return;
    seg[ls].lazy += tmp;
    seg[rs].lazy += tmp;
    seg[ls].maxv += tmp;
    seg[rs].maxv += tmp;
    tmp = 0;
    assert(seg[node].lazy == 0);
}
void update_range(int ql, int qr, ll v, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) {
        seg[node].lazy += v;
        seg[node].maxv += v;
        return;
    }
    push_down(node);
    int mid = (l + r) >> 1;
    if (ql <= mid) update_range(ql, qr, v, l, mid, ls);
    if (qr > mid) update_range(ql, qr, v, mid + 1, r, rs);
    seg[node].maxv = max(seg[ls].maxv, seg[rs].maxv);
}
void update_pos(int indx, ll v, int l, int r, int node = 1) {
    if (l == r) {
        seg[node].maxv = v;
        return;
    }
    push_down(node);
    int mid = (l + r) >> 1;
    if (indx <= mid) update_pos(indx, v, l, mid, ls);
    else update_pos(indx, v, mid + 1, r, rs);
    seg[node].maxv = max(seg[ls].maxv, seg[rs].maxv);
}
ll query_range(int ql, int qr, int l, int r, int node = 1) {
    if (ql <= l && r <= qr) return seg[node].maxv;
    push_down(node);
    int mid = (l + r) >> 1;
    ll ret = -llf;
    if (ql <= mid) ret = query_range(ql, qr, l, mid, ls);
    if (qr > mid) ret = max(ret, query_range(ql, qr, mid + 1, r, rs));
    return ret;
}

void problem() {
    int n = IO(), m = IO();
    for (int i = 0; i < m; ++i) {
        q[i].x = IO(), q[i].y = IO(), q[i].v = IO();
    }
    sort(q, q + m);
    for (int i = 0; i < m; ++i) pos[q[i].y].push_back(i);
    ll ans = 0;
    for (int i = 1, j = 0; i <= n; ++i) {
        while (j < m && q[j].x <= i) {
            update_range(0, q[j].x - 1, q[j].v, 0, n);
            j += 1;
        }
        dp[i] = query_range(0, i - 1, 0, n);
        update_pos(i, dp[i], 0, n);
        for (int p : pos[i]) update_range(0, q[p].x - 1, -q[p].v, 0, n);
        ans = max(ans, dp[i]);
    }
    print(ans);
}
```

## X - Tower

- 贪心+01背包
- dp前的贪心排序要大胆地猜，然后证明
- 用领项交换法的思路去思考，当前决策相邻的两个物品 $w_i, s_i$ 和 $w_j,s_j$ ，前面物品（假设合法）的重量为 $X$，若以 $i$ 在 $j$ 上的顺序放的话 $j$ 物品还能承受的重量为 $s_j - X - w_i$，反之 $i$ 物品还能承受的重量为 $s_i - X - w_j$，我们当然是想让 后面的更能承受重量，固 $s_j - X - w_i > s_i - X - w_j$，化简，我们就能发现只需要按 $s_i + w_i < s_j + w_j$ 排序，最后01背包找答案便可

```cpp
struct pii {
    int w, s;
    ll v;
    bool operator< (const pii& j) {
        return w + s < j.w + j.s;
    }
};
pii p[1010];
ll dp[20010];
void problem() {
    int n = IO();
    for (int i = 0; i < n; ++i) {
        p[i].w = IO(), p[i].s = IO(), p[i].v = IO();
    }
    sort(p, p + n);
    for (int i = 0; i < n; ++i) {
        for (int j = p[i].s + p[i].w; j >= p[i].w; --j) {
            dp[j] = max(dp[j], dp[j - p[i].w] + p[i].v);
        }
    }
    print(*max_element(dp, dp + 20001));
}
```

## Y - Grid 2

- 数学+dp
- 显然不能直接dp，会超时
- 如果是n*m的格子，无障碍的话方案数就是 $C^{n - 1}_{n + m - 2}$
- 找突破口，发现障碍的点不多
- 设 `dp[i]` 表示从 `(1, 1)` 到第 `i` 个障碍的方案数，下面是转移
- 设第 `j` 个障碍点在第 `i` 个障碍点的左上方，再设从第 `j` 个障碍点到第 `i` 个障碍点形成的格子是 x * y 的，则

$$
dp[i] += dp[j] * C^{x - 1}_{x + y - 2}
$$

- 最后的答案便是将所有点往 `(n, m)` 点转移得到的答案
- 之间看代码，注意转移的顺序

```cpp
ll powf(ll a, ll b, ll p, ll res = 1) {
    for (a %= p; b; b >>= 1, a = a * a % p) if (b & 1) res = res * a % p;
    return res;
}
struct pii {
    int x, y;
    bool operator< (const pii& j) {
        return x != j.x ? x < j.x : y < j.y;
    }
};

ll dp[N], f[N * 2], inv[N * 2];
pii p[3010];

ll comb(int n, int m) {
    if (n < m) return 0;
    return f[n] * inv[m] % mod * inv[n - m] % mod;
}
void problem() {
    f[0] = inv[0] = 1;
    for (int i = 1; i <= 200000; ++i) {
        f[i] = f[i - 1] * i % mod;
        inv[i] = powf(f[i], mod - 2, mod);
    }
    int h = IO(), w = IO(), n = IO();
    for (int i = 0; i < n; ++i) {
        p[i].x = IO(), p[i].y = IO();
    }
    sort(p, p + n);
    for (int i = 0; i < n; ++i) {
        dp[i] = comb(p[i].x + p[i].y - 2, p[i].x - 1);
    }
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < i; ++j) {
            if (p[j].x <= p[i].x && p[j].y <= p[i].y) {
                int x = p[i].x - p[j].x + 1, y = p[i].y - p[j].y + 1;
                dp[i] = dp[i] - dp[j] * comb(x + y - 2, x - 1) % mod;
                dp[i] = (dp[i] + mod) % mod;
            }
        }
    }
    ll ans = comb(h + w - 2, h - 1);
    for (int i = 0; i < n; ++i) {
        int x = h - p[i].x + 1, y = w - p[i].y + 1;
        ans = ans - dp[i] * comb(x + y - 2, x - 1) % mod;
        ans = (ans + mod) % mod;
    }
    print(ans);
}
```

## Z - Frog 3

- 斜率优化的dp
- 当他写出 $(h_j - h_i)^2 + C$ 的时候，用膝盖想都能猜到是要斜率优化了，再加上h数组还递增，好家伙，二分都省了

```cpp
const int N = 1e5 + 10, M = 1e6 + 10;
ll a[M * 2], dp[M * 2], q[M * 2];
ll pow2(ll x) { return x * x; }
ll gety(int i) { return dp[i] + pow2(a[i]); }
ll getx(int i) { return a[i]; }

int push_check(int j, int k, int i) {
    return gety(k) - gety(j) < 2 * a[i] * (getx(k) - getx(j));
}
int pop_check(int j, int k, int i) {
    return (gety(k) - gety(j)) * (getx(i) - getx(k)) > 
        (gety(i) - gety(k)) * (getx(k) - getx(j));
}

void problem() {
    int n = IO();
    ll m = IO();
    fill_w(a + 1, n);
    dp[1] = 0;
    int l = 0, r = -1;
    q[++r] = 1;
    for (int i = 2; i <= n; ++i) {
        while (l < r && push_check(q[l], q[l + 1], i)) ++l;
        dp[i] = dp[q[l]] + pow2(a[i] - a[q[l]]) + m;
        while (l < r && pop_check(q[r - 1], q[r], i)) --r;
        q[++r] = i;
    }
    print(dp[n]);
}
```


