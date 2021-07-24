# UVA122


## [Trees on the level](https://vjudge.net/problem/UVA-122)

- 二叉树的建立

```c++
#include <bits/stdc++.h>

using namespace std;

const int M = 1e5 + 10;

int lson[M], rson[M], cnt, node[M];
bool ok, in[M] = {false};//in记录节点是否存在
vector<int> ans;

int newnode() {//申请新的地址
    int u = ++cnt;
    lson[u] = rson[u] = 0;
    return u;
}
void newtree() {//建新树
    lson[1] = rson[1] = 0;
    in[1] = false;
    cnt = 1;
}

void build(int v, char *s) {
    int indx = 0, u = 1;
    while (s[indx] != ')') {
        if (s[indx] == 'L') {
            if (!lson[u]) lson[u] = newnode();
            u = lson[u];
        } else {
            if (!rson[u]) rson[u] = newnode();
            u = rson[u];
        }
        indx++;
    }
    if (in[u]) ok = true;
    node[u] = v;
    in[u] = true;
}
void init() {
    memset(lson, 0, sizeof lson);
    memset(rson, 0, sizeof rson);
    memset(in, 0, sizeof in);
    memset(node, 0, sizeof node);
}
bool read() {
    char str[100];
    init();
    newtree();
    while (scanf("%s", str) != EOF) {
        if (strcmp("()", str) == 0) return true;
        int node;
        sscanf(&str[1], "%d", &node);
        build(node, strchr(str, ',') + 1);
    }
    return false;
}

void dfs() {
    queue<int> q;
    q.push(1);
    while (q.size()) {
        int u = q.front();
        q.pop();
        if (!in[u]) { ok = true; return; }
        ans.push_back(node[u]);
        if (lson[u] != 0) q.push(lson[u]);
        if (rson[u] != 0) q.push(rson[u]);
    }
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (read()) {
        dfs();
        if (ok) puts("not complete");
        else {
            bool f = true;
            for (auto i : ans) {
                if (f) f = false;
                else printf(" ");
                printf("%d", i);
            }
            puts("");
        }
        ans.clear();
        ok = false;
    }
    return 0;
}
```

- 总结（收获）：在一般情况下利用指针建树和新建节点的原理就是申请新的地址内存（指针），但是如果这样的话在释放内存的时候会很不方便。所以这里我利用了变量模拟地址，即代码中的cnt，cnt = x代表申请了一个地址是x的内存（模拟的），这样的好处就是方便建树和建立新的节点，也没有因为难以释放指针内存的后顾之忧了。

