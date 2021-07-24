# 树形dp总结


# 树形dp总结

## 树形背包常用思考模型
### 模型一
- 首先对于每个节点我们在dfs中dp转移时可以想象成一个分组背包，每次新遍历一个子树就可以想象成一个分组背包的下一组，转移时利用之前的背包状态进行转移，如果觉得这个模型比较难理解可以看下面的模型二。
### 模型二
- 首先对于每次新遍历一个结点我们都可以想象成是在给这个结点拼接一颗子树如下图

{{< figure src="https://img-blog.csdnimg.cn/20210301114923359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhb182XzY=,size_16,color_FFFFFF,t_70" alt="https://img-blog.csdnimg.cn/20210301114923359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhb182XzY=,size_16,color_FFFFFF,t_70" title="模型图" >}}

- 解释：圆点为树的结点，三角形为一颗子树，线段为边，箭头是拼接方向。蓝色为之前计算好的状态的树，红色为即将**拼接**上去的一颗子树（这颗子树在dfs时计算好了所有状态）
### 模型一与模型二的联系
- 回到模型二，我们可以把蓝色部分想象成是分组背包中的前k - 1个背包状态，红色被第k组背包，子树中的每个方案都是分组背包中的一个物品，可以结合题目进行理解
- 需要注意的点：当我们在转移时一定要保证状态合法，例如转移时利用到的状态必须是这个树真的有这么多个结点

## 【例题1】P3177 树上染色 

- 思路
- 一道树形dp题，想了好久，最终看了题解发现是突破口不对
- 突破口：首先思考怎么求一条边被算过的次数，用组合数学方法思考可以想到就是这条边两个方向（定义一个左边，一个右边）的**黑点个数的乘积** + **白点个数的乘积**
- 假设左边黑点的个数是x，且其左边节点个数是size， 树的总结点个数为n，则这条边计算次数有下面的公式

$$
tot = x \times (k - x) + (size - x) \times (n - size - k + x)
$$
- 有了这个突破口就可以思考怎么dp了
- 首先要明白每条边必定会对最终的答案做出贡献，固我们可以这样来定义状态：$dp[u][i]$表示节点u的子树选i个黑点的最大贡献，由树形背包模型([对应博客点这里](https://blog.csdn.net/hao_6_6/article/details/114259329))可以得到方程，不难得出状态方程这样写
$$
dp[u][i] = max\{ dp[son][j] + dp[u][i - j] + tot \times w \}
$$
- 值得注意的点：$i - j$可能并没有这个状态，即可能这颗树在当前转移之前并没有$i - j$个节点，有一种解决方式就是将dp值初始化为-1，若在转移时dp值变了就证明他是合法的，否则并不合法
- 但是这种转移经过讨论好像他的复杂度接近$O(n^3)$
- 还有一种的转移方式就是
$$
dp[u][i + j] = max\{ dp[son][i] + dp[u][j] + tot \times w\}
$$
- 这样转移的话洛谷是可以拿到100分的，这样就能保证转移时每个子树都只在他们的lca上计算一次，代码如下
```cpp
int dfs(int u, int fa) {
    int cnt = 1;
    // dp[u][1] = dp[u][0] = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v ^ fa) {
            ll &w = edge[i].w; int son = dfs(v, u);
            for (int k = min(m, cnt); ~k; --k) { // 还是要反过来遍历
                for (int j = min(son, m); ~j; --j) {
                    if (j + k > m) continue;
                    ll tot = j * (m - j) + (son - j) * (n - son - m + j);
                    dp[u][j + k] = max(dp[u][j + k], dp[u][k] + dp[v][j] + tot * w);
                }
            }
            cnt += son;
        }
    }
    return cnt;
}
```
- 注意到我第8行和之前的代码对应的循环都是反过来遍历的，原因也解释过了，这个是01背包中我们空间优化时利用到的转移方式用来防止转移覆盖的问题，当然还有一种不用担心转移覆盖的方式，贴上[学长巨巨](https://blog.csdn.net/qq_35577488)的代码，简单易懂

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define pii pair<int,int>
#define pb push_back
#define mp make_pair
const int maxn = 2000 + 5;
const int mod = 1e9 + 7;
ll dp[maxn][maxn];
ll sz[maxn] , n;
vector<pii> e[maxn];
ll tmp[maxn] , up;
void dfs (int u , int fa)
{
    sz[u] = 1;
    for (auto g : e[u]){
        int v = g.first;
        ll w = g.second;
        if (v == fa) continue;
        dfs(v , u);
        for (int i = 0 ; i <= sz[u] + sz[v]; i++) tmp[i] = dp[u][i];
        for (int i = 0 ; i <= min(sz[u] , up) ; i++){
            for (int j = 0 ; j <= min(sz[v] , up) ; j++){
                if (i + j > up) continue;
                ll val = 1ll * j * (up - j) /*黑点*/ + 1ll * (sz[v] - j) * (n - sz[v] - (up - j));
                val *= w;
                tmp[i + j] = max (tmp[i + j] , dp[u][i] + dp[v][j] + val);
            }
        }
        for (int i = 0 ; i <= sz[u] + sz[v] ; i++) dp[u][i] = tmp[i];
        sz[u] += sz[v];
    }
    return ;
}
int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> up;
    for (int i = 1 ; i < n ; i++){
        int x , y , z; cin >> x >> y >> z;
        e[x].pb (mp(y , z));
        e[y].pb (mp(x , z));
    }
    dfs (1 , 0);
    cout << dp[1][up] << endl;
    return 0;
}
```
- 总结：这题是一个基础的树上背包问题，但是找到突破口很重要，不然可能怎么树上dp都不知道，解决方式：多刷题

## 【例题2】P1272 重建道路

- 思路
- 根据[树上背包模型](https://blog.csdn.net/hao_6_6/article/details/114259329)可以想到状态定义：$dp[u][i]$表示以u结点为根结点的子树生成只有i个结点的最少去掉的边数，转移方程如下：

$$
dp[u][i + j] = min\{ dp[u][i] + dp[son][j]\}
$$
- 这里有一个技巧，为了防止转移时覆盖，固用一个tmp函数存起计算的状态
- 其余需要注意的细节看代码注释
- ac代码如下
```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cctype>
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}
#define ll long long
using namespace std;
const int M = 2e3 + 5;
const int maxn = 2e3 + 5, maxm = 2e3 + 5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = -1; }

struct edges {
    int to, next;
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v) {
    edge[++cnt] = {.to = v, .next = head[u]};
    head[u] = cnt;
}

int dp[M][M], siz[M], tmp[M];
int n, m;

void dfs(int u, int fa) {
	// 初始化，根据树形背包模型，一开始的只保留一个结点去掉的边数为0， 保留0个的去掉的边数是1，即拼接上去的那条边
    siz[u] = 1, dp[u][1] = 0, dp[u][0] = 1; 
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa) continue;
        dfs(v, u);
        for (int j = 1; j <= siz[u] + siz[v]; ++j) tmp[j] = INF; // 用一个tmp存计算的状态，防止转移覆盖原来需要用到的dp值
        for (int j = 1; j <= siz[u]; ++j) { // 当前树保证必须选一个
            for (int k = 0; k <= siz[v]; ++k) { // 子树从0开始
                tmp[j + k] = min(tmp[j + k], dp[u][j] + dp[v][k]);
            }
        }
        siz[u] += siz[v];
        for (int j = 1; j <= siz[u]; ++j) dp[u][j] = tmp[j];
    }
}

int main() {
    n = IO(), m = IO();
    init();
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        add(u, v), add(v, u);
    }
    memset(dp, 0x3f, sizeof dp);  // 所有都初始化为最大值
    dfs(1, -1);
    int ans = dp[1][m];
    for (int i = 2; i <= n; ++i) if(siz[i] >= m) ans = min(ans, dp[i][m] + 1); // 最终的答案不一定是在1号结点上去边， +1是因为要加上去除连接父亲结点的边
    printf("%d", ans);
    return 0;
}
```
- 总结：树上背包dp的时候无论是初始化还是转移的时候都需要树上背包模型为基础来考虑

## 【例题3】P1273 有线电视网

- 思路

- 这题想了好久，唯一想到比较有用的就是假设$dp[u][j]$表示以u为根结点的子树花费j元最多可以提供多少用户，然后就没有然后了，都不知道怎么转移，看了[学长巨巨](https://blog.csdn.net/qq_35577488)的思路后发现新大陆，原来还可以利用换意的方式进行dp，即转台这样定义：$dp[u][i]$表示以u为根结点的子树提供j个用户最多能赚多少钱，这样转移又方便，最后的答案就是$dp[1][i]$中大于等于0的最大i
- 转移方程：
$$
dp[u][i + j] = max\{ dp[u][i] + dp[son][j] - w\}
$$
- 其余细节看代码注释
- ac代码
```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cctype>
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}
#define ll long long
using namespace std;
const int M = 3e3 + 5;
const int maxn = 3e3 + 5, maxm = 3e3 + 5;
const ll inf = 0xffffffff;

int head[maxn], cnt;

//初始化
inline void init() { memset(head, -1, sizeof head); cnt = -1; }

struct edges {
    int to, next;
    ll w;
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v, ll w) {
    edge[++cnt] = {.to = v, .next = head[u], .w = w};
    head[u] = cnt;
}

ll mon[M], sum, dp[M][M], son[M], tmp[M];
int n, m;

void dfs(int u) {
    if (u > n - m) son[u] = 1, dp[u][1] = mon[u]; // 如果是叶子节点则赚的钱就是他自己， 且叶子个数为1
    dp[u][0] = 0;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        dfs(v);
        for (int j = 1; j <= son[u] + son[v]; ++j) tmp[j] = -inf; // 防止转移时覆盖，初始化tmp为负无穷，当然用01背包的方式逆向循环也可以
        for (int j = 0; j <= son[u]; ++j) {
            for (int k = 0; k <= son[v]; ++k) {
                ll w = (k == 0 ? 0 : edge[i].w); // 如果子树一个叶子都不选当然不用减去子树的拼接边（回忆述树上背包模型）
                tmp[j + k] = max(tmp[j + k], dp[u][j] + dp[v][k] - w);
            }
        }
        son[u] += son[v];
        for (int j = 1; j <= son[u]; ++j) dp[u][j] = tmp[j];
    }
}

int main() {
    n = IO(), m = IO();
    init();
    for (int i = 1; i <= n - m; ++i) {
        int k = IO();
        while (k--) {
            int a = IO(), c = IO();
            add(i, a, c);
        }
    }
    memset(dp, 0xfe, sizeof dp); // 初始化为最小值
    for (int i = n - m + 1; i <= n; ++i) mon[i] = IO();
    dfs(1);
    int ans = 0;
    for (int i = 1; i <= m; ++i) {
        // printf("%lld\n", dp[1][i]);
        if (dp[1][i] >= 0) ans = max(ans, i); // 寻找最大值i
    }
    printf("%d", ans);
    return 0;
}
```
- 总结：这题需要状态定义有点新奇，当定义状态时发现没办法转移或者是没办法存下这个状态就考虑换意一下
