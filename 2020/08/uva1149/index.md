# UVA1149


## [Bin Packing](https://vjudge.net/problem/UVA-1149)

- 题意：给你n个物品，每个物品的重量为$w_i$，你有容量为w的背包，每个背包最多只能装两件物品，问你最少要多少背包来装
- 贪心就好了，贪心策略：将物品按重量排序，如果最重和最轻的物品一起不超过w就放一起，然后就让次重和次轻的放一起；否则重的单独放在一个背包，然后让次重的背包和最轻的一起，以此循环
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int M = 1e5 + 5;
int n, w, wi[M];

int main () {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    int t;
    scanf("%d", &t);
    while (t--) {
        scanf("%d%d", &n, &w);
        for (int i = 0; i < n; i++) scanf("%d", wi + i);
        sort(wi, wi + n);
        int l = 0, r = n - 1, ans = 0;
        while (l <= r) {
            if (wi[l] + wi[r] <= w) l++, r--;
            else r--;
            ans++;
        }
        if (t) printf("%d\n\n", ans);
        else printf("%d\n", ans);
    }
    return 0;
}
```

