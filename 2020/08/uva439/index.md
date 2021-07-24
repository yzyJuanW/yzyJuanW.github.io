# UVA439


## [Knight Moves](https://vjudge.net/problem/UVA-439)

- 题意：在象棋里，给马的起点和终点，计算最小移动步数

```c++
#include <bits/stdc++.h>
#define mk(a, b) make_pair(a, b)
using namespace std;
using pii = pair<int, int>;
using pdi = pair<pii, int>;

int sx, sy, ex, ey;
bool vis[10][10];
char a[5], b[5];
const int dx[] = {-2, -1, 1, 2, 2, 1, -1, -2};
const int dy[] = {-1, -2, -2, -1, 1, 2, 2, 1};

inline bool cherk(int x, int y) { return x > 0 && y > 0 && x < 9 && y < 9; }

void print(int num) {
    printf("To get from %s to %s takes %d knight moves.\n", a, b, num);
}

void bfs() {
    queue<pdi> q;
    q.push(mk(mk(sx, sy), 0));
    vis[sx][sy] = true;
    while (q.size()) {
        int x = q.front().first.first, y = q.front().first.second;
        int num = q.front().second;
        q.pop();
        if (x == ex && y == ey) { print(num); return;}
        for (int i = 0; i < 8; i++) {
            int l = x + dx[i], r = y + dy[i];
            if (cherk(l, r) && !vis[l][r]) {
                q.push(mk(mk(l, r), num + 1));
                vis[x][y] = true;
            }
        }
    }
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (~scanf("%s %s", a, b)) {
        sx = a[0] - 'a' + 1, sy = a[1] - '0';
        ex = b[0] - 'a' + 1, ey = b[1] - '0';
        bfs();
        memset(vis, 0, sizeof vis);
    }

    return 0;
}
```

