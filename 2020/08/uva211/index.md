# UVA211


## [The Domino Effect](https://vjudge.net/problem/UVA-211)

- 题意：一个多米诺骨牌有两个面，一共有28个不同的多米诺骨牌，放在那，可能横着，可能竖着，这样就形成了一个$7 \times 8$的一个数字图，问你所有可能的摆放方式
- dfs就好了，一开始没想到怎么dfs，绞尽脑汁，一开始一格一格来，失败，后来采用每一行都用一个二进制状态来描述是横还是竖，还是失败了，最后看了下题解，豁然开朗
- 也是dfs，但是不能再单纯地一格一格得dfs，要一行一行来，不然可能会像我一开始那样，会遗漏很多格子没递归到，所以要一行一行来，方式就是每行一格一格地递归，递归到最后一列的才换行（代码24行处），最终将28个牌都遍历一遍才能输出
- 至于骨牌号码的储存方式，考虑到数据小，所以用了压缩来储存起骨牌的号码
- 代码如下

```c++
#include <cstring>
#include <cstdio>
#include <algorithm>

using namespace std;

int domi[10][10], id[400], ans[10][10], nums = 0, kase = 0;
bool vis[30];
const int dr[] = {1, 0}, dc[] = {0, 1};
inline int ID(int a, int b) { return (min(a, b) << 6) | max(a, b); }
inline bool check(int r, int c) { return r < 7 && c < 8; }
void init(){ memset(ans, 0, sizeof ans); memset(vis, 0, sizeof vis); nums = 0; }

void dfs(int r, int c, int num) {
    if (num == 28) {
        nums++;
        for (int i = 0; i < 7; i++) {
            for (int j = 0; j < 8; j++) { printf("%4d", ans[i][j]); }
            puts("");
        }
        puts("\n");
        return;
    }
    if (c == 8) r++, c = 0;
    if (ans[r][c]) { dfs(r, c + 1, num); return; }
    for (int i = 0; i < 2; i++) {
        int newr = r + dr[i], newc = c + dc[i];
        int x = ID(domi[r][c], domi[newr][newc]), res = id[x];
        if (!check(newr, newc) || vis[res] || ans[newr][newc]) continue;
        ans[r][c] = ans[newr][newc] = res, vis[res] = true;
        dfs(r, c + 1, num + 1);
        ans[r][c] = ans[newr][newc] = 0, vis[res] = false;

    }
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    int cnt = 0;
    bool ok = true, f = true;
    for (int i = 0; i <= 6; i++) {
        for (int j = i; j <= 6; j++) { id[ID(i, j)] = ++cnt; }
    }
    while (1) {
        for (int i = 0; i < 7 && ok; i++) {
            for (int j = 0; j < 8; j++) {
                if (!~scanf("%d", &domi[i][j])) { ok = false; break; }
            }
        }
        if (!ok) break;
        if (f) f = !f;
        else puts("\n\n\n\n");
        printf("Layout #%d:\n\n", ++kase);
        for (int i = 0; i < 7; i++) {
            puts("");
            for (int j = 0; j < 8; j++) printf("%4d", domi[i][j]);
        }
        printf("\n\nMaps resulting from layout #%d are:\n\n\n", kase);
        init();
        dfs(0, 0, 0);
        printf("There are %d solution(s) for layout #%d.\n", nums, kase);

    }
    return 0;
}
```

