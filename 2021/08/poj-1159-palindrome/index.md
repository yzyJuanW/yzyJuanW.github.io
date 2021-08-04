# POJ 1159 Palindrome


- 题意：给你一个长为n的字符串，问你最少插入多少个字符可以使得这个字符串成为一个回文字符串

- 很容易想到直接找最长的回文子序列便可

- 设 `dp[l][r]` 为区间 `[l, r]` 的字符串中最长的回文子序列

- 则转移方程

  - 若 `s[l] != s[r]`则  `dp[l][r] = max(dp[l + 1][r], dp[l][r - 1])`

  - 否则   `dp[l][r] = max({dp[l + 1][r], dp[l][r - 1], dp[l + 1][r - 1] + 2})`
  - 初始化 `dp[i][i] = 1`

- 最后的答案便是 `n - dp[1][n]`

- 但这样空间要开到 `5000 * 5000` ，交上去后直接 MLE 了

- 观察到转移方程，发现可以空间优化，第一维只需开长度为2便可，然后滚动数组

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 5e3 + 5;
char s[N];
int dp[2][N];

int main() {
    int n;
    while (scanf("%d\n%s", &n, s + 1) != -1) {
        int now = 0, pre = 1;
        for (int i = n; i >= 1; --i) {
            dp[now][i] = 1;
            for (int j = i + 1; j <= n; ++j) {
                dp[now][j] = max(dp[pre][j], dp[now][j - 1]);
                if (s[i] == s[j]) dp[now][j] = max(dp[now][j], dp[pre][j - 1] + 2);
            }
            swap(now, pre);
        }
        printf("%d\n", n - dp[pre][n]);
    }
    return 0;
}
```


