# UVA12113


## [Overlapping Squares](https://vjudge.net/problem/UVA-12113)

- 题意：给你个$4 \times 4$的网格图，问你有没有可能用不超过6张$2 \times 2$的纸叠成给的样子
- 一开始想破脑袋都想不出来应该怎么验证是否相同，没办法还是问了下度娘，知道思路后自己写了代码ac了
- 想不出的是如何验证正确性，一开始想着是否要把被遮住的部分补全然后验证，但这样又怎么验证？看了看别人的思路发现自己是真的蠢，直接模拟用$2 \times 2$的纸一张一张地放上去不就好了吗
- dfs最多也就6层，纸放下去只有9种可能，能想到这里就可以直接dfs模拟了
- ac代码如下

```c++
#include <cstdio>
#include <algorithm>
#include <cstring>

using namespace std;

char g[10][20], p[10][20];
bool vis[10];

bool init() {
    for (int i = 0; i < 5; i++) {
        fgets(g[i], 15, stdin);
        if (g[i][0] == '0') return false;
    }
    memset(p, ' ', sizeof p);
    memset(vis, 0, sizeof vis);
    return true;
}

bool check() {
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 9; j++) if (p[i][j] != g[i][j]) return false;
    }
    return true;
}

bool dfs(int num) {
    if (check()) return true;
    if (num == 6) return false;
    char tmp[10][20];
    memcpy(tmp, p, sizeof p);
    for (int i = 0; i < 9; i++) {
        if (vis[i]) continue;
        vis[i] = true;
        int r = i / 3, c = (i % 3) * 2;
        p[r + 1][c] = p[r + 2][c] = p[r + 1][c + 4] = p[r + 2][c + 4] = '|';
        p[r][c + 1] = p[r + 2][c + 1] = p[r][c + 3] = p[r + 2][c + 3] = '_';
        for (int j = 1; j < 4; j++) p[r + 1][c + j] = ' ';
        p[r + 2][c + 2] = ' ';
        if (dfs(num + 1)) return true; 
        vis[i] = false;
        memcpy(p, tmp, sizeof p);//回溯
    }
    return false;
}

int main () {
    int kase = 0;
    while(init()) {
        printf("Case %d: ", ++kase);
        if (dfs(0)) puts("Yes");
        else puts("No");
    }
    return 0;
}

```

