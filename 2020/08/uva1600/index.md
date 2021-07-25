# UVA1600


## [Patrol Robot](https://vjudge.net/problem/UVA-1600)

- BFS进阶题
- 题意：有个移动的机器人，从（1， 1）点出发，目标是（m， n）点，0为可以通过的点，1为障碍点，机器人一次最多只能穿越k障碍，问你最少要走多少步到达终点
- BFS走，要注意个是要记录到达障碍点的最短穿越障碍个数

```c++
#include <bits/stdc++.h>

using namespace std;

int n, m, g[25][25], t, k;
const int dx[] = {-1, 0, 1, 0};
const int dy[] = {0, -1, 0, 1};

inline bool check(int x, int y) { return x >= 1 && y >= 1 && x <= n && y <= m; }

struct coo{
    int x, y, d, k;
    coo(){}
    coo(int x, int y, int d, int k) : x(x), y(y), d(d), k(k){}
};

void bfs() {
    queue<coo> q;
    bool vis[25][25][25] = {0};
    vis[1][1][0] = g[1][1] == 1;
    q.push(coo(1, 1, 0, g[1][1] == 1));
    while (q.size()) {
        coo u = q.front();
        q.pop();
        if (vis[u.y][u.x][u.k]) continue;
        vis[u.y][u.x][u.k] = true;
        if (u.x == n && u.y == m) { printf("%d\n", u.d); return; }
        for (int i = 0; i < 4; i++) {
            int x = u.x + dx[i], y = u.y + dy[i];
            if (!check(x, y)) continue;
            if (g[y][x]) {
                if (u.k + 1 > k) continue;//如果穿越障碍个数比之前算的还大就不计入
                q.push(coo(x, y, u.d + 1, u.k + 1));
            } else {
                q.push(coo(x, y, u.d + 1, 0));
            }
        }
    }
    puts("-1");
}

int main () {
    scanf("%d", &t);
    while (t--) {
        scanf("%d%d%d", &m, &n, &k);
        for (int i = 1; i <= m; i++)
            for (int j = 1; j <= n; j++)
                scanf("%d", &g[i][j]);
        bfs();
        memset(g, 0, sizeof g);
    }
    return 0;
}
```

