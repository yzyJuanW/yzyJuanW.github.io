# Tourist's_Notes


## [传送门](https://codeforces.com/problemset/problem/538/C)

- 题意：旅行者每天都会记录自己的海拔高度，他每天规定走海拔相差不超过1的路线，有一天他的记录丢失了，只剩下几天的，问你他之前有可能到达的最高海拔是多少，如果数据出现矛盾，输出"IMPOSSIBLE"
- 思路：用两个数组记录天数和当天的海拔高度，如果相隔的海拔差比相隔的天数差还大的话就是矛盾，否则就这样计算相隔天数中可能的最大海拔高度：$MaxHight = ( day[i] + hight[i] - (day[i - 1] - hight[i - 1]) ) / 2$，最后取众多MaxHight的最大值
- 注意：第一天和最后一天的数据可能没给，所以我们要自己造一个可能的最大高度，例如只给你第3天的海拔高度为5，总共旅行的天数为4，那么我们就自己造一个第一天的高度为7$(5 + 3 - 1)$，第4天的高度为6$(5 + (4 - 3))$，就是一开始没想到这个，wa了2发/(ㄒoㄒ)/~~
- 代码如下：

```c++
#include <algorithm>
#include <cmath>
#include <cstdio>
#include <cstring>

using namespace std;

const int M = 1e5 + 10;
int n, m, d[M], h[M];
bool ok = true;

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        scanf("%d%d", d + i, h + i);
        if (i != 1 && d[i] - d[i - 1] < abs(h[i] - h[i - 1])) {
            ok = false;
        }
    }
    if (ok) {
        h[0] = h[1] + d[1] - 1, h[m + 1] = h[m] + (n - d[m]);
        d[0] = 1, d[m + 1] = n;
        int Max = h[0];
        for (int i = 1; i <= m + 1; i++) {
            int tmp = (d[i] + h[i] - (d[i - 1] - h[i - 1])) >> 1;
            Max = max(Max, tmp);
        }
        printf("%d\n", Max);
    } else {
        printf("IMPOSSIBLE\n");
    }
    return 0;
}
```
