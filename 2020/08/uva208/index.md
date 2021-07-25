# UVA208


## [Firetruck](https://vjudge.net/problem/UVA-208)

- 题意：给你一个无向图，让你求出从1号节点到n号节点的所有路径，按顺序输出，具体看样例
- DFS和BFS都可以，这里我用了DFS，利用了一个ans的vector数组来储存答案，根据题目要求，优先递归节点权值小的
- 注意：要先判断1号节点能否到达n号节点，不然会tle，这里我用了一个类似于并查集的思想来判断是否能到达n号节点
- 代码如下

```c++
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <queue>
#include <vector>

using namespace std;
using vi = vector<int>;

int t, cnt, Kase = 0, nums, fa[25];
bool vis[25], f, num[25], v[25];
vi nodes[25], ans;

void init() {
    cnt = 0, nums = 0;
    for (auto &i : nodes) i.clear();
    ans.clear();
    ans.push_back(1);
    memset(num, 0, sizeof num);
    memset(vis, 0, sizeof vis);
    memset(v, 0, sizeof v);
    vis[1] = true;
    for (int i = 1; i < 25; i++) fa[i] = i;
}

void DFS(int n) {
    if (n == t) {
        cnt++, f = true;
        for (auto i : ans) { if (f) f = !f; else printf(" "); printf("%d", i); }
        puts("");
        return;
    }
    for (auto x : nodes[n]) {
        if (vis[x]) continue;
        vis[x] = true;
        ans.push_back(x);
        DFS(x);
        vis[x] = false;
        ans.pop_back();
    }
}

bool check(int n = 1) {
    for (auto i : nodes[n]) { if(!v[i]) { fa[i] = 1, v[i] = true; check(i); } }
    return fa[t] == 1;
}

int main () {
    while (~scanf("%d", &t)) {
        printf("CASE %d:\n", ++Kase);
        int u, v;
        init();
        while (scanf("%d%d", &u, &v), u + v) {
            nodes[u].push_back(v);
            nodes[v].push_back(u);
        }
        for (auto &i : nodes) sort(i.begin(), i.end());
        if(check()) DFS(1);
        printf("There are %d routes from the firestation to streetcorner %d.\n", cnt, t);
    }
    return 0;
}
```

