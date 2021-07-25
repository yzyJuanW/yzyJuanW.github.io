# UVA12563


## [Jin Ge Jin Qu hao](https://vjudge.net/problem/UVA-12563)

- 大致题意：在KTV里，如果还剩下1秒的时间，则可以点一首更长的歌，因为他会播放完最后一首歌才停止，现在给你剩下的时间$m$和$n(n \le 50)$首歌，每首歌的时长$t_1, t_2, t_3 ……t_n$，现让你算出在剩余的时间内能唱的歌的最大数量，然后利用空出来的时间最后再点一首长为678秒的歌，输出能唱的最大数量，对应的时间
- 简单的分析：
  - 题目中说最后会点一首678秒的歌来延长时间，则策略就是计算在不超过$m - 1$时间里选最多的歌
  - 读一读上面的一句话，是不是感觉很想01背包？是的就是01背包问题，每个物品的价值默认是1了（一首歌嘛）
  - 题目中说$m \le 10^9$，其实并没有这么大，~~我一开始也很苦恼这怎么建数组，~~后来发现其实并没有这么大，他说n + 1首歌的时长严格大于剩余的时长，并且每一首歌不会超过3分钟，这么算的话$180 \times 50 + 678 = 9678$，固数组完全够开
  - 值得注意的是这题计算在选歌的数量多的前提下最后尽量晚地结束KTV，这就需要讨论了，*一开始被这个搞得十分地晕，冷静下来后发现一个if是不够的，于是就特判3次*
    - 首先如果当前抉择的歌曲数量还没之前算的多，直接跳过
    - 如果当前抉择的歌曲数量严格比之前算的还多，则更新歌曲数量和时间总长度
    - 如果当前抉择的歌曲数量和之前算的一样多，则还要判断当前抉择的时长是严格比上次算还多，则更新时间总长度
  - 具体看代码

```c++
#include <cstring>
#include <cstdio>
#include <algorithm>

using namespace std;

int n, t, dp[10010], m, kase = 0, sum[10010];

void solve() {
    scanf("%d%d", &n, &m);
    memset(dp, 0, sizeof dp);
    memset(sum, 0, sizeof sum);
    for (int i = 0; i < n; i++) {
        scanf("%d", &t);
        for (int j = m; j > t; j--) {
            if (dp[j] > dp[j - t] + 1) continue;//特判1
            if (dp[j] < dp[j - t] + 1) {        //特判2
                dp[j] = dp[j - t] + 1;
                sum[j] = sum[j - t] + t;
            } else if (sum[j] < sum[j - t] + t){//特判3
                sum[j] = sum[j - t] + t;
            }
        }
    }
    printf("Case %d: %d %d\n", ++kase, dp[m] + 1, sum[m] + 678);
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        solve();
    }
    return 0;
}
```

