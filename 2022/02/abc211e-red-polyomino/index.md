# Abc211E Red Polyomino


题意：在一个n*n的格子中，`.`表示白色，`#`表示黑色，问你把k个白色染成红色并且最后是个连通（所有红色都同属一个连通块）的方案数

一开始想到状压，后来发现单行连通，此行不连通但相邻行能使他们连通的情况不好处理

后来发现可以dfs爆搜，一开始想不到怎么不重不漏地搜出来，用set去重计数，跑了400+ms的速度，很是不满意

后来察觉到，往时搜索立刻回溯的方案太慢了，而且有重复遍历，所有采用最后再全部回溯为原来模样的方式遍历，跑了17ms

可以这么理解，包括该点的走法，和不包括该点的走法， 最后统一回溯，有效防止后面dfs时再走一次

记录一下

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

int n, m;
char g[10][10];
int vis[10][10], ans = 0;
pair<int, int> dxy[] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
vector<pair<int, int>> isp;
void dfs(int num) {
    if (num == 0) return ans += 1, void();
    vector<pair<int, int>> nxt, tmp = isp;
    for (auto [x, y] : tmp) {
        for (auto [dx, dy] : dxy) {
            int nx = x + dx, ny = y + dy;
            if (nx <= 0 || ny <= 0 || nx > n || ny > n) continue;
            if (g[nx][ny] != '.') continue;
            g[nx][ny] = '@';
            isp.push_back({nx, ny});
            nxt.push_back({nx, ny});
            dfs(num - 1);
            g[nx][ny] = '#';
            isp.pop_back();
        }
    }
    for (auto [x, y] : nxt) {
        g[x][y] = '.';
    }
}

int main() {
    scanf("%d %d\n", &n, &m);
    for (int i = 1; i <= n; ++i) {
        scanf("%s\n", g[i] + 1);
    }
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (g[i][j] == '.') {
                g[i][j] = '@', isp.push_back({i, j});
                dfs(m - 1);
                g[i][j] = '#', isp.pop_back();
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
```


