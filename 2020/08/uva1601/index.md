# UVA1601


## [The Morning after Halloween](https://vjudge.net/problem/UVA-1601)

- 题意：给你一个图，"#"为障碍物，" "为通路，图里面最多有3个小写字母（a, b, c）和于其对应的大写字母（A, B, C），每次移动每个小写字母都有4个方向和不动5种方案，一次移动可以有多个小写字母一起移动，不能一个空格点不能同时有多个小写字母，问你最少要移动多少次才能让这些小写字母移动到对应的大写字母上
- 受紫书的启发，了解到了一个规矩图或者迷宫亦或是题目给的不是图（例如上次的倒水题），也能够利用“状态”来简化成一个平时我们更多看到的点对点的图，看来是几个能够相互连接产生关系的状态，就能考虑利用状态图来解
- 这题的“状态”是对每个坐标的压缩，压缩成一个一维的标志，看起来就好像是给每家每户上一个门牌号一样，因为坐标最大是（16, 16），也就是说最多有256个“门牌号”，这对于一个图来说还是有点多，幸运的是题中说每 $2 \times 2$ 的格子至少有一个可走的点，这样我们只要考虑可以走的点就行了
- 按出现顺序给可走的点编上“门牌号”，并记录上字母的门牌号
- 还要记录每个点能到哪些点，这样就能当成一个有向图来看了
- 如果题目给的鬼的数量不到3个，那可以假装他给了，为没给的字母强行加个门牌号，并且他们已经到了自己的目的地，即相应的大小写字母在同一个位置
- 以下代码是单向的BFS, vj上测得是930ms

```c++
#include <cstdio>
#include <algorithm>
#include <queue>
#include <cstring>
#include <vector>
#include <cctype>

using namespace std;
const int M = 210, f = 0xff;

char g[M][M];
int w, n, h, s[3], t[3], id[20][20], dir[M][M][M];
const int dx[] = {-1, 0, 1, 0, 0};
const int dy[] = {0, -1, 0, 1, 0};

inline bool check(int x, int y) { return x >= 0 && y >= 0 && x <= w && y <= h; }
inline int ID(int a, int b, int c) { return (a << 16) | (b << 8) | c; }//状态压缩
inline bool move(int a, int b, int ago, int bgo) {//检车移动是否合法，即不能一步交换位置，不能到同一个位置
     return (a == bgo && b == ago) || ago == bgo;
}
struct pos{ int x, y, n, go[5]; } p[200];

void BFS() {
    memset(dir, -1, sizeof dir);
    queue<int> q;
    q.push(ID(s[0], s[1], s[2]));
    dir[s[0]][s[1]][s[2]] = 0;
    while (q.size()) {
        int u = q.front();
        q.pop();
        int a = u >> 16, b = (u >> 8) & f, c = u & f;
        if (a == t[0] && b == t[1] && c == t[2]) return;
        // printf("()");
        for (int i = 0; i < p[a].n; i++) {
            int ago = p[a].go[i];
            for (int j = 0; j < p[b].n; j++) {
                int bgo = p[b].go[j];
                if (move(a, b, ago, bgo)) continue;
                for (int k = 0; k < p[c].n; k++) {
                    int cgo = p[c].go[k];
                    if (move(a, c, ago ,cgo))continue;
                    if (move(b, c, bgo, cgo))continue;
                    if (dir[ago][bgo][cgo] >= 0) continue;
                    dir[ago][bgo][cgo] = dir[a][b][c] + 1;
                    q.push(ID(ago, bgo, cgo));
                }
            }
        }
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (scanf("%d%d%d", &w, &h, &n), w + h + n) {
        int cnt = 0;
        for (int i = 0; i < h; i++) {
            getchar();
            for (int j = 0; j < w; j++) {
                char c = g[i][j] = getchar();
                if (c == '#') continue;
                p[cnt].x = j, p[cnt].y = i, id[i][j] = cnt;
                if (islower(c)) s[c - 'a'] = cnt;
                if (isupper(c)) t[c - 'A'] = cnt;
                cnt++;
            }
        }
        for (int i = 0; i < cnt; i++) {
            p[i].n = 0;
            for (int j = 0; j < 5; j++) {
                int nowx = p[i].x + dx[j], nowy = p[i].y + dy[j];
                if (g[nowy][nowx] == '#' || !check(nowx, nowy)) continue;
                p[i].go[p[i].n++] = id[nowy][nowx];
            }
        }
        if (n <= 2) p[cnt].n = 1, p[cnt].go[0] = cnt, s[2] = t[2] = cnt++;//假装给满了3个鬼
        if (n <= 1) p[cnt].n = 1, p[cnt].go[0] = cnt, s[1] = t[1] = cnt++;
        BFS();
        printf("%d\n", dir[t[0]][t[1]][t[2]]);
        memset(p, 0, sizeof p);
    }
    return 0;
}
```

- 以下代码是双向的bfs，测得是590ms

```c++
#include <cstdio>
#include <algorithm>
#include <queue>
#include <cstring>
#include <vector>
#include <cctype>

using namespace std;
const int M = 210, f = 0xff;

char g[M][M];
int w, n, h, s[3], t[3], id[20][20], dir[M][M][M][2], ans;
const int dx[] = {-1, 0, 1, 0, 0};
const int dy[] = {0, -1, 0, 1, 0};

inline bool check(int x, int y) { return x >= 0 && y >= 0 && x <= w && y <= h; }
inline int ID(int a, int b, int c) { return (a << 16) | (b << 8) | c; }
inline bool move(int a, int b, int ago, int bgo) {
     return (a == bgo && b == ago) || ago == bgo;
}
struct pos{ int x, y, n, go[5]; } p[200];

int BFS(queue<int> &q, int st) {
    int u = q.front();
    q.pop();
    int a = u >> 16, b = (u >> 8) & f, c = u & f;
    if (dir[a][b][c][1 - st] != -1 && dir[a][b][c][st] != -1) {//如果碰头了就是答案了
        return dir[a][b][c][1 - st] + dir[a][b][c][st];
    }
    for (int i = 0; i < p[a].n; i++) {
        int ago = p[a].go[i];
        for (int j = 0; j < p[b].n; j++) {
            int bgo = p[b].go[j];
            if (move(a, b, ago, bgo)) continue;
            for (int k = 0; k < p[c].n; k++) {
                int cgo = p[c].go[k];
                if (move(a, c, ago ,cgo))continue;
                if (move(b, c, bgo, cgo))continue;
                if (dir[ago][bgo][cgo][st] >= 0) continue;
                dir[ago][bgo][cgo][st] = dir[a][b][c][st] + 1;
                q.push(ID(ago, bgo, cgo));
            }
        }
    }
    return -1;
}

void double_bfs() {
    queue<int> q[2];
    memset(dir, -1, sizeof dir);
    q[0].push(ID(s[0], s[1], s[2]));
    q[1].push(ID(t[0], t[1], t[2]));
    dir[s[0]][s[1]][s[2]][0] = 0;
    dir[t[0]][t[1]][t[2]][1] = 0;
    while (1) {//起点和终点各跑一次
        ans = BFS(q[0], 0);
        if (ans != -1) return;
        ans = BFS(q[1], 1);
        if (ans != -1) return;
    }
}


int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (scanf("%d%d%d", &w, &h, &n), w + h + n) {
        int cnt = 0;
        for (int i = 0; i < h; i++) {
            getchar();
            for (int j = 0; j < w; j++) {
                char c = g[i][j] = getchar();
                if (c == '#') continue;
                p[cnt].x = j, p[cnt].y = i, id[i][j] = cnt;
                if (islower(c)) s[c - 'a'] = cnt;
                if (isupper(c)) t[c - 'A'] = cnt;
                cnt++;
            }
        }
        for (int i = 0; i < cnt; i++) {
            p[i].n = 0;
            for (int j = 0; j < 5; j++) {
                int nowx = p[i].x + dx[j], nowy = p[i].y + dy[j];
                if (g[nowy][nowx] == '#' || !check(nowx, nowy)) continue;
                p[i].go[p[i].n++] = id[nowy][nowx];
            }
        }
        if (n <= 2) p[cnt].n = 1, p[cnt].go[0] = cnt, s[2] = t[2] = cnt++;
        if (n <= 1) p[cnt].n = 1, p[cnt].go[0] = cnt, s[1] = t[1] = cnt++;
        double_bfs();
        printf("%d\n", ans);
        memset(p, 0, sizeof p);
    }
    return 0;
}
```

