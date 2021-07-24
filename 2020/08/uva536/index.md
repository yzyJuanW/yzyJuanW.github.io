# UVA536


## [Tree Recovery](https://vjudge.net/problem/UVA-536)

- 给你一个二叉树的先序和中序遍历，输出对应的后序遍历

```c++
#include <bits/stdc++.h>

using namespace std;

char pre[30], in[30];


void dfs(int a, int b, int c, int d) {
    if (c > d) return;
    char tmp = pre[a];
    int indx = c;
    while (in[indx] != tmp) indx++;
    dfs(a + 1, a + indx - c, c, indx - 1);
    dfs(a + indx - c + 1, b, indx + 1, d);
    printf("%c", tmp);
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (~scanf("%s%s", pre, in)) {
        int len = strlen(pre);
        dfs(0, len - 1, 0, len - 1);
        puts("");
    }
    return 0;
}
```

