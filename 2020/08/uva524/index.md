# UVA524


## [Prime Ring Problem](https://vjudge.net/problem/UVA-524)

- 题意：给你一个数n，让你求出所有素数环，素数环：利用 1 ~ n来排列组合成的一个环，相邻的两个数的和必须都是素数
- 直接dfs遍历所有排列，途中要剪枝，如果当前放的数和前面的数的和不是素数就直接剪了
- 画出解答树更好懂
- 代码如下

```c++
#include <cstdio>
#include <cstring>

using namespace std;

int kase = 0, n, arr[30];
bool isp[] = {0, 0, 1, 1, 0, 1, 0, 1, 0, 0, 0,
 1, 0, 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0,
 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0,
  1, 0, 0, 0, 1, 0, 0};
bool vis[30];

void dfs(int cur, int *A) {
    if (cur == n - 1) {
        if (!isp[1 + A[n - 1]]) return;
        bool f = true;
        for (int i = 0; i < n; i++) {
            if (f) f = !f;
            else printf(" ");
            printf("%d", A[i]);
        }
        puts("");
        return;
    }
    for (int i = 2; i <= n; i++) {
        if (vis[i]) continue;
        if (isp[A[cur] + i]) {
            A[cur + 1] = i;
            vis[i] = true;
            dfs(cur + 1, A);
            vis[i] = false;
        }
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    arr[0] = 1;
    bool f = true;
    while (~scanf("%d", &n)) {
        if (f) f = !f;
        else puts("");
        printf("Case %d:\n", ++kase);
        dfs(0, arr);
        memset(vis, 0, sizeof vis);
    }
    return 0;
}
```

