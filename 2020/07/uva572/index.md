# UVA572


## [Oil Deposits](https://vjudge.net/problem/UVA-572)

- 给你一个矩形图，让你求连通的“@”块，一个连通的定义是该点的八个方向都是连通的，dfs就行了

```c++
#include <bits/stdc++.h>

using namespace std;

int m, n, cont[110][110];
char g[110][110];

void dfs(int x, int y, int num) {
    if (x < 0 || y < 0 || x >= m || y >= n || cont[x][y] || g[x][y] != '@') return;
    cont[x][y] = num;
    for (int i = -1; i <= 1; i++) {
        for (int j = -1; j <= 1; j++) {
            if (i != 0 || j != 0) dfs(x + i, y + j, num);
        }
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (scanf("%d%d", &m, &n), m && n) {
        for (int i = 0; i < m; i++) {
            scanf("%s", g[i]);
        }
        int num = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (!cont[i][j] && g[i][j] == '@') {
                    dfs(i, j, ++num);
                }
            }
        }
        printf("%d\n", num);
        memset(cont, 0, sizeof cont);
    }
    return 0;
}
```

