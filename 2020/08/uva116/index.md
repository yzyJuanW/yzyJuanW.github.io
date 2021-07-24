# UVA116


## [Unidirectional TSP](https://vjudge.net/problem/UVA-116)

- 大致题意：有个$m \times n$矩阵，对于每个点你可以向直接向右、右上，右下走，第1行的上一行是第m行，第m行的下一行是第1行，问你从第一列的某一行出发，到达最后一列所经历的点的和最小是多少，并且打印出每一列的行号，如果有多解，输出字典序最小的
- 简单的分析：
  - 可以看出是一个数塔问题的变种
  - 书中说这种问题叫做**多阶段决策问题**中的一类——**多阶段图**的最短路问题，所谓多阶段图按书中说法就是图中结点可以划分成若干个阶段
  - 这里我的理解就是原数塔问题也可以说是一个DAG，而这题相当于多个数塔重合的版本，固每一列都有对应着不用的阶段
  - 计算最短路就是简单的数塔解法了
  - 真正让人头疼的是最小字典序这里，一开始我的做法是写一大堆if判断，最后对比了一下刘老师的代码，妙不可言，用了大小为3的数组存下一列的决策行数
  - 代码如下

  ```c++
  #include <cstring>
  #include <cstdio>
  #include <algorithm>

  using namespace std;

  const int INF = 0x3f3f3f3f;
  int m, n, g[15][110], ans[15][110], Min, dp[15][110], f;
  bool vis[15][110];

  int DP(int r, int c) {
      if (c == n) return 0;
      if (vis[r][c]) return dp[r][c];
      int &res = dp[r][c], M = INF, &i = ans[r][c + 1], row[] = {r - 1, r, r + 1};
      vis[r][c] = true, res = g[r][c];
      if (r == 0) row[0] = m - 1;
      if (r == m - 1) row[2] = 0;
      sort(row, row + 3);
      for (int j = 0; j < 3; j++)
          if (M > DP(row[j], c + 1)) M = DP(row[j], c + 1), i = row[j];
      res += M;
      return res;
  }

  int main() {
  #ifndef ONLINE_JUDGE
      freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
      freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
  #endif
      while (~scanf("%d%d", &m, &n)) {
          for (int i = 0; i < m; i++)
              for (int j = 0; j < n; j++)
                  scanf("%d", &g[i][j]);
          Min = INF;
          memset(vis, 0, sizeof vis);
          for (int i = 0; i < m; i++){
              int tmp = DP(i, 0);
              if (Min > tmp) Min = tmp, f = i;
          }
          printf("%d", f + 1);
          for (int i = 1, j = ans[f][i]; i < n; j = ans[j][++i])
              printf(" %d", j + 1);
          printf("\n%d\n", Min);
      }
      return 0;
  }
  ```


