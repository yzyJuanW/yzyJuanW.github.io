# UVA548


## [Tree](https://vjudge.net/problem/UVA-548)

- 给你二叉树的中序和先序遍历，让你求从根节点到叶节点的的距离权值和最小，如果有多解，叶节点的权值要小，输出叶节点的权值

```c++
#include <bits/stdc++.h>

using namespace std;

const int M = 1e4 + 10;
string str;
int ino[M], posto[M], ans, Max, lson[M], rson[M], len = 0;

void dfs(int u, int sum) {
    if (!lson[u] && !rson[u]) {
        if (sum < Max || (sum == Max && ans > u)) {
            ans = u, Max = sum;
        }
    }
    if (lson[u] != 0) dfs(lson[u], sum + lson[u]);
    if (rson[u] != 0) dfs(rson[u], sum + rson[u]);
}

int build(int a, int b, int c, int d) {
    if (c > d) return 0;
    int v = posto[b], indx = c;
    while (ino[indx] != v && indx <= d) indx++;
    int l = indx - c;
    lson[v] = build(a, a + l - 1, c, indx - 1);
    rson[v] = build(a + l, b - 1, indx + 1, d);
    return v;
}

void init(int *A) {
    char *s = &str[0];
    len = 0;
    while (1) {
        sscanf(s, "%d", A + len++);
        if (strchr(s, ' ') == nullptr) return;
        s = strchr(s, ' ') + 1;
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (getline(cin, str)) {
        init(ino);
        getline(cin, str);
        init(posto);
        build(0, len - 1, 0, len - 1);
        ans = Max = 1e5;
        dfs(posto[len - 1], posto[len - 1]);
        printf("%d\n", ans);

        memset(lson, 0, sizeof lson);
        memset(rson, 0, sizeof rson);
    }
    return 0;
}
```

