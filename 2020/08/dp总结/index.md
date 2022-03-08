# 


## 1. 写在前面的话

- 之前写了一篇不像总结的[动态规划总结](https://haofish.github.io/2020/dynamicprogramming/)，感觉更像是一个成长历程，所以就打算重写一篇。

## 2. 对DP简单的总结

1. dp的题目特点：
   - 求最大或者最小值（如背包：价值最大、凑硬币：数量最少……）
   - 计数（如：爬楼梯：计算上到n台阶的方法数）
   - 求存在性或者胜负（如：能否胜利……）
2. dp的使用**条件**
   - **拥有子问题**，子问题最优解（即拥有**最优子结构**），对于一个原问题解最优，其子问题必定也是最优，同时原问题的最优解依赖于其子问题的最优解
   - **子问题重复性**，一个子问题可能会影响多个不同的下一阶段的原问题
   - **无后效性**，即此时的之前状态无法直接影响未来的决策，换句话说就是之前的每个状态如何得来并不影响未来对此时（当前）状态的利用或者查找，因为我们最后对此时（当前）状态的利用只考虑结果不考虑过程。
3. dp的**思考方式**及注意事项
   - 若问题有dp的味道，应当优先从主问题出发来思考，即从**末尾（结果）开始思考**（例如爬楼梯问题）
   - 接下来，对于一个主问题，应当思考此问题的结果**由什么得来（由什么决定、怎么决策等）和有什么因素影响**
   - 或者思考此问题类似什么DP题（如:背包、LIS……）
   - 实在想不出可以思考如果是用dfs（或普通递归），应当如何解决问题（个人觉得有时候道理是相同的，记得吗：**递归 + 记忆化 = 递推**）
   - 注意1：在思考一个状态的得来时，目光应当只集中在此时的状态（无后效性），而不要多想之前的状态变化和未来的状态影响
   - 注意2：状态的定义很重要，要结合题目需求和状态影响因素来定义
4. 关于**dp三步走**
   - 1.**状态定义** → 2.**列状态转移方程** → 3.**验证方程**
   - 对于第一步和第二步主要可以利用上述的思考方式或者是**闫氏DP分析法**来解决
   - 第三步虽然不难，但是很重要，因为第三步包括：验证状态推理是否合理或者是否是答案最优，并且思考状态是否满足题目条件需求，前者均没问题后再思考边界是什么。对于一般验证发现有问题通常的解决办法有：1、修改状态定义；2、给状态增加维度；3、优化转移方程
5. 补充的话
   - 读了紫书上的动规篇才开始补这篇博客的坑
   - 学到了很多东西，包括用**记忆化搜索的优势**、**刷表法**、**DAG模型**等等
   - 所以有些代码写的不是递推的方式是为了练习记忆化式的递归
   - 阅读了紫书动规篇后彻底抛弃了递推就一定比记忆化递归还快的观念，也使我再一次对自己当年总结的dp思考方式（**递推 = 递归 + 记忆化**）感到肯定
   - 先来说说**记忆化的优势**：
     - ①**便于思考**，有时完全都不用所谓的状态转移方程，只需要考虑当选状态下如何选择下一步的策略来进行码代码，在比赛的时候这个优势是非常大的
     - ②有些题无法用递推的方式来写，甚至状态都可能是无限的，这时候记忆化的优势将会进一步地放大
     - ③**用记忆化不一定就比递推慢**，仔细思考一下，有时候有些状态是不用计算的，而记忆化在这方面会比全部计算出来的递推要好，固速度不必递推差
   - **记忆化的技巧**
     - **多开一个数组**来做记忆化的操作比将状态数组定义成特殊值来判定是否又有算过好一些，不仅能够增强可读性，还更方便调试操作
     - 如果状态做记忆化用另开数组的方式还是麻烦，不妨试试**用map来存被算过的状态**
     - **用引用来调用状态数组可以简化代码**
   - **刷表法**
     - 和**刷表法**相对的是**填表法**，所谓填表法我的理解就是传统的利用之前选好的状态来计算当前的状态，书中是这么说的“**对于每个状态i，计算f(i)，这需要对于每个状态i都找到f(i)的依赖的所有状态**”
     - 正如书中的一句话“在某些时候并不方便”，即找f(i)的所有依赖状态不好找，于是就诞生了刷表法
     - 所谓刷表法，我的理解就是用计算好的状态更新它所影响到的状态，书中原话“**对于每个状态i，更新f(i)所影响到的状态，但需要注意的是，只有当每个状态所依赖的状态对它的影响相互独立时才能用刷表法**”，后半句话我也不是很理解，以后遇到了能理解的对应的题再说
   - **DAG模型**
      - 动态规划能够概括出DAG模型是紫书动态规划篇给我最大的惊喜，巧妙地把我之前总结的那繁多的模型分类大大地再次概括了一遍
      - DAG模型有点像刷搜索题时一般，**将题目抽象成一个有向无环图，然后求解最大距离或者是最小距离**
      - 个人感觉由于有时太过抽象，所有很难把它的图给抽象出来

## 3. **几个重要的模型**

![动规模型](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC动规模型.png)

- 上图中有很多模型都可以归类为**DAG模型**（习得紫书后才了解到的），但是有些是完全可以单拿出来作为经典模型的
- 借用陈峰老师的一句话：子结构状态形成的如果是一棵**树**，不就是搜索了吗，如果是一个**图**，不就是动态规划了吗
- 原话是这个意思，感触很深，确实，有些题遇到了把他抽象出来如果是一颗树，基本应该优先考虑dfs、bfs等搜索操作，如果是一个图，即有**重复的子结构**，就可以考虑**动规**了

### 数塔模型

- 这个模型是许多人动规入门的题，变种不多，但是却很经典

#### 【例题】[HDU 2084 数塔](http://acm.hdu.edu.cn/showproblem.php?pid=2084)

- 题意：有一个树形的塔，每个节点都有权值，让你求出从根节点到最后一层叶子节点的的最大权值和
- 简单的分析
  - 这题是不能贪心的，即不能只选出当前最大的叶子权值
  - 假设已经算好了第2层的节点的最大权值和，则第1层的节点（根节点）的最大权值就为第二层的两个节点的最大权值和加上第一层的权值
  - 其余节点也可假设成第一层和第二层来思考
  - 所以设$dp[ i][ j]$为第i层的第j个节点的最大权值和，边界条件就是最后一层的最大权值和就是其本身
  - 状态转移方程如下

  $$
  dp[i][j] = max(dp[i - 1][j],\ \ dp[i - 1][j + 1]) + tree[i][j]
  $$

  - 发现空间还是可以优化的，因为**当前层数的dp值利用的只有下一层的dp值**，而数组的的第二维计算时利用的是后面的（j + 1）的值，所以第二维是下标小的先更新，下标大的后更新，所以完全可以把第一维抛弃掉，但这样的话dp下标循环起来必须是**从小到大循环**
  - 状态转移方程如下
  
  $$
  dp[j] = max(dp[j], dp[j + 1]) + tree[i][j]
  $$

  - 代码如下
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <vector>

  using namespace std;

  int c, n;

  int main() {
      scanf("%d", &c);
      while (c--) {
          scanf("%d", &n);
          int tree[110][110] = {0};
          for (int i = 0; i < n; i++)
              for (int j = 0; j <= i; j++)
                  scanf("%d", &tree[i][j]);
          int *dp = new int[n];
          for (int i = 0; i < n; i++) {
              dp[i] = tree[n - 1][i];
          }
          for (int i = n - 2; i >= 0; i--)
              for (int j = 0; j <= i; j++)
                  dp[j] = max(dp[j], dp[j + 1]) + tree[i][j];

          printf("%d\n", dp[0]);
      }
      return 0;
  }
  ```

### 凑硬币模型

- 凑硬币模型是一个经典的模型，题目大概就是**给你一个目标，你有许多不同的结构，你要用这些结构来组成这个目标**，让你求最大组成量、最小组成量或者是组成方法数（组成方法数可以说是爬楼梯模型）

#### 【例题1】[leetcode 322 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

- 大致题意：给你不同面额的硬币，让你输出凑成价值n的最小使用数量，若无法凑成则返回-1
- 这题就是一个凑硬币的板子题
- 简单分析：
  - 假如你有1 2 5的硬币，假设你已经求出$x$的最小使用数量$y$，**则就能算出 $x + 1$ 、$x + 2$ 、$x + 5$ 的最小使用数量，即都为** $y + 1$
  - 设 $dp[ x]$ 表示价值为x的最小凑的数量，边界的话显然，$x = 0$ 时最小要凑的数量为 $0$，所以边界就是 $dp[ 0] = 0$
  - 所以不难得出状态转移方程
  
  $$
  dp[ x] = min \lbrace dp[ x - coins[ i]] + 1 \rbrace ,\ \ x \ge coins[ i],\ \ i = 0, 1, 2 \cdots
  $$

  - 代码如下

  ```c++
  class Solution {
  public:
      int coinChange(vector<int>& coins, int amount) {
          vector<int> dp(amount + 1, 0x3f3f3f3f);
          dp[0] = 0;
          for (int i = 0; i <= amount; i++) {
              for (auto c : coins) {
                  if (i >= c) dp[i] = min(dp[i - c] + 1, dp[i]);
              }
          }
          if (dp[amount] == 0x3f3f3f3f) return -1;
          return dp[amount];
      }
  };
  ```

#### 【例题2】[leetcode 279 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

- 大致题意：平方数{1, 4, 9, 16 ……}，给你一个数n，让你用平方数组成该数，求最小组成数量
- 简单的分析：
  - **把平方数看成硬币**，然后就是硬币题了
  - 状态转移方程一样的，直接上代码
  
  ```c++
  class Solution {
  public:
      int numSquares(int n) {
          vector<int> dp(n + 1, 0);
          dp[0] = 0;
          for (int i = 1; i <= n; i++) {
              dp[i] = dp[i - 1] + 1;
              for (int j = 2; i - j * j >= 0; j++) {
                  dp[i] = dp[i] > (dp[i - j * j] + 1) ? dp[i - j * j] + 1 : dp[i];
              }
          }
          return dp[n];
      }
  };
  ```

### LIS模型

- LIS就是所谓的**最长不下降子序列**问题，变种不多，但很经典，所以直接看例题

#### 【例题1】[leetcode 300 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

- 题意：给定一个无序的整数数组，找到其中最长上升子序列的长度，注意**严格上升**
- 简单的分析：
  - 分享一下一开始我学动规的时候LIS定义的状态是dp[i]表示下标为i的数组之前的最长上升子序列（即可以不选$nums[i]$），但是这样来定义我发现很难进一步地对状态进行转移，后来还是妥协了书中的状态定义方式
  - 这件事现在看来也是有点感慨，因为现在的我明白：**状态定义的不同，转移的方式可能也会完全不一样，所以有时候不同的状态定义会影响转移的难易程度，在思考一道动规题时，如果发现状态难以转移，不妨试试换个状态定义**
  - 设dp[i]表示选择下标为i的数为结尾的最长子序列，这样做的好处就是方便后面的转移，**方便利用当前下标数和之前算好的dp状态的下标数进行对比大小**，可以想象成当前状态是和之前算好的状态进行拼接
  - 则转移方程为
  $$
  dp[i] = max\lbrace 1,\ \ dp[j] + 1 \ \ | \ \ 0 \le j < i, nums[j] < nums[i] \rbrace
  $$
  - 不难发现，状态转移**需要利用到下标为j的原数组的数**，所以这个说明了状态定义就很重要了
  - 最终的答案就是取其中的最大值
  - 这种做法的时间复杂度是O($n^2$)，当然还有更快的速度可以达到O($nlogn$)，这里不再赘述了
  - dp代码如下

  ```c++
  class Solution {
  public:
      int lengthOfLIS(vector<int>& nums) {
          int len = nums.size(), Max = 0;
          vector<int> dp(len, 1);
          for (int i = 0; i < len; i++) {
              for (int j = 0; j < i; j++) {
                  if (dp[i] < dp[j] + 1 && nums[j] < nums[i]) dp[i] = dp[j] + 1;
              }
              Max = max(Max, dp[i]);
          }
          return Max;
      }
  };
  ```

### LCS模型

- LCS就是**最长公共子序列**，也很经典

#### 【例题1】[AcWing 897. 最长公共子序列](https://www.acwing.com/problem/content/description/899/)

- 题意：给你两个长度分别为n、m的字符串，让你求最长的公共子序列有多长
- 简单的分析：
  - 设a序列的字符分别为$a_1, a_2,a_3……a_n$，b序列的字符分别为$b_1, b_2, b_3……b_m$
  - 假如序列a为ab，b为a，则他们的LCS就是1，注意到，如果b序列后面加个b变为ab，则LCS就是2，但如果加的是c变为ac，则LCS则还是1
  - 设$dp[i][j]$表示a序列长度为i，b序列长度为j时的LCS，假设已经求出了之前的状态，则**当$a_i = b_j$时，则$dp[i][j] = dp[i - 1][j - 1] + 1$，即LCS长度加一，但如果不相等，则$dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])$**，不难看出这个转移方程的意义，下面上一个图更好理解
  ![LCA递推图](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPICLCS递推图.png)
  - 其中红色是a序列的各个字符，橙黄色是b序列的各个字符，**表格中的数字是相对应的LCS**
  - 代码如下
  
  ```c++
  #include <cstdio>
  #include <algorithm>

  using namespace std;

  int n, m, dp[1010][1010];
  char a[1010], b[1010];

  int main() {
      scanf("%d%d%s%s", &n, &m, &a[1], &b[1]);
      for (int i = 1; i <= n; i++) {
          for (int j = 1; j <= m; j++) {
              if (a[i] == b[j]) dp[i][j] = dp[i - 1][j - 1] + 1;
              else dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
          }
      }
      printf("%d", dp[n][m]);
      return 0;
  }
  ```

### 背包模型

- 背包问题是经典的一类动规问题
- 篇幅过长已搬出（[传送门](https://haofish.github.io/2021/03/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98/)）

### DAG模型

- DAG模型是一个概括非常广的模型，它包括凑硬币模型、背包模型等等，但还是要写写，因为我觉得DAG更多的是一种思想，把DP问题抽象成一个DAG的图，便于思考

#### 【例题1】[UVA 1025 A Spy in the Metro](https://vjudge.net/problem/UVA-1025)

- 大致题意：一个线性的地铁，有$n(2 \le n \le 50)$个站，目标是在$T(0 \le T \le 200)$时刻从1号站到n号站，注是规定刚好T时刻，不是在T时刻之前也不是在T时刻之后，从1号点出发，可以在途中转乘，问你最少的中途等车时间
  - 第i站到第i + 1站需要花的时间为$t_i$
  - 有$M1(M1 \le 50)$个车从1号站出发，出发时间分别为$d_1, d_2, d_3……, d_{M1}\ \ (d_i < d_{i + 1}, d_i \le 250)$，同样有M2个车从n号站出发，时间格式同上
  - 其余细节看题
- 简单分析：
  - 就看当先的状态，假设现在的时间是tim，正在第p个站有三种策略
    - **等1分钟，剩下的下一分钟再说**
    - **如果有向1号站行的车，乘坐上去**
    - **如果有向n号站行的车，乘坐上去**
  - 那么这个是怎么建立DAG图的呢，对于每个点，定义它包括的**属性有时间和站点**，如果当前时间当前站点有开往下个一或者上一个站的车，则将到站的时间和对应的站点连一条有向的边，例如当前时间是15，站点是3，并且有开向第2站点的车，10单位的时间后到达，但没有向下一个站开的车，则在属性为（15，3）的节点**连一条有向边**到（25，2）的节点，
  - 当然千万别忘了一点就是**下1单位时间的同一站点也有一条边**，即（15，3）到（16，3）也有一条有向边
  - 然后就可以利用这个DAG图的节点属性来定义状态了，即设dp[p][tim]为在p站点tim时刻的状态，既然是求最小路，则状态就是到当前节点的最短路
  - 对应三种策略的状态转移方式
    - 当前的状态等于下一单位时间的状态加1的等待时间
    - 当前状态等于到站后的时间和站点的状态
    - 同上
  - 最后取一个最小值
  - **边界条件就是如果在T时刻和n站点刚好到达，则返回0**，因为不用等了，如果超过了T时间，则说明是从某个站点到另一个站点后时间过了，则返回无穷大表示不用等了，如果刚好到T时刻且没到达n站点，则说明后面再怎么坐车时间也过了，也返回无穷大
  - 说了这么多，却没写状态转移方程是因为打算使用记忆化来做，所有不用转移方程，直接考虑当前递归的来自哪些结果就好了
  - 代码如下

  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>

  using namespace std;

  const int INF = 0x3f3f3f3f;

  int n, T, t[100], kase = 0, dp[110][210];
  bool has_train[110][210][2], vis[110][210];

  bool read() {
      int m, x;
      scanf("%d", &n);
      if(!n) return false;
      scanf("%d", &T);
      for (int i = 1; i < n; i++) scanf("%d", t + i);
      memset(has_train, 0, sizeof has_train);
      memset(vis, 0, sizeof vis);
      scanf("%d", &m);
      for (int i = 0; i < m; i++) {
          scanf("%d", &x);
          has_train[1][x][0] = true;
          for (int j = 2; j <= n; j++) {
              has_train[j][x + t[j - 1]][0] = true;
              x += t[j - 1];
          }
      }
      scanf("%d", &m);
      for (int i = 0; i < m; i++) {
          scanf("%d", &x);
          has_train[n][x][1] = true;
          for (int j = n - 1; j >= 1; j--) {
              has_train[j][x + t[j]][1] = true;
              x += t[j];
          }
      }
      return true;
  }

  int DP(int p, int tim) {
      int &d = dp[p][tim];
      if (tim > T) return INF;
      if (tim == T) return p == n ? 0 : INF;
      if (vis[p][tim]) return d;
      vis[p][tim] = true, d = INF;
      d = DP(p, tim + 1) + 1;
      if (p < n && has_train[p][tim][0]) d = min(d, DP(p + 1, tim + t[p]));
      if (p > 1 && has_train[p][tim][1]) d = min(d, DP(p - 1, tim + t[p - 1]));
      return d;
  }

  int main() {
      while (read()) {
          int ans = DP(1, 0);
          if (ans >= INF) printf("Case Number %d: impossible\n", ++kase);
          else printf("Case Number %d: %d\n", ++kase, ans);
      }
      return 0;
  }
  ```

#### 【例子2】[UVA 437 The Tower of Babylon](https://vjudge.net/problem/UVA-437)

- 题意：给你n种有无数个的立方体，现在让你用这些立方体堆一个塔，每个立方体的底面长宽都要严格小于下面立方体的底面长宽，问你最高能堆多高
- 简单的分析
  - 能看得出来是一个矩形嵌套的变种问题，用**DAG模型建立**来做会非常好码代码
  - 一种立方体有三种摆放方式，假设一个立方体的长宽高为a、b、c，则三种摆放方式分别是以a、b、c为高的摆放方式，固一种立方体可以看成三个立方体
  - 下面就是建立DAG了，**如果一个立方体的底面长宽严格大于另一个底面长宽的立方体则连一条有向边过去**
  - 最后就DP就是求**从某个节点出发的最大距离**就是答案
  - 注意到，无论是思考还是码代码都不需要再去想状态方程来，直接当成一个图求最远距离来做，为了加速，我用了邻接表来建立图
  - 下面是代码

  ```c++
  #include <cstring>
  #include <cstdio>
  #include <algorithm>

  using namespace std;

  int n, kase = 0, head[100], cnt = 0, dp[500], vis[500];

  struct rect{
      int a, b, c;
      rect(){}
      rect(int a, int b, int c) : a(a), b(b), c(c) {}
  }r[100];

  struct edges{
      int to, next;
      edges(int to = 0, int next = -1) : to(to), next(next) {}
  }edge[10010];

  bool ok(const rect& x, const rect& y) {
      return (x.a > y.a && x.b > y.b) || (x.b > y.a && x.a > y.b);
  }

  void add_edge(int u, int v) { edge[++cnt] = edges(v, head[u]); head[u] = cnt; }

  bool read() {
      scanf("%d", &n);
      if (!n) return false;
      int x[3];
      for (int i = 0; i < 3 * n;) {
          for (auto &j : x) scanf("%d", &j);
          r[i++] = rect(x[0], x[1], x[2]);
          r[i++] = rect(x[0], x[2], x[1]);
          r[i++] = rect(x[1], x[2], x[0]);
      }
      memset(head, -1, sizeof head);
      memset(edge, 0, sizeof edge);
      memset(dp, 0, sizeof dp);
      memset(vis, 0, sizeof vis);
      cnt = 0;
      for (int u = 0; u < 3 * n; u++) {
          for (int v = 0; v < 3 * n; v++) {
              if (u == v) continue;
              if (!ok(r[u], r[v])) continue;
              add_edge(u, v);//邻接表建图
          }
      }
      return true;
  }

  int DP(int u) {
      if (u >= 3 * n) return 0;
      if (vis[u]) return dp[u];
      int &res = dp[u], Max = 0;
      res = r[u].c, vis[u] = true;
      for (int v = head[u]; ~v; v = edge[v].next) {//它的下一个节点
          Max = max(Max, DP(edge[v].to));
      }
      return res += Max;
  }

  int main() {
      while (read()) {
          int ans = 0;
          for (int i = 0; i < 3 * n; i++) ans = max(ans, DP(i));
          printf("Case %d: maximum height = %d\n", ++kase, ans);
      }
      return 0;
  }
  ```

#### 【例题3】[UVA 116 Unidirectional TSP](https://vjudge.net/problem/UVA-116)

- 大致题意：有个$m \times n$矩阵，对于每个点你可以向直接向右、右上，右下走，第1行的上一行是第m行，第m行的下一行是第1行，问你从第一列的某一行出发，到达最后一列所经历的点的和最小是多少，并且打印出每一列的行号，如果有多解，输出字典序最小的
- 简单的分析：
  - 可以看出是一个数塔问题的变种
  - 书中说这种问题叫做**多阶段决策问题**中的一类——**多阶段图**的最短路问题，所谓多阶段图按书中说法就是图中结点可以划分成若干个阶段
  - 在递归过程中即将完成的决策被称为阶段，回忆解答树中星号之前的第一个数字，应该就是书中所说的阶段吧，或者说每个序就是一个阶段（因为在01背包中，一个物品有选和不选两种决策，每个物品序号可以看成一个阶段）
  - 这题我的理解就是原数塔问题也可以说是一个DAG，而这题相当于多个数塔重合的版本，固每一列都是对应着一个的阶段，而每个阶段都有许多状态，每个状态都由上一个阶段能影响它的状态推得
  - 引用《算法笔记》的话是这样的：“**它可以描述成若干个有序的阶段，且每个阶段的状态只和上一个阶段的状态有关**”
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

#### 【例题4】[UVA 12563 Jin Ge Jin Qu hao](https://vjudge.net/problem/UVA-12563)

- 大致题意：在KTV里，如果还剩下1秒的时间，则可以点一首更长的歌，因为他会播放完最后一首歌才停止，现在给你剩下的时间$m$和$n(n \le 50)$首歌，每首歌的时长$t_1, t_2, t_3 ……t_n$，现让你算出在剩余的时间内能唱的歌的最大数量，然后利用空出来的时间最后再点一首长为678秒的歌，输出能唱的最大数量，对应的时间
- 简单的分析：
  - 题目中说最后会点一首678秒的歌来延长时间，则策略就是计算在不超过$m - 1$时间里选最多的歌
  - 读一读上面的一句话，是不是感觉很想**01背包**？是的就是01背包问题，每个物品的价值默认是1了（一首歌嘛）
  - 题目中说$m \le 10^9$，其实并没有这么大，~~我一开始也很苦恼这怎么建数组~~，后来发现其实并没有这么大，他说n + 1首歌的时长严格大于剩余的时长，并且每一首歌不会超过3分钟，这么算的话$180 \times 50 + 678 = 9678$，固数组完全够开
  - 值得注意的是这题计算在选歌的数量多的前提下最后尽量晚地结束KTV，这就需要讨论了，*一开始被这个搞得十分地晕，冷静下来后发现一个if是不够的，于是就**特判3次***
    - 首先**如果当前抉择的歌曲数量还没之前算的多**，直接跳过
    - 如果**当前抉择的歌曲数量严格比之前算的还多**，则更新歌曲数量和时间总长度
    - 如果**当前抉择的歌曲数量和之前算的一样多**，则还要判断当前抉择的时长是严格比上次算还多，则更新时间总长度
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

## 4. 分类

![dp分类](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPICdp分类9.png)

### ① 线性DP

### ② 区间DP

- 区间dp一般是将状态定义成某某区间内的某个解
- 一般遍历方式

```cpp
for(int len = 1; i <= n; ++i) { // 先枚举区间长度
    for (int i = 1, j = i + len - 1; j <= n; ++j, ++i) { // 再枚举起始节点
        // 
    }
}

for(int i = n; i >= 1; --i) { // 先枚举左端点
    for (int j = i; i <= n; ++j) { // 再枚举右端点
        //
    }
}
```

### ③ 树型DP

- 来自2021.3.22的更新，回来看了一下，以前的写博客也太蠢了吧，不想改了，直接看下面几篇博客[放博客链接]

- 所谓树状DP（树形DP）就是说一个动态规划的问题他的数据（或者子问题）之间是建立在树的基础上提问的，即父节点的最优是由其子节点的最优来推出来的（大部分是这样）。

#### 【例题1】[HDU 1520 Anniversary party](http://acm.hdu.edu.cn/showproblem.php?pid=1520)

- 大致题意：一棵树，一共有n个节点，父节点和子节点不能同时选，每个节点都有权值，问怎么选权值总和最大

- 显然是要用到树DP了~~废话~~，给我的感觉有点像是一个变种的**01背包问题**~~不清楚~~

- 简单的分析：
  - 对于一个节点有两种可能，**选**或者**不选**。那么我们就这样定义状态：dp\[i][j],  $1 \le i \le n$、 $0 \le j \le 1$，表示i节点的权值总和最大，$j = 0$表示不选择当前节点时的权值总和最大，$j = 1$表示选择当前节点时的权值总和最大
  - 当一个节点选了之后，那么它的所有子节点都不能选（当然它的父节点也不能选，但是考虑到我们这题用dp来解，为了不扰乱思维，我们**只看它和它的子节点**），看图：
  ![父选子不选](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC父选子不选.png)
  - **黄色**为选，**灰色**为不选，父亲节点选了后，它下面的2、3、4、5、6……都不能选了（清楚明了），喂喂喂，你可别再想1号节点的父亲的事了，我们正在dp呢，它父亲节点以后它爷爷节点会搞定的，别再多想了，不然会扰乱思维。那么很容易得到这部分的转移方程(son[i]表示i的子节点, w[i]表示当前节点的权值)：
  $$
  dp[i][1] = w[i] + \sum dp[son[i]][0]
  $$
  - 如果这个节点不选呢？不说话，先看图：
  ![父不选子选](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC父不选子选.png)
  - 还是一样：**黄色**为选，**灰色**为不选。可能有人很奇怪：“哎呀，为什么儿子还有不选的情况，既然父亲都不选了，那么他的儿子全选不就总和最大了吗”。别急，且听我慢慢道来，如果这个是一条链，那么你这种想法可能就是对的，但这个是一棵树，之所以会有这个疑惑是因为我的图没上画完呢。
  ![父选子不选补充](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC父不选子选补充.png)
  - 说明：方形内的数字是节点的权值。显然如果要总和最大，那么5号节点就不能选，因为它的5号几点的子节点（8号节点）的权值更大（清楚明了😁）。所以又可以得到相应的转移方程：
  $$
  dp[i][0] = \sum max(dp[son[i]][1], dp[son[i]][0])
  $$
  - 最后我这题采用的是自顶向下的递归来求的，最后根节点是通过回溯得来，当然如果喜欢自底向上的童鞋可以采用栈来遍历也可以哦。而我们的状态转移方程总结为
  $$
  dp[i][1] = w[i] + \sum dp[son[i]][0]，\ \ (选择i节点) 
  $$
  $$
  dp[i][0] = \sum max(dp[son[i]][1], dp[son[i]][0]) \ \ (不选择i节点)
  $$

  

  - 那么最后的答案就是根节点的选和不选两状态的最大值,即 max(dp\[root][1], dp\[root][0])，（其余细节看注释）。

  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>
  #define debug(n) printf("(%d)", n);
  
  using namespace std;
  
  const int N = 6e3 + 10;
  int n, l, k, w[N], dp[N][2];
  vector<int> son[N];
  
  void dfs(int r) {
      dp[r][0] = 0, dp[r][1] = w[r]; //初始化，方便转移时的求和
      for (auto s : son[r]) {        //遍历所有子节点
          dfs(s);                    //先求子节点的最优
          //↓然后回溯↓
          dp[r][1] += dp[s][0];                //当前节点选
          dp[r][0] += max(dp[s][0], dp[s][1]); //当前节点不选
      }
  }
  
  int main() {
      while (scanf("%d", &n) != EOF) { //不定组输入，输入节点数
          vector<int> fa[N]; //记录节点的父亲，用来寻找根节点
          for (int i = 1; i <= n; i++) scanf("%d", w + i); //输入节点权值
  
          while (scanf("%d%d", &l, &k) != EOF) { //输入节点之间的关系
              if (l == 0 && k == 0) break; //结束输入
              son[k].push_back(l);
              fa[l].push_back(k);
          }
  
          //寻找根节点
          int root = 1;
          for (int i = 1; i <= n; i++) {
              root = fa[i].size() == 0 ? i : root;
          }
  
          //开始转移
          dfs(root);
          int ans = dp[root][1] > dp[root][0] ? dp[root][1] : dp[root][0];
          printf("%d\n", ans); //输出答案
  
          //下面全是清空数组的代码，忽略就好
          memset(dp, 0, sizeof dp);
          memset(w, 0, sizeof w);
          for (int i = 1; i <= n; i++) {
              for (auto it = son[i].begin(); it != son[i].end();)
                  it = son[i].erase(it);
          }
  
      }
      return 0;
  }
  ```

  - 平均时间复杂度应该是O($n$)
  - 空间复杂度是O(n)，，应该不是O($ n ^ 2$),因为用的是vector。

#### 【例题2】[HDU 2196 Computer](http://acm.hdu.edu.cn/showproblem.php?pid=2196)

- 大致题意：给你一个棵无向树，每个相连的节点之间都有个距离，即边有权，让你求出每个节点距离该节点的最远节点的距离。

- 又是一道树状DP~~都放树状DP里了，怎么可能不是呢😂~~，但是这题是无向的，恰恰是因为这样给了一丝丝方便。

- 简单的分析
  - 首先既然是无向，为了方便我们把他看作有向的，就设1号节点（1号电脑）是根节点，任性~~图方便~~。
  - 每个节点，他只有两种状态（除根节点，和叶子节点），一、要么**是从它的子节点过来的距离**；二、要么**不是从它的子节点过来**。~~如图~~不画了，自己脑补😂。
  - 那么我们就这样定义状态：dp[i][j], i表示的是节点编号（$1 \le i \le n$），而 j（$0 \le j \le 1$）的话就是，当 $j = 0$ 时表示 i 节点到其最远从属于它的叶子节点 **（即他的子节点过来）的距离**， $j = 1$ 时表示**不是从它子节点过来的最远距离**。
  - 对于从子节点到来本节点的距离，只需要对比大小便可，为了方便思考，我把题目的例子改了一下，如图（这回有图啊！！）
  ![树状例题2图01](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC树状例题2图01.png)
  - 说明：方形里的数字代表距离（边权）。不难看出从2号节点的子节点（7号节点、6号节点、3号节点）到2号节点的最大距离就是子节点到来的最大值（老规矩，别想他的父节点），有点拗口，看方程（son[i]表示 i 节点的子节点, len(son[i])则表示它的子节点到他它自己的距离）：
  $$
  dp[i][0] = max\lbrace dp[son[i]][0] + len(son[i])\rbrace
  $$
  - 简单吧，下面就来看看比较难的：不是从他的子节点到来的最大距离。先上图（画的好累）：
  ![树状例题2图ii](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC树状例题2图ii.png)
  - 清楚吧，~~不用解释了吧~~，好吧好吧，我们把目光放到3号节点上，如果计算**不是从它子节点过来的距离**的话，只有两种可能，其中最简单的一条路就是从它的爷爷节点来再到他的父亲节点来的距离（黄色箭头路线），为什么只有一条？因为它肯定只有一个爷爷和父亲，不可能有两个，不可能！！！这个世界还是很美好滴；第二种可能就是从它的兄弟节点来，这个比较多路（蓝色和绿色箭头路线），因为它可以有很多兄弟，也可以没有兄弟（独生子😁）。这样理解的话方程就能出来了（fa[i]表示 i 节点父节点，所以son[fa[i]]就代表 i 节点 的兄弟节点，len的含义和上面一样）：
  $$
  dp[i][1] = max\lbrace dp[fa[i]][1]+ len(i), dp[son[fa[i]]][0] + len(son[fa[i]]\rbrace+ len(i))
  $$
  - 化简一下
  $$
  dp[i][1] = max\lbrace dp[fa[i]][1], dp[son[fa[i]]][0] + len(son[fa[i]])\rbrace + len(i)
  $$
  - 解释：max内部的前者是上述的第一种可能，后者是第二种可能，是不是简单明了😁，但是对于这个状态还利用到来我们第一个讲的（$j = 0$）状态。所以我们在计算时要先把所有 $j = 0$ 的状态先计算，即做两遍dfs（当然你也可以把他们的dfs放到一起，都一样的，为了方便~~我不会😂~~我选择做两遍dfs），注意：这个状态方程的状态，即 $j = 1$ 时的状态一定要自顶向下计算，因为方程中还用到了它父亲的第二（$j = 1$）状态。所以最终的状态转移方程总结为：
  $$
  dp[i][0] = max\lbrace dp[son[i]][0] + len(son[i])\rbrace， \ \ （第一种状态）
  $$
  $$
  dp[i][1] = max\lbrace dp[fa[i]][1], dp[son[fa[i]]][0] + len(son[fa[i]])\rbrace + len(i) \ \ （第二种状态）
  $$

  

  - 那么最后的答案是什么呢？显然就是每个节点的 $j = 0$ 和 $j = 1$ 的两种状态的最大值，即对于 i 节点 max（dp\[i][0], dp\[i][1]）。不废话，上代码（其余细节看注释）！！！

  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>
  
  using namespace std;
  const int M = 1e4 + 10;
  int n;
  vector<int> son[M]; //子节点不止一个，不放入结构体是为了方便清空数组
  long long dp[M][2];
  
  struct cpNode {    //电脑节点结构体
      int fa;        //它爹是谁
      long long len; // 它到它爹的距离
      cpNode(int f = 0, long long l = 0) : fa(f), len(l) {}
  } cp[M];
  
  void dfs1(int r) { //状态一
      for (auto s : son[r]) {
          dfs1(s);
          dp[r][0] = max(dp[r][0], dp[s][0] + cp[s].len);
      }
  }
  
  void dfs2(int r) { //状态二
      dp[r][1] = dp[cp[r].fa][1] + cp[r].len; //先把第二种状态的第一种可能算了，还记得吗因为它只有一个爹
      for (auto bro : son[cp[r].fa]) { //遍历他的兄弟
          if (bro != r)                // 如果是它兄弟，即不是它自己
              dp[r][1] = max(dp[r][1], dp[bro][0] + cp[bro].len + cp[r].len);
      }
      for (auto s : son[r]) dfs2(s); //计算它子节点的状态二
  }
  
  int main() {
      while (scanf("%d", &n) != EOF) { //多组输入，这设定把我坑惨了
          for (int i = 2; i <= n; i++) {
              int f, len; // f为第 i 号节点的父亲， len是到它父亲的距离
              scanf("%d%d", &f, &len);
              son[f].push_back(i);
              cp[i] = {f, (long long)len};
          }
  
          dfs1(1); //第一状态的转移计算
          for (auto s : son[1]) dfs2(s); //把除 1 号节点的节点都进行计算第二状态，因为 1 号节点只有第一状态
  
          for (int i = 1; i <= n; i++) printf("%lld\n", max(dp[i][0], dp[i][1])); //输出每个节点的结果
  
          //清空数组，可以不看
          memset(dp, 0, sizeof dp);
          for (int i = 1; i <= n; i++) {
              for (auto it = son[i].begin(); it != son[i].end();)
                  it = son[i].erase(it);
          }
  
      }
      return 0;
  }
  ```

  - 平均时间复杂度应该也是O($n$)
  - 空间复杂度O(n)

### ④ 状态压缩DP

- 状态压缩更像是一种思想，而不是一种方法。当有些问题很可能不存在有效的(多项式级的)算法时，利用状态压缩是可以使得题目简单些。一般情况下状态压缩是把状态压缩成二进制的形式来表示（也有三进制的）一个集合，通过压缩可以把不必要的信息给去掉，从而减少冗余。

#### 常见的二进制使用

- 都是最基础的：与、或、非、异或 在c++中分别是 **&**（与）、**|**（或）、**!**（非）、**^**（异或）
- 左右移：**<<** （左移），例如 5（00000101）<< 1 = 10（00001010）； **>>**（右移）,例如 5 （00000101）>> 1 = 2 (00000010)。
- 取反：**~**（1 变 0，0 变 1），例如 ~ 00000101 = 11111010
- 查看是否有相邻的 1（状压用得多，多用来去掉不合法的状态）：**x & (x >> 1) == 0**或者**x & (x << 1) == 0**：语句判断为真则没有相邻的1，否则有相邻的 1。例如 88（01011000）& 44 (00101100) = 8 (00001000) $\ne$ 所以有相邻的 1。（不懂就自己多举几个例子）
- 检查 a 是否是 b 的子集（状压也用得多）：**a & b == a**：语句判断真为是其子集，否则不是。例如 88 是 16 的子集，因为88（01011000）& 16（00010000）= 16（00010000）
- 判断 a 和 b 是否有相同位的 1：**a & b == 0**：语句判断真为没有，否则有，很简单不解释
- 取末尾的 1：**x & (-x)**，例如 88（01011000）^ (-88)（10101000）= 8（00001000）——多用于树状数组

#### 【例题 1】[HDU 方格取数（1）](http://acm.hdu.edu.cn/showproblem.php?pid=1565)

- 大致题意：给你一个方格，里面有数字，你可以在这些方格里面取数，但是不能取拥有公共边的方格，问你取出来的最大值为多少
- 一道挺标准的状压dp板子题，一开始用普通dp没做出来，后来发现用状压真的简单一些
- 简单的分析
  - 对于每个格子只有两种状态：**选**或者**不选**，我们定义选中了则在相应的二进制位数为 1，否则为 0。例如5 （000101）表示一行中第一个数和第三个数被选中
  - 对于一行有 n 个格子的总共的选择方式个数为 $2^n$
  - 既然都说了状压，那直接开压：把列压缩（对于这题行和列相同，压列是个人喜欢，更多情况下是压缩数据范围小的那个，你当然希望$2^n$越小越好啦）
  - 下一步在输入之前把不合法的**状态预处理**了，只留下合法的，就是本题所说的不能选取拥有公共边的格子，即对于每一行状态不能有相邻的 1
  - 然后是转移方程（很简单，并且不难想）
  
  $$
  dp[i][now] = max\lbrace dp[i - 1][pre] + w[now]]\rbrace
  $$

  - 解释：i 表示第 i 行，now 表示当前行的二进制状态，pre 表示上一行的所有不与 now 具有相同位 1 的状态，可以用相与（**&**）是否等于 0 来判断，若等于 0 则没有相同我位的 1，否则有；w[now]表示当前状态的选取方格的总值
  - 那么最后的答案就是最后一行中的最大值
  - 代码如下
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>
  
  using namespace std;
  
  const int maxn = 25;
  unsigned int nums[maxn][maxn];//用无符型是因为HDU的尿性，不这样可能会超内存
  vector<unsigned int> v;//用来存储合法的状态
  int n;
  
  int val(int r, int v) {//求当前行状态的总价值
      int res = 0, i = n;
      while (v != 0) {
          if (v & 1) {
              res += nums[r][i];
          }
          v >>= 1;
          i--;
      }
      return res;
  }
  
  int main() {
      while (scanf("%d", &n) != EOF) {
          v.clear();                    //清空v数组
          int choice = (1 << n);        //最大的状态数
          for (int i = 0; i < choice; i++) {
              if ((i & (i << 1)) == 0) v.push_back(i);//预处理，选取合法的状态
          }
          int len = v.size();
          vector<vector<unsigned int>> dp(n + 1, vector<unsigned int>(len));//动态开辟数组，防止超内存
          for (int i = 1; i <= n; i++) {//输入
              for (int j = 1; j <= n; j++) {
                  scanf("%d", &nums[i][j]);
              }
          }
          unsigned int Max = 0;
          for (int i = 1; i <= n; i++) {
              for (int now = 0; now < len; now++) {
                  int w = val(i, v[now]);
                  for (int pre = 0; pre < len; pre++) {//枚举上一行的状态值
                      if ((v[now] & v[pre]) == 0) dp[i][now] = max(dp[i][now], dp[i - 1][pre] + w);//状态转移
                  }
              }
          }
  
          for (int i = 0; i < len; i++) {//最后的结果
              Max = max(Max, dp[n][i]);
          }
          printf("%d\n", Max);
      }
  
      return 0;
  }
  ```
  
  - 表面上时间复杂度：O($n2^n2^n$),先别急，经过预处理，实际比这个小得多得多，每个$2^n$估计会被压到最大也就4000左右，据大佬测试 n 最大是17，不会有n = 20得样例；这下子复杂度就妥妥地能过了
  - 空间复杂度：和时间复杂度同理，最大也就开个约为17 * 4000的数组
  
#### 【例题2】[洛谷 P1879 Corn Fields G](https://www.luogu.com.cn/problem/P1879)

- 题目大意：给你一个M * N的方格土地，1 表示可以种玉米，0 表示不可以种玉米。拥有公共边的格子不能同时种上玉米，问你有多少种种的方式，什么都不种也算一种，答案对 1e8 取模
- 一道状压dp计数题，也是一个很好的板子题
- 简单的分析
  - M 和 N 的数据范围都很小，所以我还是采用了**压缩列**来做
  - 要做的准备有：预处理本不合法的状态、把方格土地的01状态转换成十进制的表示，用个数组cor[i]来储存，方便后面判断是否能种、先把第一行合法的状态算了，把能种的且合法的都初始化为 1；
  - 先看状态转移方程
  $$
  dp[i][now] += dp[i - 1][pre]
  $$
  - 解释：i 表示第 i 行，now 和 pre 分别表示当前行的状态和上一行的状态，当然 now 的前提是可以在当前行上种下来，即 now 是 cor[i] 的子集，而 pre 和 now 必须没有同位的 1
  - 例如第 n 行，什么都不种（now = 0）的方法数就是上一行合法状态的和（其实所有和都可算，因为不合法的一定是0），而第 n 行状态 q 的种法的方法数就是上一行与他没有同位 1 状态的和（这里可以多一个判断，判断pre是否能在上一行上种起来，这样可以循环，当然不判断也行，因为不合法的一定为0）
  - 最后的答案就是最后一行的dp和(稍微换了一下码风，可能会舒服点，因为循环和判断有点多)
  
  ```c++
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <vector>

  using namespace std;
  const int M = 15, mod = 1e8;
  int m, n, nums[M], cor[M];
  vector<int> v;//存储合法的状态

  int main() {
      scanf("%d%d", &m, &n);
      int choice = 1 << n;
      for (int i = 0; i < choice; i++) {
          if ((i & (i << 1)) == 0) v.push_back(i);
      }
      int len = v.size();
      vector<vector<int>> dp(m + 1, vector<int>(len, 0));
      for (int i = 1; i <= m; i++) {
          for (int j = 1; j <= n; j++) {
              scanf("%d", &nums[j]);
              if (!nums[j]) continue;
              cor[i] |= (1 << (j - 1));//把每一行的方块状态转换成对应的十进制
          }
      }
      int ans = 0;
      for (int i = 0; i < len; i++) { //先处理第一行，方便后面计算
          if ((v[i] & cor[1]) != v[i]) continue;
          dp[1][i] = 1;
      }

      for (int i = 2; i <= m; i++) {
          for (int j = 0; j < len; j++) {
              if ((v[j] & cor[i]) != v[j]) continue;
              for (int k = 0; k < len; k++) {
                  if ((v[j] & v[k]) != 0) continue;
                  if ((v[k] & cor[i - 1]) != v[k]) continue;
                  dp[i][j] = (dp[i][j] + dp[i - 1][k]) % mod;

              }
          }
      }
      for (int i = 0; i < len; i++) {
          ans = (ans + dp[m][i]) % mod;
      }
      printf("%d\n", ans);
      return 0;
  }
  ```

  - 时间和空间复杂度和上一题类似，压着就可以过的

### ⑤ 数位DP

- 篇幅过长已搬出，见**数位dp**

## 5. 优化dp的手段

### ① 矩阵优化

### ② 数据结构优化

### ③ 单调队列优化

### ④ 决策单调性优化

### ⑤ 二进制优化

### ⑥ 斜率优化

### ⑦ 四边形不等式优化

未完待续（以后有空补充）……  

