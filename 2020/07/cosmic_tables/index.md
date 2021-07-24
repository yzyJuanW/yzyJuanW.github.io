# Cosmic_Tables


## [传送门](https://codeforces.com/contest/222/problem/B)

- 题意：给你一个矩阵，每次进行与行之间的交换，或者是列与列之间的交换，或者查询(x, y)点的值
- 思路：一开始想着是不是直接就纯模拟算了，后来想想，直接用数组把行和列也记录下来不就好了吗
- 代码如下

```c++
#include <cstdio>

using namespace std;

const int M = 1e3 + 10;
int r[M], c[M], g[M][M], n, m, k;

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1; i <= n; i++) r[i] = i;
    for (int i = 1; i <= m; i++) c[i] = i;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            scanf("%d", &g[i][j]);
        }
    }
    while (k--) {
        char a;
        int x, y;
        scanf("\n%c %d %d", &a, &x, &y);
        if (a == 'r') {//交换操作
            r[x] ^= r[y], r[y] ^= r[x], r[x] ^= r[y];
        } else if (a == 'c') {
            c[x] ^= c[y], c[y] ^= c[x], c[x] ^= c[y];
        } else {
            printf("%d\n", g[r[x]][c[y]]);
        }
    }
    return 0;
}
```

