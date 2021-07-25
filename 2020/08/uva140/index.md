# UVA140


## [Bandwidth](https://vjudge.net/problem/UVA-140)

- 题意：给你一个无向图，让你输出一种序列使得每个节点到该节点在图中相连的节点的最远距离的最小值，直接看题目比较好懂
- dfs遍历所有可能的序列，然后求出一个最小值，我的代码用了邻接表
- 剪枝的话一种是利用计算好最小带宽的一种来剪枝，第二种就是计算未确定位置的节点的最小带宽来剪
- 按照紫书的指导，剪了两种枝，但是感觉剪一种的速度差不多，两者交上去都是$0ms$，毕竟节点不多，剪一种枝就行了
- 以下代码是剪了两种枝的，如果只剪一种，只要把28，60，62注释就行了
  
```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;

char str[200];
bool ext[26], ok[26][26];
int head[26], n, indx[26], vis[26], mind, con[26];
struct es{
    int to, next;
    es(int to = 0, int next = -1) : to (to), next(next) {}
};

vector<es> e;
vector<char> node, res, ans;

void add_edge(int u, int v) {
    e.push_back(es(v, head[u]));
    head[u] = e.size() - 1, con[u]++;
    if (!ext[u]) node.push_back(u + 'A');
    ext[u] = true;
}

bool check(int cur, int u) {
    if (!cur) return false;
    if (con[u] > mind) return true;
    for (int i = head[u]; ~i; i = e[i].next) {
        int v = e[i].to;
        if (!vis[v]) continue;
        if (cur - indx[v] >= mind) return true;
    }
    return false;
}

void left(int u, int x = -1) {
    for (int i = head[u]; ~i; i = e[i].next) {
        con[e[i].to] += x;
    }
}
void dfs(size_t cur) {
    if (cur == node.size()) {
        int m = 0;
        for (int i = cur - 1; i >= 0; i--) {
            int u = res[i] - 'A';
            for (int j = head[u]; ~j; j = e[j].next) {
                int v = e[j].to;
                m = max(m, abs(i - indx[v]));
            }
        }
        if (m < mind) { mind = m; ans = res; }
        return;
    }
    for (size_t i = 0; i < node.size(); i++) {
        int u = node[i] - 'A';
        if (vis[u]) continue;
        if (check(cur, u)) continue;
        res[cur] = node[i], indx[u] = cur, vis[u] = true;
        left(u);
        dfs(cur + 1);
        left(u, 1);
        vis[u] = false;
    }
}

void init() {
    memset(head, -1, sizeof head);
    e.clear(), ans.clear(), res.clear(), node.clear();
    memset(vis, 0, sizeof vis);
    memset(con, 0, sizeof con);
    memset(ext, 0, sizeof ext);
    memset(ok, 0, sizeof ok);
    mind = 100;
}

int main () {
    while (scanf("%s", str), str[0] != '#') {
        init();
        char *tmp = str;
        for (;;) {
            int u = tmp[0] - 'A';
            int len = strlen(tmp);
            for (int i = 2; tmp[i] != ';' && i < len; i++) {
                int v = tmp[i] - 'A';
                if (ok[u][v]) continue;
                add_edge(u, v);
                add_edge(v, u);
                ok[u][v] = ok[v][u] = true;
            }
            if (strchr(tmp, ';') == nullptr ) break;
            tmp = strchr(tmp, ';') + 1;
        }
        sort(node.begin(), node.end());
        res = vector<char>(node.size());
        dfs(0);
        for (auto i : ans) printf("%c ", i);
        printf("-> %d\n", mind);
    }
    return 0;
}
```

