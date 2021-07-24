# UVA1025


## [A Spy in the Metro](https://vjudge.net/problem/UVA-1025)

- 本篇是由DP总结中搬出的，目的是记录刷题进度
- 大致题意：一个线性的地铁，有$n(2 \le n \le 50)$个站，目标是在$T(0 \le T \le 200)$时刻从1号站到n号站，注是规定刚好T时刻，不是在T时刻之前也不是在T时刻之后，从1号点出发，可以在途中转乘，问你最少的中途等车时间
  - 第i站到第i + 1站需要花的时间为$t_i$
  - 有$M1(M1 \le 50)$个车从1号站出发，出发时间分别为$d_1, d_2, d_3……, d_{M1}\ \ (d_i < d_{i + 1}, d_i \le 250)$，同样有M2个车从n号站出发，时间格式同上
  - 其余细节看题
- 简单分析：
  - 就看当先的状态，假设现在的时间是tim，正在第p个站有三种策略
    - 等1分钟，剩下的下一分钟再说
    - 如果有向1号站行的车，乘坐上去
    - 如果有向n号站行的车，乘坐上去
  - 那么这个是怎么建立DAG图的呢，对于每个点，定义它包括的属性有时间和站点，如果当前时间当前站点有开往下个一或者上一个站的车，则将到站的时间和对应的站点连一条有向的边，例如当前时间是15，站点是3，并且有开向第2站点的车，10单位的时间后到达，但没有向下一个站开的车，则在属性为（15，3）的节点连一条有向边到（25，2）的节点，
  - 当然千万别忘了一点就是下1单位时间的同一站点也有一条边，即（15，3）到（16，3）也有一条有向边
  - 然后就可以利用这个DAG图的节点属性来定义状态了，即设dp[p][tim]为在p站点tim时刻的状态，既然是求最小路，则状态就是到当前节点的最短路
  - 对应三种策略的状态转移方式
    - 当前的状态等于下一单位时间的状态加1的等待时间
    - 当前状态等于到站后的时间和站点的状态
    - 同上
  - 最后取一个最小值
  - 边界条件就是如果在T时刻和n站点刚好到达，则返回0，因为不用等了，如果超过了T时间，则说明是从某个站点到另一个站点后时间过了，则返回无穷大表示不用等了，如果刚好到T时刻且没到达n站点，则说明后面再怎么坐车时间也过了，也返回无穷大
  - 说了这么多，却没写状态转移方程是因为打算使用记忆化来做，所有不用转移方程，直接考虑当前递归的来自哪些结果就好了
  - 代码如下

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>

using namespace std;

const int INF = 0x3f3f3f3f;

int n, T, t[100], kase = 0, dp[110][210];
bool has_train[110][210][2], vis[110][210];

bool read() {
    int m, x;
    scanf("%d", &n);
    if(!n) return false;
    scanf("%d", &T);
    for (int i = 1; i < n; i++) scanf("%d", t + i);
    memset(has_train, 0, sizeof has_train);
    memset(vis, 0, sizeof vis);
    scanf("%d", &m);
    for (int i = 0; i < m; i++) {
        scanf("%d", &x);
        has_train[1][x][0] = true;
        for (int j = 2; j <= n; j++) {
            has_train[j][x + t[j - 1]][0] = true;
            x += t[j - 1];
        }
    }
    scanf("%d", &m);
    for (int i = 0; i < m; i++) {
        scanf("%d", &x);
        has_train[n][x][1] = true;
        for (int j = n - 1; j >= 1; j--) {
            has_train[j][x + t[j]][1] = true;
            x += t[j];
        }
    }
    return true;
}

int DP(int p, int tim) {
    int &d = dp[p][tim];
    if (tim > T) return INF;
    if (tim == T) return p == n ? 0 : INF;
    if (vis[p][tim]) return d;
    vis[p][tim] = true, d = INF;
    d = DP(p, tim + 1) + 1;
    if (p < n && has_train[p][tim][0]) d = min(d, DP(p + 1, tim + t[p]));
    if (p > 1 && has_train[p][tim][1]) d = min(d, DP(p - 1, tim + t[p - 1]));
    return d;
}


int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (read()) {
        int ans = DP(1, 0);
        if (ans >= INF) printf("Case Number %d: impossible\n", ++kase);
        else printf("Case Number %d: %d\n", ++kase, ans);
    }
    return 0;
}
```

