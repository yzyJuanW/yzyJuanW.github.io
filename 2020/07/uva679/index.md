# UVA679


## [Dropping Balls](https://vjudge.net/problem/UVA-679)

- 题意：小球在一个二叉树上下落，每个节点都有一个开关，起初全是关的，如果节点是开小球往右走，反之往左走，让你求最后一个球落到了哪个节点
- 如果直接单纯的模拟是会超时的，观察可以发现一个节点的第奇数个球都会落到左子树，第偶数个球都落到了右边，自己草稿模拟一下就好了
- 代码如下

```c++
#include <bits/stdc++.h>

using namespace std;

int d, n, t;

int main() {
    scanf("%d", &t);
    while (t--) {
        scanf("%d%d", &d, &n);
        int ans = 1;
        for (int i = 1; i < d; i++) {
            if (n & 1) {
                ans <<= 1;
                n = (n + 1) >> 1;
            } else {
                ans = ans << 1 | 1;
                n >>= 1;
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

