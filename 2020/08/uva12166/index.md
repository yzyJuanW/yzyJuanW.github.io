# UVA12166


## [Equilibrium Mobile](https://vjudge.net/problem/UVA-12166)

- 题意：给你一个天平，修改某些节点可以使得天平平衡，让你求出最小的修改数量
- 竟然是一道思维题，不行，这题要写写题解
- 思路：
  - 假设这个数是一个满二叉树的，并且每个叶子节点每个叶子节点的重量（权值）都不一样，则最少的修改次数就是：叶子节点数 - 1，具体策略就是找一个叶子节点为基准点，设其重量为$x$，然后把其余的叶子节点的重量都修改成$x$，设数的深度为$h$，则此时整棵树的的重量为 $x \times 2 ^ {h - 1}$
  - 对于其他情况，我们可以根据上述的特殊情况来推导，假设以某个叶子节点为基准点，然后计算修改后整棵树的重量（根据公式 $x \times 2 ^ {h - 1}$）
  - 所以策略就是，计算每个叶子节点以其为基准点修改后的整棵树的重量，这样计算下去，可能会出现计算结果相同的情况，就意味着以该叶子节点为基准点修改时，有不需要修改的叶子节点
  - 那么我们就可以统计以每个叶子节点为基准点修改后的整棵树的重量的值，出现最多次数的就说明以该叶子节点为基准点修改，有最多的叶子节点不需要修改，固需要修改的叶子节点个数最少
  - 剩下的就是代码实现了，由于重量可能很大，固用哈希来存放该重量的数量
- 代码如下

```c++
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

int t, num;
char str[1000005];
unordered_map<ll, int> myhash;

void dfs(int l, int r, ll d) {
    if (str[l] == '[') {
        int l_indx = l + 1, p = 0;
        while (l_indx < r) {
            if (str[l_indx] == '[') p++;
            if (str[l_indx] == ']') p--;
            if (p == 0) break;
            l_indx++;
        }
        int r_indx = strchr(&str[l_indx], ',') - &str[l_indx] + l_indx;
        dfs(l + 1, l_indx, d + 1);
        dfs(r_indx + 1, r - 1, d + 1);
        return;
    }
    ll w = 0;
    sscanf(&str[l], "%lld", &w);
    myhash[w * (1 << d)]++;
    num++;
}

int main () {
    scanf("%d", &t);
    while (t--) {
        myhash.clear();
        num = 0;
        scanf("%s", str);
        int len = strlen(str), ans = -1;
        dfs(0, len - 1, 0);
        for (auto i : myhash) ans = max(ans, i.second);
        printf("%d\n", num - ans);
    }
    return 0;
}
```

