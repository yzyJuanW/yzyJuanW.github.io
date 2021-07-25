# UVA816


## [Abbott's Revenge](https://vjudge.net/problem/UVA-816)

- 题意：给你起点和终点，每个点面向不同的方向有不用的指定方向，问你从起点到终点最短路
- BFS就好，但是要注意的是每个点都有不用的方向，所以要多加一维来保存这些方向，即每个点相当于是4个点
- 一道值得学习深究的一题

```c++
#include <bits/stdc++.h>

using namespace std;

const char dis[] = "NESW", go[] = "RLF";
const int dx[] = { -1, 0, 1, 0 }, dy[] = { 0, 1, 0, -1 };

int sx, sy, rx, ry, ex, ey, rd, d[15][15][4];
bool ok[15][15][4][3];

inline int get_dis(char c) { return strchr(dis, c) - dis; }
inline int get_go(char c) { return strchr(go, c) - go; }
inline bool cherk(int x, int y) { return x > 0 && x <= 9 && y > 0 && y <= 9; }

struct nodes{
    int x, y, d;
    nodes(int x = 0, int y = 0, int d = 0) : x(x), y(y), d(d) {}
}pre[15][15][4];

nodes work(nodes &node, int i) {
    int dr = node.d;
    if (i == 0) dr = (dr + 1) % 4;
    if (i == 1) dr = (dr + 3) % 4;
    return nodes(node.x + dx[dr], node.y + dy[dr], dr);
}

void print(nodes last) {
    vector<nodes> ans;
    while (1){
        ans.push_back(last);
        if (d[last.x][last.y][last.d] == 1) break;
        last = pre[last.x][last.y][last.d];
    }
    ans.push_back(nodes(sx, sy, 0));
    reverse(ans.begin(), ans.end());
    int k = 0;
    printf(" ");
    for (auto i : ans) {
        if (k++ && k % 10 == 1) printf("\n ");
        printf(" (%d,%d)", i.x, i.y);
    }
    puts("");
}

void bfs() {
    queue<nodes> q;
    q.push(nodes(rx, ry, rd));
    d[rx][ry][rd] = 1;
    while (q.size()) {
        nodes node = q.front();
        q.pop();
        int x = node.x, y = node.y, dir = node.d;
        if (x == ex && y == ey) {
            print(node);
            return;
        }
        for (int i = 0; i < 3; i++) {
            if (!ok[x][y][dir][i]) continue;
            nodes nxt = work(node, i);
            if (!cherk(nxt.x, nxt.y) || d[nxt.x][nxt.y][nxt.d] != 0) continue;
            d[nxt.x][nxt.y][nxt.d] = d[x][y][dir] + 1;
            pre[nxt.x][nxt.y][nxt.d] = node;
            q.push(nxt);
        }
    }
    puts("  No Solution Possible");
}

void init() {
    memset(ok, 0, sizeof ok);
    memset(d, 0, sizeof d);
}

int main() {
    char str[100], w, ch[100];
    int x, y;
    while (1) {
        scanf("%s", str);
        if (strcmp(str, "END") == 0) break;
        puts(str);
        scanf("%d%d %c%d%d", &sx, &sy, &w, &ex, &ey);
        rd = get_dis(w);
        rx = sx + dx[rd], ry = sy + dy[rd];
        while (scanf("%d", &x), x) {
            scanf("%d", &y);
            while (scanf("%s", ch), ch[0] != '*') {
                int len = strlen(ch);
                for (int i = 1; i < len; i++) {
                    ok[x][y][get_dis(ch[0])][get_go(ch[i])] = true;
                }
            }
        }
        bfs();
        init();
    }
    return 0;
}
```

