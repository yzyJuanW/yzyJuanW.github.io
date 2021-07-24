# UVA1354


## [Mobile Computing](https://vjudge.net/problem/UVA-1354)

- 题意：有一个房间，有长为1的木棍，用天平的方式放一些物品，让你求出不超过房间的宽度的最长宽度的，如果误解输出-1
- 做了两天，习得了两种dfs非枚举子集的办法（到时再回头研究刘神方法），只选择了一种学习，当然另一种也看了
- 自底向上枚举二叉树，思想有点像哈夫曼建树，选择两个节点来合并，然后就是递归回溯的事
- dfs(n) ：代表还剩下 n 个节点可以拼接
- w[i] ：代表第 i 个节点的重量，w[i] = 0 则表示该节点已经被用了
- l[i]、r[i] ：代表第 i 节点的左右宽度的最大值
- 值得注意的是在求 l[i] 和 r[i] 时同时要考虑当前另一个节点，例如在计算 l[i] 时，不仅要考虑 **左子树的左最大宽度** + **当前节点的左宽度**，还要考虑 **右子树的左最大宽度** - **当前节点的右宽度**，前者很好理解，关键是后者：因为有可能右子树的左最大宽度会超过左子树
- 具体看图，右子树同理

![UVA1354](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPICUVA1354.png)

- 显然橙色节点的距离超过了黄色节点
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;
using vi = vector<int>;

double d, Max, l[10], r[10];
int n, cnt, k = 0;
vi w;

void dfs(int num) {
    if (num == 1) {
        for (int i = 0; i < n; i++) {
            if (!w[i]) continue;
            if (l[i] + r[i] > d) continue;
            Max = max(Max, l[i] + r[i]);
        }
        return;
    }
    for (int i = 0; i < n; i++) {
        if (!w[i]) continue;
        for (int j = 0; j < n; j++) {
            if (!w[j] || i == j) continue;
            int neww = w[i] + w[j], tmp1 = w[i], tmp2 = w[j];
            double newl = (w[j] * 1.0) / (w[i] + w[j]), newr = 1 - newl;
            w[i] = neww, w[j] = 0;
            double tmpl = l[i], tmpr = r[i];
            l[i] = max(newl + l[i], l[j] - newr);
            r[i] = max(newr + r[j], r[i] - newl);
            cnt--;

            dfs(num - 1);
            //回溯
            cnt++;
            w[i] = tmp1, w[j] = tmp2;
            l[i] = tmpl, r[i] = tmpr;

        }
    }
}

void solve() {
    memset(l, 0, sizeof l);
    memset(r, 0, sizeof r);
    scanf("%lf%d", &d, &n);
    Max = -1, cnt = n;
    w = vi(n);
    for (auto &i : w) scanf("%d", &i);
    if (n == 1) { puts("0"); return; }
    dfs(n);
    printf("%0.10f\n", Max);
}


int main () {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    int T;
    scanf("%d", &T);
    while(T--) solve();
    return 0;
}
```

