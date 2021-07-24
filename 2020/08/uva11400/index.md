# UVA11400


## [Lighting System Design](https://vjudge.net/problem/UVA-11400)

- 题意：你要设计一个一共需要**n种不同电压灯泡**的照明系统，不同种类的灯泡用不同的电源，同一种灯泡必须要用同一个电源，现在为了省钱，在亮度不减的前提下，可以用电压高的灯泡来换电压低的灯泡减小费用，现给你每种灯泡的电压V，电源费用K，每个灯泡的费用C，每种灯泡所需要的数量L，问你计算出最优的方案的费用
- 思路：
  - 为了减少电压之间的比较可以对灯泡进行从小到大的排序，这样的话就可以默认是下标大的换下标小的
  - 注意到对于一种灯泡，要不就全换掉，要不就全部都不换，不可能说最优的方案是当前这种灯泡只换一部分
  - 设dp[i]表示当决策到第i种灯泡时的最优方案
  - 将sum[i]表示前i种灯泡需要的总数量，相当于一个前缀和，则转移方程为
  $$
  dp[i] = max\{dp[j] + (sum[i] - sum[j]) * c[i] + k[i]\}
  $$
  - 解释：相当于是dp[i]的最优方案是第j + 1 ~ i种的灯泡都用第i种灯泡，然后再加上前j种灯泡中的最优方案，即dp[j]
  - 因为每种灯泡的电压都是不一样的，所以这个方程的转移不用担心出现两种的灯泡的使用电压是一样但是电源或者灯泡的单价不一样的情况
  - 再来就是第i种的灯泡在将前面的灯泡替换时一定是连续的，即j + 1 ~ i种是连续的不会出现断续的情况，因为如果出现断续的k（j + 1 ~ k - 1 和 k + 1 ~ i）的情况，则第k种灯泡替换成j + 1 ~ k的方案一定会比替换成i要优
  - 代码如下

  ```c++
  #include <cstdio>
  #include <cstring>
  #include <algorithm>

  using namespace std;

  int n, dp[1010], sum[1010];
  struct lamps{
      int v, k ,c, l;
      lamps(){}
      lamps(int v, int k, int c, int l) : v(v), k(k), c(c), l(l) {}
  }lamp[1010];

  int main() {
  #ifndef ONLINE_JUDGE
      freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
      freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
  #endif
      while (scanf("%d", &n), n) {
          for (int i = 1; i <= n; i++)
              scanf("%d%d%d%d", &lamp[i].v, &lamp[i].k, &lamp[i].c, &lamp[i].l);
          memset(dp, 0x3f, sizeof dp);
          sum[0] = 0, dp[0] = 0;
          sort(lamp + 1, lamp + n + 1, [](lamps a, lamps b) { return a.v < b.v; });
          for (int i = 1; i <= n; i++) { sum[i] = lamp[i].l + sum[i - 1]; }
          for (int i = 1; i <= n; i++) {
              for (int j = 0; j <= i; j++) {
                  dp[i] = min(dp[i], dp[j] + lamp[i].c * (sum[i] - sum[j]) + lamp[i].k);
              }
          }
          printf("%d\n", dp[n]);
      }
      return 0;
  }
  ```

