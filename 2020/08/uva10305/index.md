# UVA10305


## [Ordering Tasks](https://vjudge.net/problem/UVA-10305)

- 题意：给你一堆未知数的大小关系，让你输出可能的总体大小关系
- 简化了拓扑排序，只因根据题中的意思了解到一定是DAG

```c++
#include <bits/stdc++.h>

using namespace std;

const int M = 110;
int m, n, vis[M];
bool g[M][M];
vector<int> ans;

void dfs(int u) {
    vis[u] = true;
    for (int i = 1; i <= m; i++) {
        if (!vis[i] && g[u][i]) {
            dfs(i);
        }
    }
    ans.push_back(u);
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (scanf("%d%d", &m, &n), m + n) {
        int x, y;
        for (int i = 0; i < n; i++) {
            scanf("%d%d", &x, &y);
            g[x][y] = true;
        }
        for (int i = 1; i <= m; i++) {
            if (vis[i]) continue;
            dfs(i);
        }
        reverse(ans.begin(), ans.end());
        for (auto i : ans) printf("%d ", i);
        puts("");
        ans.clear();
        memset(g, 0, sizeof g);
        memset(vis, 0, sizeof vis);
    }
    return 0;
}
```

