# 图论板子


### 前置存图

```cpp
const int N = 1e5 + 10, M = 2e5 + 10;
const int INF = 0x3f3f3f3f;

int head[N], cnt;

//初始化
void init(int n) { fill_n(head, n + 5, -1); cnt = -1; }

struct edges {
    int to, next;
    int w;
    void add(int t, int n, int w) {
        to = t, next = n, this->w = w;
    }
}edge[N << 1]; //无向图则需要乘2

inline void add(int u, int v, int w) {
    edge[++cnt].add(v, head[u], w);
    head[u] = cnt;
}

```

### 1. 最短路

#### dijkstra

```cpp
//顶点数和边数
const int maxn = 1e5 + 10, maxm = 2e5 + 10;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt, dis[maxn];
bool vis[maxn];
//初始化
void init(int n) { // n个点
    fill_n(head, n + 1, -1);
    cnt = 0;
}

struct edges {
    int to, next;
    int w;
    edges(int to = 0, int next = -1, int w = 0) : to(to), next(next), w(w) {}
}edge[maxm << 1]; //无向图则需要乘2

inline void add_edges(int u, int v, int w) {
    edge[++cnt] = edges(v, head[u], w);
    head[u] = cnt;
}

struct qnode{
    int v;
    int w;
    qnode(int v = 0, int w = 0) : v(v), w(w) {}
    bool operator< (const qnode &t) const { return w > t.w; }
};

void dij(int n, int s) {//n 为顶点数， m 为边数
    for (int i = 0; i <= n; ++i) dis[i] = INF, vis[i] = 0;
    dis[s] = 0;
    priority_queue<qnode> heap;
    heap.push(qnode(s, dis[s]));
    while (heap.size()) {
        int u = heap.top().v;
        heap.pop();
        if (vis[u]) continue;
        vis[u] = true;
        for (int i = head[u]; ~i; i = edge[i].next) {
            int v = edge[i].to;
            int w = edge[i].w;
            if (!vis[v] && dis[u] + w < dis[v]) { //松弛
                dis[v] = dis[u] + w;
                heap.push(qnode(v, dis[v]));
            }
        }
    }
}
```

#### bellman-ford

```cpp
const int maxn = 1e5 + 10, maxm = 2e5 + 10, inf = 0x3f3f3f3f;
int dis[maxn];

struct edges {
    int u, v, w;
    edges(int u = 0, int v = 0, int w = 0) : u(u), v(v), w(w) {}
} edge[maxm];

bool bf(int n, int m, int s) { // n个点， m个边， s为起点
    fill_n(dis, n + 1, inf);
    dis[s] = 0;
    for (int k = 1; k < n; k++) {
        int ok = 1;
        for (int i = 1; i <= m; i++) {
            if (dis[edge[i].v] > dis[edge[i].u] + edge[i].w) { //松弛
                dis[edge[i].v] = dis[edge[i].u] + edge[i].w;
                ok = 0;
            }
        }
        if (ok) break; // 小优化，无松弛操作
    }
    for (int i = 1; i <= m; i++) {
        if (dis[edge[i].v] > dis[edge[i].u] + edge[i].w) {
            return false; // 有负环
        }
    }
    return true;// 无负环
}
```

#### spfa

```cpp
const int maxn = 1e5 + 10, maxm = 2e5 + 10, inf = 0x3f3f3f3f;
int dis[maxn], num[maxn], head[maxn], cnt; //num 数组是判断是否有负环
bool inq[maxn];

void init(int n) { // n个点
    fill_n(head, n + 5, -1);
    cnt = 0;
}

struct edges {
    int to, w, next;
    edges(int to = 0, int w = 0, int next = -1) : to(to), w(w), next(next) {}
} edge[maxm];

inline void add_edges(int u, int v, int w) {
    edge[++cnt] = edges(v, w, head[u]);
    head[u] = cnt;
}

bool spfa(int n, int s) {// n个点， s为起点
    for (int i = 0; i <= n; ++i) {
        dis[i] = inf, num[i] = inq[i] = 0;
    }
    queue<int> q;
    q.push(s);
    inq[s] = num[s] = 1, dis[s] = 0;
    while (q.size()) {
        int u = q.front();
        q.pop();
        inq[u] = false;
        for (int i = head[u]; ~i; i = edge[i].next) {
            int v = edge[i].to, w = edge[i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                if (!inq[v]) {
                    q.push(v);
                    inq[v] = true, num[v]++;
                    if (num[v] >= n) return false;
                    //如果从1号点到x的最短路中包含至少n个点（不包括自己），则存在环
                }
            }
        }
    }
    return true;
}
```

#### floyd

```cpp
const int M = 2e2;
int n, m; //顶点数和边数
int dis[M][M];

void floyd() {
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (dis[i][j] > dis[i][k] + dis[k][j]) {
                    dis[i][j] = dis[i][k] + dis[k][j];
                }
            }
        }
    }
}

void init() {
    memset(dis, 0x3f, sizeof dis);
    for (int i = 0; i < M; ++i) dis[i][i] = 0;
}
```

### 2. 生成树

#### kruskal 适合稀疏图

```cpp
#define ll long long
using namespace std;
#include <cctype>
inline long long IO() {} //省略快读
const int M = 2e5 + 10, N = 5e5 + 5;
int fa[M];
struct edges { int u, v; ll w; } e[N];

bool cmp(edges& i, edges& j) { return i.w < j.w; }
int findset(int x) { return x == fa[x] ? x : fa[x] = findset(fa[x]); }

bool un(int a, int b) {
    int fa1 = findset(a), fa2 = findset(b);
    if (fa1 == fa2) return false;
    fa[fa1] = fa2;
    return true;
}

ll kruskal(int n, int m) {
    sort(e, e + m, cmp);
    for (int i = 0; i <= n; ++i) fa[i] = i;
    int cnt = 0;
    ll ans = 0;
    for (int i = 0; i < m; ++i) {
        if (un(e[i].u, e[i].v)) {
            ans += e[i].w;
            if (++cnt == n - 1) break;
        }
    }
    return n - 1 == cnt ? ans : -1;
}

int main() {
    int n = IO(), m = IO();
    for (int i = 0; i < m; ++i) e[i].u = IO(), e[i].v = IO(), e[i].w = IO();
    printf("%lld\n", kruskal(n, m));
    return 0;
}
```

### 3. tarjan

### 4. 网络流

#### Edmonds-Karp算法，速度较慢

```cpp
#define ll long long
#include <cctype>
inline long long IO() // 快读略
const int N = 205, M = 1e4 + 5;
struct edges{
    int to, next;
    ll cap, flow;// flow为记录当前路径流的流量，cap为容量
    void add(int a, int b, ll c) {
        to = a, next = b;
        cap = c, flow = 0; 
    }
};

struct EK{
    edges e[M];
    const ll inf = 1e18;
    int head[N], cnt, n = 0, vis[N], pre[N];// vis记录是否在队内, pre记录前驱内存池编号
    ll minc[N]; // 记录增广路的最小流
    void init(int n) {
        this->n = n, cnt = -1;
        fill_n(head, n + 1, -1);
    }
    void add(int u, int v, ll cap, int f = 1) {
        e[++cnt].add(v, head[u], cap);
        head[u] = cnt;
        if (f) add(v, u, 0, 0);
    }
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
    ll ek(int s, int t) {
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
    void clearflow() { // 清空流
        for (int i = 0; i <= cnt; ++i) e[i].flow = 0;
    }
}ek;


int main() {
    int n = IO(), m = IO(), s = IO(), t = IO();
    ek.init(n);
    for (int i = 0; i < m; ++i)  {
        int u = IO(), v = IO(), cap = IO();
        ek.add(u, v, cap);
    }
    printf("%lld", ek.ek(s, t));
    return 0;
}
```

#### dinic, 当前弧优化+多路增广优化+炸点优化(模板题),复杂度$O(n^2m)$

```cpp
inline long long IO() // 快读略
const int N = 205, M = 1e4 + 5;
struct edges{
    int to, next;
    ll cap, flow;// flow为记录当前路径流的流量，cap为容量
    void add(int a, int b, ll c) {
        to = a, next = b;
        cap = c, flow = 0; 
    }
};

struct Dinic {
    edges e[M];
    const ll inf = 1e18;
    int head[N], cnt, n = 0, deep[N], cur[N];
    void init(int n) {
        this->n = n, cnt = -1;
        fill_n(head, n + 1, -1);
    }
    void add(int u, int v, ll cap, int f = 1) {
        e[++cnt].add(v, head[u], cap);
        head[u] = cnt;
        if (f) add(v, u, 0, 0);
    }
    // bfs求增广路，一次求出多条增广路
    int bfs(int s, int t) {
        queue<int> q;
        fill_n(deep, n + 1, 0);
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
    
    ll dinic(int s, int t) {
        ll ans = 0;
        while (bfs(s, t)) {
            for (int i = 0; i <= n; ++i) cur[i] = head[i];// 预处理，方便当前弧优化
            ans += dfs(s, t, inf);// 进过多路增广优化可不用循环
        }
        return ans;
    }
    
    void clearflow() { // 清空流
        for (int i = 0; i <= cnt; ++i) e[i].flow = 0;
    }
}dinic;

int main() {
	int n = IO(), m = IO(), s = IO(), t = IO();
    dinic.init(n);
    for (int i = 0; i < m; ++i) {
        int u = IO(), v = IO();
        ll c = IO();
        dinic.add(u, v, c);
    }
    printf("%lld", dinic.dinic(s, t));
    return 0;
}
```

#### 最小费用最大流，将ek算法中的bfs换成spfa

```cpp
inline long long IO() // 快读代码略
const int N = 410, M = 2e5 + 5;
struct edges {
    int to, next;
    ll cap, cost, flow;
    void add(int a, int b, ll c, ll d) {
        to = a, next = b;
        cap = c, cost = d, flow = 0;
    }
};
struct MCMF{
    const ll inf = 1e15;
    int head[N], pre[N], cnt, n, inq[N];
    ll maxflow, mincost, dist[N];
    edges e[M];
    void add(int u, int v, ll cap, ll cost, int f = 1) {
        e[++cnt].add(v, head[u], cap, cost);
        head[u] = cnt;
        if (f) add(v, u, 0, -cost, 0);// 建立反向弧，费用相反
    }
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
    
    void mcmf(int s, int t) {
        maxflow = mincost = 0;
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
    }
    void init(int n) {
        cnt = -1, this->n = n;
        fill_n(head, n + 1, -1);
    }
    void clearflow() {
        for (int i = 0; i <= cnt; ++i) e[i].flow = 0;
    }
}mcmf;

int main() {
    int n = IO(), m = IO();
    mcmf.init(n);
    for (int i = 0; i < m; ++i) {
        int u = IO(), v = IO();
        ll cap = IO(), cost = IO();
        mcmf.add(u, v, cap, cost);
    }
    mcmf.mcmf(1, n);
    printf("%lld %lld", mcmf.maxflow, mcmf.mincost);
    return 0;
}
```

### 5. 二分图

#### 匈牙利算法，时间复杂度$O(ev)$

```cpp
int match[M];
bool vis[M];
bool dfs(int u) {
    for (int &v : gp[u]) {
        if (vis[v]) continue;
        vis[v] = true;
        if (!match[v] || dfs(match[v])) {
            match[u] = v, match[v] = u;
            return true;
        }
    }
    return false;
}
// 主函数里
fill_n(match, n + 1, 0); // n是点的个数
for (int i = 1; i <= n; ++i) {
    if (match[i]) continue;
    fill_n(vis, n + 1, false);
    dfs(i);
}
```


