# UVA712


## [S-Trees](https://vjudge.net/problem/UVA-712)

- 题意：一棵二叉树，每个层节点都有有个逻辑布尔变量，0往左走，1往右走，给你最后一层的结果值，再给你每个变量的取值，问你最后走到的最后一层叶子节点的值
- 这题有点迷，一开始忘记记录变量的顺序尽然也过了，后来记录了也能过
- 不考虑变量的顺序

```c++
#include <bits/stdc++.h>

using namespace std;

int n, k, kase = 0;;
char res[600], in[10];

void dfs(int indx, int x) {
    if (indx == n) {
        printf("%c", res[x - (1 << n)]);
        return;
    }
    if (in[indx] == '0') dfs(indx + 1, x << 1);
    else dfs(indx + 1, x << 1 | 1);
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (scanf("%d", &n), n) {
        char tmp[10];
        for (int i = 0; i < n; i++) {
            scanf("%s", tmp);
        }
        scanf("%s", res);
        printf("S-Tree #%d:\n", ++kase);
        scanf("%d", &k);
        while (k--) {
            scanf("%s", in);
            dfs(0, 1);
        }
        puts("\n");
    }
    return 0;
}
```

- 考虑变量的顺序

```c++
#include <bits/stdc++.h>

using namespace std;

int n, k, num[10], kase = 0;;
char res[600], in[10];

void dfs(int indx, int x) {
    if (indx == n) {
        printf("%c", res[x - (1 << n)]);
        return;
    }
    if (in[num[indx]] == '0') dfs(indx + 1, x << 1);
    else dfs(indx + 1, x << 1 | 1);
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (scanf("%d", &n), n) {
        char tmp[10];
        for (int i = 0; i < n; i++) {
            scanf("%s", tmp);
            num[i] = tmp[1] - '0' - 1;
        }
        scanf("%s", res);
        printf("S-Tree #%d:\n", ++kase);
        scanf("%d", &k);
        while (k--) {
            scanf("%s", in);
            dfs(0, 1);
        }
        puts("\n");
    }
    return 0;
}
```

