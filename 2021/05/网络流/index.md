# 网络流


# 网络流

## 一、最大流

### Ekmonds-Karp

- 主要思想就是先找增广路，就是一个贪心的过程，注意这是带反悔的贪心，固要建立一个流为0的反边
- 时间复杂度$O(nm^2)$

#### bfs记录前驱的寻找增广路

- 因为要记录一个点的前驱，该方法一次只能找到一条增广路，具体细节看代码

```cpp
#include <queue>
bool vis[maxn]; // 记录是否在队内
ll minc[maxn]; // 记录增广路的最小流
struct pairs { int u, i; } pre[maxn]; // 用来记录前驱结点
bool bfs(int s, int t, int n) {
    queue<int> q;
    for (int i = 0; i <= n; ++i) vis[i] = false;
    q.push(s), vis[s] = true, minc[s] = inf; // 初始化minc为无穷大
    while (q.size()) {
        int u = q.front(); q.pop();
        for (int i = head[u]; ~i; i = edge[i].next) {
            int v = edge[i].to;
            if (vis[v] || !edge[i].c) continue;
            vis[v] = true, pre[v] = {.u = u, .i = i}; //记录当前点的前驱点和当前点的内存池编号
            minc[v] = min(minc[u], edge[i].c); // 更新最小流
            if (v == t) return true; //找到了终点（汇点）
            q.push(v);
        }
    }
    return false;
}
```

#### EK核心代码

- 一直找前驱，然后减去增广路中的最小费用

```cpp
ll EK(int s, int t, int n) {
    ll ans = 0, &dif = minc[t]; // 引用到达汇点的最小费用
    while (bfs(s, t, n)) {
        ans += dif;
        for (int i = t; i != s; i = pre[i].u) {
            edge[pre[i].i].c -= dif; // 正向边-
            edge[pre[i].i ^ 1].c += dif; // 反向边+
        }
    }
    return ans;
}
```

#### 完整代码

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;
#define ll long long
#include <cctype>
inline long long IO() // 快读略

class EK {
    struct edges {
        int to, next;
        ll cap, flow;// flow为记录当前路径流的流量，cap为容量
    };
    vector<edges> e;
    static const ll inf = 1e18;
    vector<int> head, vis, pre;// vis记录是否在队内, pre记录前驱内存池编号
    vector<ll> minc; // minc记录增广路的最小流
    int n; // 点的个数
    int bfs(int s, int t) {
        queue<int> q;
        for (int i = 0; i <= n; ++i) vis[i] = 0, pre[i] = -1;
        q.push(s), vis[s] = 1, minc[s] = inf;// 初始化minc为无穷大
        while (q.size()) {
            int u = q.front(); q.pop();
            for (int i = head[u]; ~i; i = e[i].next) {
                int v = e[i].to;
                if (vis[v] || e[i].cap == e[i].flow) continue;
                vis[v] = 1, pre[v] = i;
                minc[v] = min(minc[u], e[i].cap - e[i].flow);// 更新最小流
                if (v == t) return 1;//找到了终点（汇点）
                q.push(v);
            }
        }
        return 0;
    }
  public:
    EK(int n = 0) { init(n); }
    void init(int n) {
        this->n = n;
        e.clear();
        head.resize(n + 1, -1);
        pre.resize(n + 1), vis.resize(n + 1), minc.resize(n + 1);
    }
    void add_edge(int u, int v, ll cap, int f = 1) {
        e.push_back({v, head[u], cap, 0});
        head[u] = e.size() - 1;
        if (f) add_edge(v, u, 0, 0);
    }
    ll maxflow(int s, int t) { // 计算最大流
        ll ans = 0, &dif = minc[t];
        while (bfs(s, t)) {
            ans += dif;
            for (int i = pre[t]; ~i; i = pre[e[i ^ 1].to]) {
                e[i].flow += dif; // 正向边flow+
                e[i ^ 1].flow -= dif; // 反向边flow-
            }
        }
        return ans;
    }
    void clearflow() { // 将流清空
        for (auto& x : e) x.flow = 0;
    }
};

int main() {
    int n = IO(), m = IO(), s = IO(), t = IO();
    EK ek(n);
    for (int i = 0; i < m; ++i)  {
        int u = IO(), v = IO(), cap = IO();
        ek.add_edge(u, v, cap);
    }
    printf("%lld", ek.maxflow(s, t));
    return 0;
}
```

### Dinic

- ek算法比较慢，原因在于每次只能找到一条增广路
- dinic算法每次可以利用**深度**（bfs最近距离）找到多个增广路
- 复杂度$O(n^2m)$

#### bfs找多个增广路

- 利用$deep$数组记录每个点的距离源点的最近距离

```cpp
#include <queue>
int deep[maxn], cur[maxn];
// bfs求增广路，一次求出多条增广路
bool bfs(int s, int t, int n) {
    queue<int> q;
    for (int i = 1; i <= n; ++i) deep[i] = 0;
    deep[s] = 1; // 源点的深度为1
    q.push(s);
    while (q.size()) {
        int u = q.front();
        q.pop();
        for (int i = head[u]; ~i; i = edge[i].next) {
            const int &v = edge[i].to;
            const ll &c = edge[i].c;
            if (deep[v] || !c) continue; // 如果被遍历过，或者没有流量了
            deep[v] = deep[u] + 1;
            q.push(v);
        }
    }
    return deep[t] != 0;
}
```

#### 递归在多个增广路上计算流

- 由于一次bfs能找到多条增广路，固可以用dfs对多条增广路同时计算流，这是dinic的核心
- 几个优化
  - **当前弧优化**：对于一条路被计算过后，后面的dfs时这条路就不会再进行对这条路进行计算了，例如$1 \to 2 \to 3$中当$1\to 2$被计算过后，我们可以让$1\to 3$这样
  - **多路增广优化**：普通的dinic一般都是一次bfs多次dfs，而利用多路增广的话就可以做到一次bfs一次dfs，做法就是记录后面的路总共的流再进行return流的总共的大小，这个优化较其他两个优秀
  - **炸点优化**：当一个点遍历完他的所有他的下一个节点，发现一条增广路都没有即`nowflow = 0`，则可以将这个点从图中去掉了，即`deep[i] = -2`

```cpp
// 当前弧优化+多路增广优化+炸点优化
ll dfs(int u, int t, ll f) {
    if (u == t) return f;
    ll nowflow = 0;
    for (int i = cur[u]; ~i; i = edge[i].next) {
        cur[u] = i; // 当前弧优化
        int &v = edge[i].to;
        ll &c = edge[i].c;
        if (deep[v] != deep[u] + 1 || !c) continue;
        if (ll low = dfs(v, t, min(f - nowflow, c))) {
            c -= low, edge[i ^ 1].c += low;
            nowflow += low;// 多路增广优化
            if (nowflow == f) break; // 满流了
        }
    }
    if (!nowflow) deep[u] = -2; // 炸点优化
    return nowflow;
}

ll dinic(int s, int t, int n) {
    ll ans = 0;
    while (bfs(s, t, n)) {
        for (int i = 1; i <= n; ++i) cur[i] = head[i]; // 预处理，方便当前弧优化
        ans += dfs(s, t, inf); // 进过多路增广优化可不用循环
    }
    return ans;
}
```



#### 完整代码

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
#define ll long long
using namespace std;
#include <cctype>
inline long long IO() // 快读略
class Dinic {
    struct edges {
        int to, next;
        ll cap, flow;// flow为记录当前路径流的流量，cap为容量
    };
    vector<edges> e;
    static const ll inf = 1e18;
    vector<int> head, cur, deep;
    int n;
    // bfs求增广路，一次求出多条增广路
    int bfs(int s, int t) {
        queue<int> q;
        for (auto& x : deep) x = 0;
        deep[s] = 1, q.push(s);
        while (q.size()) {
            int u = q.front(); q.pop();
            for (int i = head[u]; ~i; i = e[i].next) {
                int v = e[i].to;
                if (deep[v] || e[i].cap == e[i].flow) continue;
                deep[v] = deep[u] + 1;
                q.push(v);
            }
        }
        return deep[t] != 0;
    }

    ll dfs(int u, int t, ll flow) {
        if (u == t) return flow;
        ll nowflow = 0;
        for (int i = cur[u]; ~i; i = e[i].next) {
            cur[u] = i;// 当前弧优化
            int v = e[i].to;
            if (deep[v] != deep[u] + 1 || e[i].cap == e[i].flow) continue;
            if (ll low = dfs(v, t, min(flow - nowflow, e[i].cap - e[i].flow))) {
                e[i].flow += low, e[i ^ 1].flow -= low;
                nowflow += low;// 多路增广优化
                if (nowflow == flow) break;
            }
        }
        if (!nowflow) deep[u] = -2;// 炸点优化
        return nowflow;
    }
public:
    Dinic(int n = 0) { init(n); }
    void init(int n) {
        this->n = n;
        head.resize(n + 1, -1);
        deep.resize(n + 1);
    }
    void add_edge(int u, int v, ll cap, int f = 1) {
        e.push_back({v, head[u], cap, 0});
        head[u] = e.size() - 1;
        if (f) add_edge(v, u, 0, 0);
    }

    ll maxflow(int s, int t) {
        ll ans = 0;
        while (bfs(s, t)) {
            cur = head;// 预处理，方便当前弧优化
            ans += dfs(s, t, inf);// 进过多路增广优化可不用循环
        }
        return ans;
    }

    void clearflow() {
        for (auto& x : e) x.flow = 0;
    }
};

int main() {
    int n = IO(), m = IO(), s = IO(), t = IO();
    Dinic dinic(n);
    for (int i = 0; i < m; ++i) {
        int u = IO(), v = IO();
        ll c = IO();
        dinic.add_edge(u, v, c);
    }
    printf("%lld", dinic.maxflow(s, t));
    return 0;
}
```



## 二、最小费用最大流

- 最小费用最大流，我发现如果是普通的更改dinic会退化到比ek的复杂度还大（可能是我太菜了）
- 计算mcmf只要将计算最大流中寻找增广路的bfs修改成寻找最短路的就可以了（好像有个公式可以证明的）
- 注意：存图的反向图的边的流必须是0，同时它的费用必须是正向边的费用的相反数

### EK版

- 直接将ek中的bfs换成spfa就好了

#### 完整代码

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;
#define ll long long
#include <cctype>
inline long long IO() // 快读略
class MCMF{
    struct edges {
        int to, next;
        ll cap, cost, flow;;// flow为记录当前路径流的流量，cap为容量
    };
    vector<edges> e;
    static const ll inf = 1e15;
    vector<int> head, pre, inq;
    vector<ll> dist;
    int n;
    int spfa(int s, int t) {// 利用spaf找最小费用的路，即最短路
        for (int i = 0; i <= n; ++i) inq[i] = 0, dist[i] = inf, pre[i] = -1;
        queue<int> q;
        inq[s] = 1, q.push(s), dist[s] = 0;
        while (q.size()) {
            int u = q.front(); q.pop();
            inq[u] = 0;
            for (int i = head[u]; ~i; i = e[i].next) {
                int v = e[i].to;
                ll &cost = e[i].cost;
                if (dist[v] > dist[u] + cost && e[i].cap > e[i].flow) {
                    dist[v] = dist[u] + cost;
                    pre[v] = i;//记录当前点的前驱的内存池编号
                    if (inq[v]) continue;
                    inq[v] = 1 , q.push(v);
                }
            }
        }
        return pre[t] != -1;// 如果说t没有前驱则说明找不到增广路了
    }
public:
    MCMF(int n = 0) { init(n); }
    void add_edge(int u, int v, ll cap, ll cost, int f = 1) {
        e.push_back({v, head[u], cap, cost, 0});
        head[u] = e.size() - 1;
        if (f) add_edge(v, u, 0, -cost, 0);// 建立反向弧，费用相反
    }
    void init(int n) {
        this->n = n;
        head.resize(n + 1, -1);
        pre.resize(n + 1), inq.resize(n + 1);
        dist.resize(n + 1);
    }
    
    pair<ll, ll> mcmf(int s, int t) {
        ll maxflow = 0, mincost = 0;
        while (spfa(s, t)) {
            ll low = inf;
            for (int i = pre[t]; ~i; i = pre[e[i ^ 1].to]) {
                low = min(low, e[i].cap - e[i].flow);//寻找最小流
            }
            for (int i = pre[t]; ~i; i = pre[e[i ^ 1].to]) {
                e[i].flow += low;
                e[i ^ 1].flow -= low;
                mincost += low * e[i].cost;
            }
            maxflow += low;
        }
        return make_pair(maxflow, mincost);
    }
    void clearflow() {
        for (auto& x : e) x.flow = 0;
    }
};

int main() {
    int n = IO(), m = IO();
    MCMF mcmf(n);
    for (int i = 0; i < m; ++i) {
        int u = IO(), v = IO();
        ll cap = IO(), cost = IO();
        mcmf.add_edge(u, v, cap, cost);
    }
    auto [a, b] = mcmf.mcmf(1, n); // 注意c++17才能这样写
    printf("%lld %lld", a, b);
    return 0;
}
```




