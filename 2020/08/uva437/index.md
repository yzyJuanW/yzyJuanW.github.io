# UVA437


## [The Tower of Babylon](https://vjudge.net/problem/UVA-437)

- 题意：给你n种有无数个的立方体，现在让你用这些立方体堆一个塔，每个立方体的底面长宽都要严格小于下面立方体的底面长宽，问你最高能堆多高
- 简单的分析
  - 能看得出来是一个矩形嵌套的变种问题，用DAG模型建立来做会非常好码代码
  - 一种立方体有三种摆放方式，假设一个立方体的长宽高为a、b、c，则三种摆放方式分别是以a、b、c为高的摆放方式，固一种立方体可以看成三个立方体
  - 下面就是建立DAG了，如果一个立方体的底面长宽严格大于另一个底面长宽的立方体则连一条有向边过去
  - 最后就DP就是求从某个节点出发的最大距离就是答案
  - 注意到，无论是思考还是码代码都不需要再去想状态方程来，直接当成一个图求最远距离来做，为了加速，我用了邻接表来建立图
  - 下面是代码

```c++
#include <cstring>
#include <cstdio>
#include <algorithm>

using namespace std;

int n, kase = 0, head[100], cnt = 0, dp[500], vis[500];

struct rect{
    int a, b, c;
    rect(){}
    rect(int a, int b, int c) : a(a), b(b), c(c) {}
}r[100];

struct edges{
    int to, next;
    edges(int to = 0, int next = -1) : to(to), next(next) {}
}edge[10010];

bool ok(const rect& x, const rect& y) {
    return (x.a > y.a && x.b > y.b) || (x.b > y.a && x.a > y.b);
}

void add_edge(int u, int v) { edge[++cnt] = edges(v, head[u]); head[u] = cnt; }

bool read() {
    scanf("%d", &n);
    if (!n) return false;
    int x[3];
    for (int i = 0; i < 3 * n;) {
        for (auto &j : x) scanf("%d", &j);
        r[i++] = rect(x[0], x[1], x[2]);
        r[i++] = rect(x[0], x[2], x[1]);
        r[i++] = rect(x[1], x[2], x[0]);
    }
    memset(head, -1, sizeof head);
    memset(edge, 0, sizeof edge);
    memset(dp, 0, sizeof dp);
    memset(vis, 0, sizeof vis);
    cnt = 0;
    for (int u = 0; u < 3 * n; u++) {
        for (int v = 0; v < 3 * n; v++) {
            if (u == v) continue;
            if (!ok(r[u], r[v])) continue;
            add_edge(u, v);//邻接表建图
        }
    }
    return true;
}

int DP(int u) {
    if (u >= 3 * n) return 0;
    if (vis[u]) return dp[u];
    int &res = dp[u], Max = 0;
    res = r[u].c, vis[u] = true;
    for (int v = head[u]; ~v; v = edge[v].next) {//它的下一个节点
        Max = max(Max, DP(edge[v].to));
    }
    return res += Max;
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (read()) {
        int ans = 0;
        for (int i = 0; i < 3 * n; i++) ans = max(ans, DP(i));
        printf("Case %d: maximum height = %d\n", ++kase, ans);
    }
    return 0;
}
```

