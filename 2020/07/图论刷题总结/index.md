# 图论刷题总结


## 前言

- 起因是一次力扣周赛时被一道最短论的题给整蒙了，决定这几天恶补图论，正好POJ的刷题题单刷到了图
- 打算学习图论从零开始，目前刷的题不多

## 总结

- 关于存图
  - 不同的存图方式可以使得不同的算法更加方便，例如：邻接表可以使Dijkstra算法在堆优化中方便不少，而邻接矩阵可以使Floyd算法更加方便
- 关于建图
  - 有一些题目在描述的时候给的数据不好建图，这需要经验的积累
  - 而有一些题目并没有直接说明是图，换句话说能否看出是使用图论的知识来解题也需要经验的累积
- 关于计算
  - 题目中的要求并不是一成不变的，有时候最短路径并不是仅仅指的是值最短，有时候也有可能题目让你求最大值，但最短路依然可以解

## 最短路

- 先从最短路开始刷，刷到后面发现有些题三个算法（Dijkstra、Bellman-Ford、Floyd）都是可以解的

### Dijkstra

- [例题]
- [洛谷P4779 【模板】单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)
  - 题意：最短路板子题，不解释
  - 思路：直接用Dijkstra的堆优化做，因为没有负环
  - 直接上代码

  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <locale>
  #include <queue>
  #include <utility>
  #include <vector>

  using namespace std;

  typedef long long ll;
  typedef pair<int, int> pii;
  const int M = 2e5 + 10;
  const ll INF = 0x3f3f3f3f;
  int n, m, s, cnt = 0;

  struct edges {
      int w, to, next;
      edges(int a = 0, int b = 0, int c = -1) : w(b), to(a), next(c) {}
  };
  vector<int> head, dis;
  vector<edges> edge;

  inline void add(int u, int v, int w) {
      edge[++cnt] = edges(v, w, head[u]);
      head[u] = cnt;
  }

  void dij() {
      vector<bool> vis(n + 1, false);
      dis = vector<int>(n + 1, INF);
      priority_queue<pii, vector<pii>, greater<pii> > heap;
      dis[s] = 0;
      heap.push(make_pair(0, s));
      while (heap.size()) {
          int u = heap.top().second;
          heap.pop();
          if (vis[u]) continue;
          vis[u] = true;
          for (int i = head[u]; ~i; i = edge[i].next) {
              int v = edge[i].to, w = edge[i].w;
              if (dis[v] > dis[u] + w) {
                  dis[v] = dis[u] + w;
                  heap.push(make_pair(dis[v], v));
              }
          }
      }
  }

  int main() {
      scanf("%d%d%d", &n, &m, &s);
      head = vector<int>(n + 1, -1);
      edge = vector<edges>(m + 1);
      for (int i = 0; i < m; i++) {
          int u, v, w;
          scanf("%d%d%d", &u, &v, &w);
          add(u, v, w);
      }
      dij();
      for (int i = 1; i <= n; i++) {
          printf("%d ", dis[i]);
      }
      puts("");
      return 0;
  }
  ```
  
- [洛谷P1629 邮递员送信](https://www.luogu.com.cn/problem/P1629)
  - 题意：一个邮递员从一号顶点出发送东西，一次只能送一个，送完就要回到1号节点，求送完后的最短路(注意：有向图的往返)
  - 思路：两次Dijkstra就可以了
  - 代码如下
  
  ```c++
  #include <cstdio>
  #include <cstring>
  #include <queue>
  #include <utility>
  #define mk(a, b) make_pair(a, b)

  using namespace std;

  typedef pair<int, int> pii;

  const int INF = 0x3f3f3f3f;
  int n, m, cnt = 0;
  vector<int> dis, ddis, head, head2;

  struct edges {
      int to, w, next;
      edges(int a = 0, int b = 0, int c = -1) : to(a), w(b), next(c) {}
  };
  vector<edges> edge, bk;

  void init() {
      head = vector<int>(n + 1, -1);
      head2 = vector<int>(n + 1, -1);
      dis = vector<int>(n + 1, INF);
      ddis = vector<int>(n + 1, INF);
      edge = vector<edges>(m + 1);
      bk = vector<edges>(m + 1);
  }

  void add(int u, int v, int w) {
      edge[++cnt] = edges(v, w, head[u]);
      bk[cnt] = edges(u, w, head2[v]);
      head[u] = cnt;
      head2[v] = cnt;
  }

  void dij() {
      priority_queue<pii> heap;
      vector<bool> vis(n + 1, false);
      heap.push(mk(0, 1));
      while (heap.size()) {
          int u = heap.top().second;
          heap.pop();
          if (vis[u]) continue;
          vis[u] = true;
          for (int i = head[u]; ~i; i = edge[i].next) {
              int v = edge[i].to, w = edge[i].w;
              if (dis[v] > dis[u] + w) {
                  dis[v] = dis[u] + w;
                  heap.push(mk(-dis[v], v));
              }
          }
      }
  }
  void dij2() {
      priority_queue<pii> heap;
      vector<bool> vis(n + 1, false);
      heap.push(mk(0, 1));
      while (heap.size()) {
          int u = heap.top().second;
          heap.pop();
          if (vis[u]) continue;
          vis[u] = true;
          for (int i = head2[u]; ~i; i = bk[i].next) {
              int v = bk[i].to, w = bk[i].w;
              if (ddis[v] > ddis[u] + w) {
                  ddis[v] = ddis[u] + w;
                  heap.push(mk(-ddis[v], v));
              }
          }
      }
  }

  int main() {
      scanf("%d%d", &n, &m);
      init();
      for (int i = 0; i < m; i++) {
          int u, v, w;
          scanf("%d%d%d", &u, &v, &w);
          add(u, v, w);
      }
      dis[1] = ddis[1] = 0;
      dij();
      dij2();
      int ans = 0;
      for (int i = 1; i <= n; i++) {
          ans += dis[i] + ddis[i];
      }
      printf("%d", ans);
      return 0;
  }
  ```
  
- [洛谷P1144 最短路计数](https://www.luogu.com.cn/problem/P1144)
  - 题意：无向图，每条边的权值都为 1， 求从一号顶点出发到其他顶点的最短路的个数
  - 思路：用个$ans$数组储存最短路的个数，Dijkstra内加个计数条件，如果有松弛操作则$ans$继承上一个节点最短路的个数，如果没用松弛操作，就直接让$ans$加上一节点的$ans$（注：ans[1]初始化为1， 其余都初始化为0）
  - 代码如下

  ```c++
  #include <bits/stdc++.h>

  using namespace std;
  typedef pair<int, int> pii;
  const int M = 1e6 + 5;
  int n, m, dis[M], head[M], ans[M], cnt = 0;

  struct es {
      int to, next;
      es(int a = 0, int b = -1) : to(a), next(b) {}
  } e[M << 2];

  void add(int a, int b) {
      e[++cnt] = es(b, head[a]);
      head[a] = cnt;
  }

  void dij() {
      for (int i = 2; i <= n; i++) {
          dis[i] = 0x3f3f3f3f;
      }
      ans[1] = 1;
      vector<bool> vis(n + 1, false);
      priority_queue<pii> heap;
      heap.push(make_pair(0, 1));
      while (heap.size()) {
          int u = heap.top().second;
          heap.pop();
          if (vis[u]) continue;
          vis[u] = true;
          for (int i = head[u]; ~i; i = e[i].next) {
              int v = e[i].to;
              if (dis[v] > dis[u] + 1) {//松弛操作
                  dis[v] = dis[u] + 1;
                  ans[v] = ans[u];
                  heap.push(make_pair(-dis[v], v));
              } else if (dis[u] + 1 == dis[v]) {
                  ans[v] += ans[u];
                  ans[v] %= 100003;
              }
          }
      }
  }

  int main() {
      scanf("%d%d", &n, &m);
      memset(head, -1, sizeof head);
      for (int i = 0; i < m; i++) {
          int u, v;
          scanf("%d%d", &u, &v);
          add(u, v);
          add(v, u);
      }
      dij();
      for (int i = 1; i <= n; i++) {
          if (ans[i] >= 100003) ans[i] = 0;
          printf("%d\n", ans[i]);
      }
      return 0;
  }
  ```

### Bellman-Ford

- 这个算法更多的不是用来计算最短路，而是用来计算是否有负环或者正环（负环dis初始化为INF，正环dis初始化为0）
- 【例题】
- [EOlymp - 1453 Ford-Bellman](https://vjudge.net/problem/EOlymp-1453)
  - 题意：给你一个有向图，有负权边，保证没有负环，求1号顶点到各个点的最短距离，如果无法到达顶点，距离输出30000
  - 思路：就是一个很纯正的Bellman - Ford的板子题
  - 直接上代码
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>

  using namespace std;

  const int INF = 0x3f3f3f3f;
  int n, m;

  vector<int> dis;
  struct edges {
      int u, v, w;
      edges(int u = 0, int v = 0, int w = 0) : u(u), v(v), w(w) {}
  };
  vector<edges> e;

  int add(int a, int b) { //无穷大加任何数还是无穷大
      if (a == INF || b == INF) return a;
      return a + b;
  }
  void bf() {
      bool flag;
      while (true) {
          flag = true;
          for (int j = 0; j < m; j++) {
              if (dis[e[j].v] > add(dis[e[j].u], e[j].w)) {
                  dis[e[j].v] = dis[e[j].u] + e[j].w;
                  flag = false;
              }
          }
          if (flag) return;
      }
  }

  int main() {
      scanf("%d%d", &n, &m);
      dis = vector<int>(n + 1, INF);
      for (int i = 0; i < m; i++) {
          int u, v, w;
          scanf("%d%d%d", &u, &v, &w);
          e.push_back(edges(u, v, w));
      }
      dis[1] = 0;
      bf();
      for (int i = 1; i <= n; i++) {
          if (dis[i] == INF) dis[i] = 30000;
          printf("%d ", dis[i]);
      }
      puts("");
      return 0;
  }
  ```

- [POJ 2240 Arbitrage](http://poj.org/problem?id=2240)
  - 题意：给你一个有向图，判断是否有正环
  - 思路：把字符串的顶点用hash来代替，然后就是标准的Bellman - Ford模板题了,正环判断dis初始化为0
  - 代码如下

  ```c++
  #include <cstring>
  #include <iostream>
  #include <map>
  #include <string>
  #include <vector>

  using namespace std;

  int n, m, test = 0;
  double dis[100];
  string str;
  struct es {
      int u, v;
      double w;
      es(int a = 0, int b = 0, double c = 0) : u(a), v(b), w(c) {}
  };
  vector<es> e;

  bool bf() {
      for (int i = 1; i <= n - 1; i++) {
          for (int j = 0; j < m; j++) {
              if (dis[e[j].v] < dis[e[j].u] * e[j].w) {
                  dis[e[j].v] = dis[e[j].u] * e[j].w;
              }
          }
      }
      for (int i = 0; i < m; i++) {
          if (dis[e[i].v] < dis[e[i].u] * e[i].w) {
              return true;
          }
      }
      return false;
  }
  int main() {
      map<string, int> hsh;
      while (cin >> n, n) {
          for (int i = 1; i <= n; i++) {
              cin >> str;
              hsh[str] = i;
          }
          cin >> m;
          e = vector<es>(m);
          for (int i = 0; i < m; i++) {
              string a, b;
              double w;
              cin >> a >> w >> b;
              e[i] = es(hsh[a], hsh[b], w);
          }
          memset(dis, 0, sizeof dis);
          dis[1] = 1;
          if (bf()) {
              cout << "Case " << ++test << ": Yes" << endl;
          } else {
              cout << "Case " << ++test << ": No" << endl;
          }
      }
      return 0;
  }
  ```
  
- [POJ 3259 Wormholes](http://poj.org/problem?id=3259)
  
  - 题意：有两种边，一种是双向边，一种是单线负权边，问你是否有负环
  - 思路：双向边和负权边都用邻接表存起来，然后就是普通的Bellman - Ford了
- 代码如下
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>

  using namespace std;

  const int M = 3e3;
  int n, m, ww, f, dis[510];
  
  struct es {
      int u, v, w;
      es(int a = 0, int b = 0, int c = 0) : u(a), v(b), w(c) {}
  };
  vector<es> e;
  
  bool bf() {
      for (int i = 1; i <= n; i++) {
          bool flag = true;
          for (int j = 0; j < m + m + ww; j++) {
              if (dis[e[j].v] > dis[e[j].u] + e[j].w) {
                  dis[e[j].v] = dis[e[j].u] + e[j].w;
                  flag = false;
              }
          }
          if (flag) break;
          if (i == n) return true;//有负环，因为松弛了 n 次
      }
      return false;
  }
  
  int main() {
      scanf("%d", &f);
      while (f--) {
          scanf("%d%d%d", &n, &m, &ww);
          int u, v, w;
          for (int i = 0; i < m; i++) {
              scanf("%d%d%d", &u, &v, &w);
              e.push_back(es(u, v, w));
              e.push_back(es(v, u, w));
          }
          for (int i = 0; i < ww; i++) {
              scanf("%d%d%d", &u, &v, &w);
              e.push_back(es(u, v, -w));
          }
          memset(dis, 0x3f, sizeof dis);
          dis[1] = 0;
          if (bf()) puts("YES");  
          else puts("NO");
          e.clear();
      }
      return 0;
  }
  ```
  
- [POJ 1860 Currency Exchange](http://poj.org/problem?id=1860)
  
  - 题意：给你一个有向图，你手中有v元的a货币，每个顶点都是一个货币兑换点，每个兑换点都有自己的兑换费用和兑换比例，并且兑换是双向的，例如 a → b, b → a，但兑换比率和兑换费用不一定一样，由测试样例给，问你从某点出发，回到该点时，即兑换会原本的货币种类，他手中的钱能否变多
  - 思路：判断正环，唯一要注意的就是初始化  $ dis[s] = V $，其余初始化为0
- 代码如下
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  using namespace std;
  const int M = 300;
  int n, m, s;
  double vv, dis[M] = {0}; //vv是一开始手中的钱的数量
  
  struct es {
      int u, v;
      double r, c;
      es(int a = 0, int b = 0, double c = 0, double d = 0) : u(a), v(b), r(c), c(d) {}
  } e[M];
  
  bool bf() {
      for (int i = 1; i <= n - 1; i++) {
          for (int j = 0; j < m + m; j++) {
              if (dis[e[j].v] < (dis[e[j].u] - e[j].c) * e[j].r) {
                  dis[e[j].v] = (dis[e[j].u] - e[j].c) * e[j].r;
              }
          }
      }
      for (int j = 0; j < m + m; j++) {
          if (dis[e[j].v] < (dis[e[j].u] - e[j].c) * e[j].r) {
              return true; //手中的钱变多了
          }
      }
      return false;//手中的钱没变或者少了
  }
  
  int main() {
      scanf("%d%d%d%lf", &n, &m, &s, &vv);
      for (int i = 0; i < m; i++) {
          int u, v;
          double a, b, c, d;
          scanf("%d%d%lf%lf%lf%lf", &u, &v, &a, &b, &c, &d);
          e[i << 1] = es(u, v, a, b);         //一个兑换点有从 a -> b,
          e[i << 1 | 1] = es(v, u, c, d);     //也有从 b -> a
      }
    dis[s] = vv;
  
    if (bf()) puts("YES");
    else puts("NO");
  
    return 0;
  }
  ```

### Floyd

- [POJ 2253 Frogger](http://poj.org/problem?id=2253)
  - 题意：一直青蛙想通过石头跳到另一只青蛙那里，但是他的跳跃能力是有封顶的，即他的能力是有限的，最多能跳1米的话，绝对跳不到1.1米远的石头上，给你石头的坐标，和两只青蛙的坐标，问你青蛙的最远能跳的封顶距离的最小值是多少
  - 思路：一个最短路的变形题，用Floyd比较好想（其他方法也可），转换成人话就是让你求从 a 点到 b 点的路径中，最小边权的最大值是多少，有点绕，自己草稿纸画个图就明白了
  - 代码如下
  
  ```c++
  #include <algorithm>
  #include <cmath>
  #include <cstdio>
  #include <cstring>
  #include <vector>
  
  using namespace std;
  
  const int M = 2e2 + 10;
  int n, x[M], y[M], test = 0;
  double dis[M][M];
  
  void Floyd() {
      for (int k = 1; k <= n; k++) {
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j <= n; j++) {
                  if (dis[i][j] > max(dis[i][k], dis[k][j])) {
                      dis[i][j] = max(dis[i][k], dis[k][j]);
                  }
                  // dis[i][j] = min(dis[i][j], max(dis[i][k], dis[k][j]));//更清晰的代码
              }
          }
      }
  }
  
  int main() {
      while (scanf("%d", &n), n) {
          scanf("%d%d%d%d", &x[1], &y[1], &x[2], &y[2]);
          double a = x[2] - x[1], b = y[2] - y[1];
          dis[1][2] = sqrt(a * a + b * b);
          for (int i = 3; i <= n; i++) {
              scanf("%d%d", &x[i], &y[i]);
              for (int j = 1; j < i; j++) {
                  a = x[i] - x[j], b = y[i] - y[j];
                  dis[i][j] = sqrt(a * a + b * b);
                  dis[j][i] = dis[i][j];
              }
          }
          Floyd();
          printf("Scenario #%d\nFrog Distance = %.3f\n\n", ++test, dis[1][2]);
          memset(dis, 0, sizeof dis);
      }
  
      return 0;
  }
  ```
  
- [POJ 1125 Stockbroker Grapevine](http://poj.org/problem?id=1125)
  - 题意：给你一个有向图，问你是否能从一点出发到达所有其他顶点，如果有输出该点编号并输出到达这些顶点的最短路中的最大值，否则输出"disjoint"
  - 思路：显然Floyd算法，在Floyd完了之后遍历每个最短路，看看取最短路的最大值和其起点，如果这个最大值是无穷大则说明没有一个顶点可以到达其他顶点
  - 代码如下

  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <iostream>
  #include <vector>
  
  using namespace std;
  
  const int M = 110;
  int n, tm[M][M], dis[M][M];
  
  void floyd() {
      for (int k = 1; k <= n; k++) {
          for (int i = 1; i <= n; i++) {
              for (int j = 1; j <= n; j++) {
                  dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
              }
          }
      }
  }
  
  int main() {
  #ifndef ONLINE_JUDGE
      freopen("test.in", "r", stdin);
      freopen("test.out", "w", stdout);
  #endif
      while (scanf("%d", &n), n) {
          memset(dis, 0x3f, sizeof dis);
          memset(tm, 0x3f, sizeof tm);
          for (int i = 1; i <= n; i++) {
              int m;
              scanf("%d", &m);
              for (int j = 0; j < m; j++) {
                  int x, y;
                  scanf("%d%d", &x, &y);
                  dis[i][x] = y;
              }
          }
          floyd();
          int ans = -1, Min = 0x3f3f3f3f;
          for (int i = 1; i <= n; i++) {
              int Max = 0;
              for (int j = 1; j <= n; j++) {
                  if (i != j && dis[i][j] > Max) {
                      Max = dis[i][j];
                  }
              }
              if (Max < Min) {
                  ans = i;
                  Min = Max;
              }
          }
          if (Min == 0x3f3f3f3f) {
              puts("disjoint");
              continue;
          }
          printf("%d %d\n", ans, Min);
      }
  
      return 0;
  }
  ```

### Spfa

- 一种速度极快的最短路算法，但是听说被一位大佬卡死了，但是在一些题目上还是能用的，下面利用这个算法来解决上面的一些题

- 存图的方式最好是邻接表法，这样比较方便松弛

- 其实道理都差不多，题目的不同，主要修改的就是松弛操作部分的代码

- 【例题】

- [POJ 3259 Wormholes](http://poj.org/problem?id=3259)

  - 上面的Bellman - Ford算法的例题，除了存图的方式不同，松弛的操作还是不变
  - 直接上代码


```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <queue>
#include <vector>

using namespace std;

const int M = 2e4;
int n, m, ww, f, dis[510], head[510], cnt = 0;

struct es {
    int to, w, next;
    es(int a = 0, int b = 0, int c = -1) : to(a), w(b), next(c) {}
};

vector<es> e;

void add(int u, int v, int w) {
    e[++cnt] = es(v, w, head[u]);
    head[u] = cnt;
}

bool spfa() {
    queue<int> q;
    vector<bool> inq(n + 1);
    vector<int> nums(n + 1, 0);
    q.push(1);
    inq[1] = true;
    nums[1]++;
    while (q.size()) {
        int u = q.front();
        q.pop();
        inq[u] = false;
        for (int i = head[u]; ~i; i = e[i].next) {
            int v = e[i].to, w = e[i].w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                if (!inq[v]) {
                    q.push(v);
                    inq[v] = true;
                    nums[v]++;
                    if (nums[v] >= n)
                        return true;
                }
            }
        }
    }
    return false;
}

int main() {
    scanf("%d", &f);
    while (f--) {
        cnt = 0;
        memset(head, -1, sizeof head);
        scanf("%d%d%d", &n, &m, &ww);
        e = vector<es>(m + m + ww + 1);
        int u, v, w;
        for (int i = 0; i < m; i++) {
            scanf("%d%d%d", &u, &v, &w);
            add(u, v, w);
            add(v, u, w);
        }
        for (int i = 0; i < ww; i++) {
            scanf("%d%d%d", &u, &v, &w);
            add(u, v, -w);
        }
        memset(dis, 0x3f, sizeof dis);
        dis[1] = 0;
        if (spfa()) puts("YES");
        else puts("NO");

        e.clear();
    }
    return 0;
}
```



## 未完待续


