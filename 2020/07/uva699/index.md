# UVA699


## [The Falling Leaves](https://vjudge.net/problem/UVA-699)

- 二叉树的计算，直接递归读入遍历即可
- 所有数据要以空行相隔，UVA特色

```c++
#include <bits/stdc++.h>

using namespace std;

const int M = 5e5 + 5;
int node[M], t = 0;

void build(int mid) {
    int v;
    scanf("%d", &v);
    if (v == -1) return;
    node[mid] += v;
    build(mid - 1);
    build(mid + 1);
}

bool read() {
    int v, mid = M >> 1;
    scanf("%d", &v);
    if (v == -1) return false;
    memset(node, 0, sizeof node);
    node[mid] = v;
    build(mid - 1);
    build(mid + 1);
    return true;
}

int main() {
    while (read()) {
        int indx = 0, f = 1;
        while (!node[indx]) indx++;
        printf("Case %d:\n", ++t);
        for (; node[indx] != 0; indx++) {
            if (f) f = 0;
            else printf(" ");
            printf("%d", node[indx]);
        }
        puts("\n");
    }
    return 0;
}
```

