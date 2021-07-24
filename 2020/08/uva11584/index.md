# UVA11584


## [Partitioning by Palindromes](https://vjudge.net/problem/UVA-11584)

- 题意：给你一个字符串，让你求分割成最少回文串的数量，比如racecar最少就是1，fastcar就是7，aaadbccb就是3
- 思路：
  - 说实话，不放到动态规划的专题我可能都不知道怎么做
  - 提供一种比较好想的思路：记一个长度为$l$的字符串最大的分割数量就是$l$，如果说第$l$个字符和第$l - 1$个字符拼成的字符串是回文串，则分割数量就可以减少，即将$l - 1$ ~ $l$的字符串看成一个字符，固分割数减一，以此类推往前找能够看成单个字符的回文串来求出长度为$l$的字符串的最小的分割数
  - 设dp[i]为从0~i的字符串的最小分割数，当j + 1 ~ i是回文串时，其分割数就为dp[j] + 1，设isp[i][j]表示字符串下标从i~j是否为回文串，状态转移方程为
  $$
  dp[i] = min\{dp[j] + 1\},\ \ isp[j + 1][i] = true
  $$
  - 代码如下

  ```c++
  #include <cstdio>
  #include <cstring>
  #include <algorithm>

  using namespace std;

  int dp[1005], len;
  bool isp[1005][1005];
  char str[1005];

  void check() {
      len = strlen(&str[1]) + 1;
      for (int i = 1; i < len; i++) { isp[i][i] = true; }
      for (int i = 1; i + 1 < len; i++) { isp[i][i + 1] = (str[i] == str[i + 1]); }
      for (int i = 2; i < len; i++) {
          for (int j = 1; j + i < len; j++) {
              if (str[j] == str[j + i] && isp[j + 1][j + i - 1]) 
                  isp[j][j + i] = true;
          }
      }
  }

  void solve(){
      memset(isp, 0, sizeof isp);
      memset(dp, 0x3f, sizeof dp);
      scanf("%s", &str[1]);
      check();
      dp[0] = 0;
      for (int i = 1; i < len; i++) {
          for (int j = i; j > 0; j--) {
              if(!isp[j][i]) continue;
              dp[i] = min(dp[i], dp[j - 1] + 1);
          }
      }
      printf("%d\n", dp[len - 1]);
  }

  int main() {
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

