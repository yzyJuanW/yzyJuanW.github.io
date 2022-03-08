# 背包问题


# 背包问题总结

本文从[**dp总结**](https://haofish.github.io/2020/dp%E6%80%BB%E7%BB%93/)中搬出

- 背包问题是一类非常经典的动规问题，包括了**01背包**、**完全背包**、**多重背包**、**混合背包**、**二维费用背包**、**分组背包**、**有依赖的背包问题**、**背包问题求方案数**、**背包问题求具体方案**（称为背包九讲）
- 本片博客会在个人觉得比较重要的地方上啰嗦qwq

## 01背包

- 最最经典的背包问题，背包九讲中说到：“它包含了背包问题中设计状态、方程的最基本思想。另外，**别的类型的背包问题往往也可以转换成01 背包问题求解**。”，固01背包的模型是非常重要的
- 下面的01背包会展示**三种空间的优化手段**，我发现这三种优化手段都是非常重要的，针对不同场景三种手段各有优势
- 直接看到最原汁原味的题

### 【例题1】[AcWing 2. 01背包问题](https://www.acwing.com/problem/content/description/2/)

- 题意不多说，就是原汁原味的01背包题，值得说的是以下内容均以V为容量，W为价值，c为背包总容量

- 分析1:

  - 对于每个物品有**选和不选的两种决策**，固我们可以从**dfs**的角度加上**记忆化**来思考这道题
  - 可以考虑每一层都代表一个物品，有选和不选两种方案，意味着有两个递归下去的道路
  - 其余细节具体看代码

  ```c++
  //调用时直接调用dfs(n, m)就好了
  int dfs(int nums, int sum) {//第nums个物品，还剩下sum的容量，
      if (nums == n + 1) return 0; // 边界
      if (vis[nums][sum]) return dp[nums][sum]; // 记忆化
      vis[nums][sum] = true;
      int &ans = dp[nums][sum];
      ans = dfs(nums + 1, sum); //不选当前的物品，则容量依旧还剩sum
      if (sum >= v[nums]) ans = max(ans, dfs(nums + 1, sum - v[nums]) + w[nums]); // 如果容量够，则选当前物品，则容量剩余sum - v[nums]
      return ans;
  }
  ```

  - 可以看得出来，**dfs+记忆化**的好处就是简单易懂，似乎完全不用过多说明代码的意义

- 分析2：
  - 本题递归+记忆化的好处就是易于思考，不需要过多地打草稿
  - 但考虑到01背包经典性和别的背包问题都可以转换成01背包来解决，所以只会递归是不够的
  - 根据上述分析1的内容，可以很容易定义状态：**设dp\[i][j]为决策到第i个物品时，还剩j容量的储存的最大价值**
  - 根据分析1的代码不难得出状态转移方程
  $$
  dp[i][j] = max\lbrace dp[i + 1][j], \ \ dp[i + 1][j - v[i]] + w[i]\rbrace \ \ (j >= v[i])
  $$
  - 递推部分代码如下，答案为dp\[1][m]

  ```c++
  for (int i = n; i >= 1; i--){
      for (int j = 0; j <= m; j++) {
          dp[i][j] = dp[i + 1][j]; // 不选
          if (j >= v[i]) dp[i][j] = max(dp[i][j], dp[i + 1][j - v[i]] + w[i]); // 选，前提是空间够
      }
  }
  ```

  - 其实从第1件物品开始也是一样的
  - 输出答案为dp\[n][m]

  ```c++
  for (int i = 1; i <= n; i++) {
      for (int j = 0; j <= m; j++) {
          dp[i][j] = dp[i - 1][j];
          if (j >= v[i]) dp[i][j] = max(dp[i][j], dp[i - 1][j - v[i]] + w[i]);
      }
  }
  ```

  - 上面这个代码比逆着来递推的好处就是可以*边读边计算*，剩下了一些空间

  ```c++
  for (int i = 1; i <= n; i++) {
      scanf("%d%d", &v, &w);
      for (int j = 0; j <= m; j++) {
          dp[i][j] = dp[i - 1][j];
          if (j >= v) dp[i][j] = max(dp[i][j], dp[i - 1][j - v] + w);
      }
  }
  ```

  - 复杂度都是$O(nm)$

- **空间优化1**：

  - 不难发现，当遍历到第$i$个物品时（顺着来递推），它利用到的状态只有上一个物品的状态，即第i - 1个物品的状态，则这个第一维完全可以用**滚动数组**来优化，即优化成$2 * Maxn$的数组
  - 直接看代码

  ```cpp
  #include <cstdio>
  #include <algorithm>
  
  using namespace std;
  
  const int Maxn = 1010;
  int n, m, dp[2][Maxn] = {0};
  
  int main() {
      scanf("%d%d", &n, &m);
      int now = 1, pre = 0; // 一个本状态，一个上一个状态
      for (int i = 1; i <= n; i++) {
          int v, w;
          scanf("%d%d", &v, &w);
          fill_n(dp[now], dp[now] + m + 1, 0); // 这句话就01背包而言可以不用加，但在其他动规问题时可能就必须初始化了
          for (int j = 0; j <= m; ++j) {
              dp[now][j] = dp[pre][j];
              if (j >= v) dp[now][j] = max(dp[now][j], dp[pre][j - v] + w);
          }
          swap(pre, now); // 滚动
      }
      printf("%d", dp[pre][m]); // 答案是推出循环后的pre维数组里的答案
      return 0;
  }
  ```

  - 这种写法的好处就是当我们在处理动规时发现他的只和上一个状态有关时就可以用这种优化，注意第19行的初始化有时候是必加的，例如在计数类dp中，我们就必须初始化当前计算的数组（即当前循环的now维数组）为0
  - 下面两种优化个人感觉比本空间优化1要重要

- **空间优化2**

  - 根据空间优化1， 我们再来看他的第二维，它会利用到$j - v[i]$的状态，计算完成后$j - v[i]$的状态就没用了
  - 综上，看图
    ![01背包空间优化](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC零一背包空间优化.png)
  - 所以完全可以将二维优化成一维的数组，为了保持$j - v[i]$是上一个物品的状态，**第二层递推时要逆着过来递推**
  - 状态转移方程如下

  $$
  dp[j] = max(dp[j],\ \ dp[j - v] + w)
  $$
  - 上述的状态方程是对应边读边计算的
  - 最终代码如下

  ```c++
  #include <cstdio>
  #include <algorithm>
  
  using namespace std;
  
  const int Maxn = 1010;
  int n, m, dp[Maxn] = {0};
  
  int main() {
      scanf("%d%d", &n, &m);
      for (int i = 1; i <= n; i++) {
          int v, w;
          scanf("%d%d", &v, &w);
          for (int j = m; j >= v; j--) {
              dp[j] = max(dp[j], dp[j - v] + w);
          }
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

  - 说明：这里的二层循环是m到v，一来是因为j要大于等于v数组才有意义，二来是因为最开始是从c到0时，当$j < v$时只有不选当前物品的选择，则状态就会继承自一个物品此时的容量的状态，而此时代码是m到v默认了$j < v$时的状态时**继承自上一个物品同容量的状态**
  - 本优化多用于**分组背包**中空间优化，**树形背包（有依赖的背包）**，记住子问题是01背包时，一维空间优化必须逆着来（有基础的同学可以回忆一下树形背包中的转化

- **空间优化3**

  - 下面介绍个人认为**最最有用的空间优化手段**
  - 先来说说第一种空间优化的好处：好理解，不必像第二种空间优化那样需要逆着过来更新状态（相信我，考虑循环的顺逆，在比赛时真的可以把自己搞晕）
  - 然而第一种空间优化也是有缺陷的：维度依然没有变化，即本来是二维的还是二维（这样的坏处就是多维时比较折磨人，想想树形背包，我们之所以可以用在定义状态时只开二维，是因为利用到了01背包一维优化的思想，更新时是逆着更新的，若要顺着更新，则可能会退化到三维，哪怕你第三位用滚动数组优化（第一种优化）可能也会被pre和now维度下标弄晕）
  - 而第二种空间优化的好处和缺陷正好是第一种空间优化的缺陷和好处
  - 综上，接下来要推出一个能集合上面两种优化的好处的优化手段
  - 先看代码，感觉看了代码就能秒懂了

  ```cpp
  #include <cstdio>
  #include <algorithm>
  
  using namespace std;
  
  const int Maxn = 1010;
  int n, m, dp[Maxn] = {0}, tmp[Maxn];
  
  int main() {
      scanf("%d%d", &n, &m);
      for (int i = 1; i <= n; i++) {
          int v, w;
          scanf("%d%d", &v, &w);
          for (int j = 0; j <= m; ++j) tmp[j] = dp[j]; // 用tmp数组存起上一次的状态
          for (int j = 0; j <= m; ++j) {
              dp[j] = tmp[j]; // 这句话可以不加，具体问题具体分析吧
              if (j >= v) dp[j] = max(dp[j], tmp[j - v] + w);
          }
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

  - 当然还有另一种写法

  ```cpp
  #include <cstdio>
  #include <algorithm>
  
  using namespace std;
  
  const int Maxn = 1010;
  int n, m, dp[Maxn] = {0}, tmp[Maxn];
  
  int main() {
      scanf("%d%d", &n, &m);
      for (int i = 1; i <= n; i++) {
          int v, w;
          scanf("%d%d", &v, &w);
          for (int j = 0; j <= m; ++j) tmp[j] = 0; // 用tmp数组来装本次要更新的状态，初始化具体问题具体分析，例如dp找到是最大值则初始化为负无穷
          for (int j = 0; j <= m; ++j) {
              tmp[j] = dp[j];
              if (j >= v) tmp[j] = max(tmp[j], dp[j - v] + w);
          }
          for (int j = 0; j <= m; ++j) dp[j] = tmp[j]; // 把值赋回给dp数组
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

  - 上述两种写法都是一个道理的，他们的好处就是依旧是顺推，依旧是一维
  - 怎么说呢，我觉得这种写法是上面两种空间优化的进化版吧，不懂的可以看后面的依赖背包写法中利用到这种方式更新状态的好处，当然多重背包单调队列优化也用到了这个技巧

- 上面的01背包问的是容量不超过m的最大价值，但如果问的是**容量恰好是m**的呢？
- 下面将不超过容量m名为问题一，容量恰好为m为问题二
- 回忆以下不超过c的最大价值的状态定义：dp\[i][j]表示正在考虑第i件物品还剩下j的容量所能装下的最大价值
- 说解法之前，先来看这么一组数据，**物品个数为4，背包容量为9，物品重量分别为{2，3，4，5}，价值分别为{3，5，2，6}**，则在不超过9容量的最大价值为11，但恰好是9容量的最大价值为10，为什么反而价值还少了呢，其实在问题一中，背包装的容量为8，并没有装满，这就意味着在问题一中dp[9] = dp[8]，有一格空间是没有用上的，使得装9和装8的价值一样，那为什么会有这种情况呢
  - 下面是dp[9]和dp[8]在问题一中的真正转移路程
  - $dp[9] = dp[4(9 - 5)] + 6 = (dp[1(4 - 3)] + 5) + 6 = (0 + 5) + 6$
  - $dp[8] = dp[3(8 - 5)] + 6 = (dp[0(3 - 3)] + 5) + 6 = (0 + 5) + 6$
- 注意到dp[1]在问题一中是为0的，上面说过为0则表示什么都不装，但是在问题二中，**除了容量本来就是0的背包外其余容量的背包在什么都不装的情况下价值不能定义为0**，为什么？因为这样在问题二中转移时为默认认为多出的1格（以上面的例子为例）是不装东西的
- 怎么解决呢，根据上述的一句话“除了容量本来就是0的背包外其余容量的背包在什么都不装的情况下价值不能定义为0”，这样这样操作：**dp[0] = 0，其余的都初始化为$-∞$**
- 还不理解就这样想：**恰好容量m一定是从容量为0的状态一步一步转移过来的**

## 完全背包

- 紫书是先说完全背包问题的，紫书是从完全背包来引入背包问题的，可能是因为完全背包和硬币模型十分相似，都是**DAG模型**的题，和硬币模型最明显不同的是硬币模型起点和终点都是固定的，而完全背包是起点固定，终点任意的题
- 直接看例题

### 【例题1】[AcWing 3. 完全背包问题](https://www.acwing.com/problem/content/3/)

- 完全背包的裸题，题意就不再赘述了
- 分析方式1：
  - 刚刚说了，完全背包和无限硬币模型（硬币无限个，问你能否组成特定的面值）相似，只不过呢和硬币模型DAG有两个不同的地方，一是**硬币模型的起点是要求的面值和，终点是0，而完全背包的DAG的起点是要求的背包总容量，而终点是任意的**；二是**硬币模型的边权是1，而完全背包问题的边权是对应物品的价值，固之前硬币问题中的“+1”就要变成“+w[i]”了**
  - 回忆一下硬币模型的状态定义：dp[i]表示面值为i的最大（最小）凑的数量，转移方程是：$dp[i] = max \lbrace dp[i - coin[j]] \rbrace + 1, \ \ i \ge coin[j]$，其中**除dp[0]为0外，其余都初始化为**$-∞$
  - 上面说过，硬币模型的DAG终点是0，但完全背包问题的终点是任意的，固完全背包应该对所有dp值都初始化为0（这个和01背包中的不超过容量m和恰好容量是m的道理是一样的），不难得出完全背包的转移方程
  $$
  dp[i] = max\lbrace dp[i - v[j]]\ \ +\ \ w[j]\rbrace ,\ \ i \ge v[j]
  $$
  - 和硬币模型差不多其中dp[i]表示容量为i的背包能装的最大价值，输出答案为dp[m]
  - 代码如下

  ```c++
  memset(dp, 0, sizeof dp);
  for (int i = 0; i <= m; i++) { // 面值 对应完全背包的背包容量
      for (int j = 1; j <= n; j++) { // 硬币 对应完全背包的物品
          if (i >= v[j]) dp[i] = max(dp[i], dp[i - v[j]] + w[j]);
      }
  }
  ```

  - 以上代码是仿照凑硬币模型写的，**第一层循环和第二层循环是完全可以对调的**

  ```c++
  memset(dp, 0, sizeof dp);
  for (int j = 1; j <= n; j++) {
      for (int i = 0; i <= m; i++) {
          if (i >= v[j]) dp[i] = max(dp[i], dp[i - v[j]] + w[j]);
      }
  }
  ```

  - 注意到，上面的代码第二层循环的起点完全可以改成**从v[j]到m**，然后if语句就可以删去了
  - 至于最终答案也是dp[m]

  ```c++
  memset(dp, 0, sizeof dp);
  for (int j = 1; j <= n; j++) {
      for (int i = v[j]; i <= m; i++) {
          dp[i] = max(dp[i], dp[i - v[j]] + w[j]);
      }
  }
  ```

  - 时间复杂度为O(mn)
  - 对于并非第一次接触完全背包的同学，是不是觉得上面的代码很像最终学到的代码呢，没错，上面的代码就是即将进入的**分析方式2**的最终结论
  - 此时比较乱的同学可能有疑惑，第二重循环能否逆着来呢，答案是不行，细心的同学可能发现了，如果**逆着过来就是01背包**了
  - 那么为什么不行呢，其实很简单，回到最开始完全背包的代码甚至是硬币模型的代码，我们是从0开始一步一步递推到最终目标m的，这样能推出最终答案，仔细想想也是，怎么可能是先从目标c开始推呢？
- 分析方式2：
  - 对于完全背包，上面的分析1完全是够用的，但是就以背包问题而言，这样是不够的
  - 考虑这么一个物品，其重量为v[i]，则它最多只能装**m/v[i]个**，这个很重要，这个结论意味着完全背包可以转换成01背包来做
  - 将每个无限个的物品看成**有限的m/v[i]个**，然后当成01背包来做，**设dp\[i][j]表示对于第i件物品，容量为j**转移方程如下
  $$
  dp[i][j] = max\lbrace dp[i - 1][j],\ \ dp[i - 1][j - k \times v[i]] + k \times w[i]\rbrace \ \ k = 1, 2, 3, 4 \cdots
  $$
  - 其中k = 0时就是dp\[i - 1][j]，这个转移方程之所以这么写是为了对应01背包的转移方程
  - 相应的代码如下

  ```c++
  memset(dp, 0, sizeof dp);
  for (int i = 1; i <= n; i++) {
      for (int j = 0; j <= m; j++) {
          dp[i][j] = dp[i - 1][j]; // 不选
          for (int k = 1; k * v[i] <= m; k++) { //选
              if (j >= k * v[i]) dp[i][j] = max(dp[i][j], dp[i - 1][j - k * v[i]] + k * w[i]);
          }
      }
  }
  ```
  
  - 时间复杂度为O($m n\times \frac{m}{v[i]}$)
  - 很遗憾TLE了，对转移方程做一下变换
$$
dp[i][j] = max\lbrace dp[i - 1][j], dp[i][j - v[i]] + w[i]\rbrace
$$
  - 解释：将每**种**无限的物品看成一**个**一**个**的，当不选这**种**物品时，则决策为上一**种**物品容量相同时的状态，当选这一**种**物品的一**个**时，其由同**种**物品的$j - v[i]$的容量转移过来
  - 换句话说：如果我选当前物品，则应该是当前物品背包容量还剩$j - v[i]$加上当前物品的价值，即我选了当前物品我还能继续考虑是否选当前物品
  - 状态定义是不变的代码如下

  ```c++
  for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= m; j++) {
          dp[i][j] = dp[i - 1][j]; // 不选
          if (j >= v[i]) dp[i][j] = max(dp[i][j], dp[i][j - v[i]] + w[i]); //选
      }
  }
  ```

  - 时间复杂度为O(nm)，这回Ac了
  - 但同样是转换成01背包，为什么这个转移方程复杂度就降得如此低呢？说起来有点绕，如果下面解释完了还没明白可以自己草稿模拟两个代码或者一层一层递推状态方程，找到区别
- 假设我们考虑到dp\[i][j]时，对于复杂度为O(cn)的转移，dp[i][j]由两个状态转移而来
    - 一是**dp\[i - 1][j]**，表示不选当前物品，状态来自上一种物品的同容量状态
    - 二是**dp\[i][j - v[i]] + w[i]**，表示选当前物品，状态来自同种物品，小v[i]容量的状态而来
  - 根据动态规划的原则，上一个状态一定是计算好并且是当前最优解的值
  - 而对于复杂度为O($m n\times \frac{m}{v[i]}$)的话，dp\[i][j]则由多个状态而来
    - 基本都是由上一种物品**对应$j - k \times v[i]$容量**的状态而来，**即$dp[i - 1][j - k \times v[i]] + k \times w[i]$**
  - 注意到在复杂度为O($m n\times \frac{m}{v[i]}$)中计算**dp\[i][j]时**，用到了一个状态就是**dp\[i - 1][j - 2 $\times$ v[i]]**，而我们在计算**dp\[i][j - v[i]]时**也用到了**dp\[i - 1][j - k $\times$ v[i]]这个状态**，假设dp\[i][j-v[i]]的最终值就是来自于dp\[i - 1][j - t $\times$ v[i]]，则dp\[i][j]就相当于也利用到了$dp[i][j - v[i]]$的值，固可以**把众多的[i - 1]的状态归纳为[i]**，其余的便可以**反推归纳为dp\[i - 1][j]**
  - 真相已经浮出水面了：在复杂度为O(mn)的算法里**dp\[i][j - v[i]]的值可能本来就包含了（装了）若干个值为v[i]的物品**，这就避免了复杂度为O($m n\times \frac{m}{v[i]}$)算法中许多的max计算
  - 再来说说完全背包的空间优化
  - 和01背包一样，可以把二维优化成一维的，但值得注意的是：j - v[i]的第一维是i，和01背包的i - 1不同，固它用的不是上一层的数据，而是刚更新的同层的下标在j之前的数据，所以第二层于01背包不同，要顺着遍历，即从小到达遍历
  - 代码与**分析1**中的最终代码是一样的
  
```c++
memset(dp, 0, sizeof dp);
for (int j = 1; j <= n; j++) {
    for (int i = v[j]; i <= m; i++) {
        dp[i] = max(dp[i], dp[i - v[j]] + w[j]);
    }
}
```

- 一个**重要的优化**
  - 对于0(mn)的复杂度应该说是非常优秀的了，但是O($m n\times \frac{m}{v[i]}$)在不换转移方程的思路下还是有别的优化方式的，能优化到O($nmlog\frac{m}{v[i]}$)
  - 采用一种**二进制的方式优化**
    - 考虑这么一个数10，现在要你拆分成几个数，使得能够用这些数自由组合能够组合成和为1\~10的数。最简单的方式就是把10拆成10个1，就能将1\~10都能凑出来了
    - 但是如果只拆成10个1就没有优化的意义了，现在要你拆分成最少的数使得自由组合能凑成1~10的数，有一种比较优秀的拆分方式，拆分的结果为：1，2，4，3；怎么分的？观察到第一个数是$2^0$，后面除了最后一个数依次是$2^1、2^2$，最后一个数是$10 - 2^0 - 2^1 - 2^2 = 3$得到的
    - 这样做的好处是，这4个数，每个数只有选和不选两种选择，一次对应4个位数的二进制的数，例如0101就是选了2，3，于是就凑成了5
  - 原话是这么说的：“这是二进制的思想。因为，不管最优策略选几件第i 种物品，其件数写成二进制后，总可以表示成若干个$2^k$件物品的和”
  - 至于价值当然也要变成对应的倍数价格啦
  - 对于每个拆分出来的新物品只有选和不选的两种决策，不就是01背包了嘛
  - 其余的有点难解释，看代码细细地品吧

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <vector>

using namespace std;

const int Max = 1010;
int n, m, dp[100005][Max], v, w;

struct goods{
    int v, w;
    goods(int v = 0, int w = 0) : v(v), w(w) {}
};
vector<goods> good;

int main() {
    scanf("%d%d", &n, &m);
    good.push_back(goods(0, 0));
    for (int i = 0; i < n; i++) {
        scanf("%d%d", &v, &w);
        int num = m / v;
        for (int j = 1; j <= num; j <<= 1) {
            num -= j;
            good.push_back(goods(j * v, j * w));
        }
        if (num) good.push_back(goods(num * v, num * w));
    }
    int len = good.size();
    for (int i = 1; i <= len; i++) {//01背包代码
        for (int j = 0; j <= m; j++) {
            dp[i][j] = dp[i - 1][j]; // 不选
            if (j >= good[i].v) dp[i][j] = max(dp[i][j], dp[i][j - good[i].v] + good[i].w); // 选
        }
    }
    printf("%d", dp[len][m]);
    return 0;
}
```

- 假设对于每个物品平均最多只能装num件，则这个代码的复杂度为O($mnlognum$)，这样的优化完全足够ac的，当然还能做的就是像01背包一样把二维优化成一维的空间优化，在这里直接把第一维未知的大小直接省去，达到一个非常大的空间优化效果，固01背包代码就要换成这样、答案最终为dp[m]

```cpp
for (int i = 1; i <= len; i++) {//01背包代码
    for (int j = m; j >= good[i].v; j--) {
        dp[j] = max(dp[j], dp[j - good[i].v] + good[i].w);
    }
}
```

## 多重背包

- 和01背包不同，01背包是每个物品都只有一个，但多重背包的每个物品都有若干个

### 【例题1】[AcWing 4. 多重背包问题 I](https://www.acwing.com/problem/content/4/)

- 题意不多讲，但是值得一提的是，多重背包被分为了三种等级，对应的数据范围不同，本题数据范围是三种之中最小的
- 分析：
  - 当成**完全背包**里的**分析方式2**一开始介绍的思路来做，每个物品有$s_i$个，只需改一下第三重循环便可
  - 下面的代码顺便把空间也优化了

  ```c++
  #include <cstdio>
  #include <algorithm>

  using namespace std;

  const int Maxn = 110;
  int n, m, dp[Maxn];

  int main () {
      scanf("%d%d", &n, &m);
      for (int i = 1; i <= n; i++) {
          int v, w, s;
          scanf("%d%d%d", &v, &w, &s);
          for (int j = m; j >= v; j--) {//01背包逆着来
              for (int k = 1; k <= s && k * v <= j; k++) {
                  dp[j] = max(dp[j], dp[j - k * v] + k * w);
              }
          }
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

  - 时间复杂度为O(mns)，速度比较慢，对于本题物品个数不超过100个，足够跑了
  
### 【例题2】[AcWing 5. 多重背包问题 II](https://www.acwing.com/problem/content/5/)

- 本题和【例题1】的题意是一样的，只不过数据范围直接大了一位数
- 分析：
  - 再用刚刚的思想完全是不够用的了，直接TLE了
  - 还记得**完全背包的二进制优化吗**吗，利用那个思想，就能优化成O($mnlogs$)的复杂度了
  - 具体代码如下

  ```c++
  #include <cstdio>
  #include <algorithm>
  #include <vector>

  using namespace std;

  struct goods{
    int v, w;
    goods(int v = 0, int w = 0) : v(v), w(w) {}
  };

  const int M = 2010;
  int dp[M], m, n;

  int main() {
      scanf("%d%d", &n, &m);
      for (int i = 1; i <= n; i++) {
          int v, w, s;
          scanf("%d%d%d", &v, &w, &s);
          vector<goods> good;
          for (int j = 1; j <= s; j <<= 1) {//二进制优化
              s -= j;
              good.push_back(goods(j * v, j * w));
          }
          if (s) good.push_back(goods(s * v, s * w));
          for (auto g : good) {//01背包
              for (int j = m; j >= g.v; j--) {
                  dp[j] = max(dp[j], dp[j - g.v] + g.w);
              }
          }
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

- 总结：通过多次用到了01背包的思想可以看出01背包的重要性

### 【例题3】[6. 多重背包问题 III](https://www.acwing.com/problem/content/description/6/)

- 题意和【例题1】也是一样，但这题的数据范围更大，达到了2e5
- 这题需要用到单调队列优化（单调队列是什么？自学吧）
- 仔细看下面我截取例题1时的代码的部分

```cpp
for (int j = m; j >= v; j--) {//01背包逆着来
    for (int k = 1; k <= s && k * v <= j; k++) {
        dp[j] = max(dp[j], dp[j - k * v] + k * w);
    }
}
```

- 不难发现$j$只会利用到$j - k \times v$来转化，这就意味着$j$只会和与$j(mod \ \ v)$同余的$dp$容量来转移，例如$j = 9, v = 2$则他只可能会利用容量为$7,5,3,1$的$dp$来转移
- 同时注意到第二重循环的第一个循环条件$k \le s$，这就意味着$j$利用到的与$j(mod \ \ v)$同余的容量的个数是有限的，不超过$s$个，例如$j = 9, v = 2, s = 2$时，则只可能会利用容量为$7,5$的$dp$来转移
- 综上，是不是觉得很像一个在固定窗口大小里面找一个最大值？
- 直接先看代码，然后再解刨

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;
const int M = 2e4 + 5, N = 1e3 + 5;

int dp[M], tmp[M], q[M];
int main() {
    int n, m;
    scanf("%d %d\n", &n, &m);
    for (int i = 1; i <= n; ++i) {
        int v, w, s;
        scanf("%d %d %d", &v, &w, &s);
        for (int i = 0; i <= m; ++i) tmp[i] = dp[i];
        for (int j = 0; j < v; ++j) { // 枚举余数
            int l = 0, r = -1; // 单调队列的左右指针
            for (int k = j; k <= m; k += v) { // 枚举容量
                if (l <= r && q[l] < k - s * v) ++l;// 窗口范围必须是[k - s * v, k - v]
                if (l <= r) dp[k] = max(tmp[k], tmp[q[l]] + (k - q[l]) / v * w);
                while (l <= r && tmp[q[r]] + (k - q[r]) / v * w <= tmp[k]) --r;
                q[++r] = k;
            }
        }
    }
    printf("%d\n", dp[m]);
    return 0;
}
```

- 下面对代码进行解刨
- 首先，由于我们之前说的$j$在只会利用到比他小的同余数，所以我们转移时必选顺着转移，不能再逆着来了，这时就要用到之前01背包说的第三种空间优化来达到顺着转移的目的了
- 目光移到第16行代码（枚举余数），把相同余数的容量同时一起转移，固第18行的循环中是`k += v`这样就保证了每次的余数都是`j`（当前解说的代码的`j`）
- 然后就是第18行循环的内部循环了，首先学过单调队列的同学都知道，我们为了方便处理队列，我们在队列内存的不是对应的值，而是下标（在这题下标就是容量）。前面说到我们窗口的大小是有限制的，不能超过`s`个，固对应下标的范围就是$[k - s\times v,k - v]$，固若`q[l] < k - s * v`则弹出队头元素
- 然后就是20行的简单易懂的更新操作，用上一个状态和当前选取`(k - q[l])/v`个当前物品的状态进行对比转移
- 21和22行都是单调队列的入队操作，队尾弹出的条件就是容量为`q[l]`的状态加上`(k - q[r]) / v * w`的价值都没有当前`k`容量的状态价值大（小于等于）

## 混合背包

- 混合背包就是有些物品只能选1次，有些物品最多只能选s次，有些物品无限次，分别对应01背包，多重背包，完全背包

### 【例题】[ 混合背包问题](https://www.acwing.com/problem/content/7/)

- 解决方法很简单，全部转化为01背包就好了，对于本来就是01背包的不作处理，对于多重背包的物品可以用二进制优化变成01背包，完全背包同理可以转化为最多只能选$\frac{m}{v}$次的多重背包后再用二进制优化
- 代码略

## 二维费用背包

- 就是一个多了体积限制的背包罢了

### 【例题】[ 二维费用的背包问题](https://www.acwing.com/problem/content/8/)

- 很简单，多加一维体积的数组，转移时和01背包相同
- 直接看代码就好了

```cpp
#include <cstdio>
#include <algorithm>

using namespace std;

const int Maxn = 110;
int n, V, M, dp[Maxn][Maxn];


int main () {
    scanf("%d%d%d", &n, &V, &M);
    for (int i = 1; i <= n; ++i) {
        int v, m, w;
        scanf("%d%d%d", &v, &m, &w);
        for (int i = V; i >= v; i--)
            for (int j = M; j >= m; j--) 
                dp[i][j] = max(dp[i][j], dp[i - v][j - m] + w);
    }
    printf("%d", dp[V][M]);
    return 0;
}
```

## 分组背包

- 分组背包就是有若干组物品，对于每组物品都有若干件物品，每组物品最多选一个物品，问你容量不超过c的能装的最大价值

### 【例题】[AcWing9. 分组背包问题](https://www.acwing.com/problem/content/description/9/)

- 题意不多说，就是一个赤果果的分组背包问题

- 分析：
  - 可以把每一组物品看成一个物品，然后当成01背包来做，不同的是对于每种容量的背包，都要考虑每组物品的每一个物品
  - 也可以这么理解，对于每组物品，都假设只有一个物品，这一组物品就是一个物品了，然后不就是01背包了吗

  ```c++ {.line-numbers}
  #include <cstdio>
  #include <algorithm>

  using namespace std;

  const int Maxn = 105;
  int n, m, dp[Maxn];

  int main () {
      scanf("%d%d", &n, &m);
      for (int i = 1 ; i <= n; i++) {
          int v[105], w[105], s;
          scanf("%d", &s);
          for (int j = 0; j < s; j++) {
              scanf("%d%d", &v[j], &w[j]);
          }
          for (int j = m; j >= 0; j--) {    //01背包
              for (int k = 0; k < s; k++) { //假设当前组只有这一个物品
                  if (j >= v[k]) dp[j] = max(dp[j], dp[j - v[k]] + w[k]);
              }
          }
      }
      printf("%d", dp[m]);
      return 0;
  }
  ```

  
  - 值得注意的是第18行和19行的代码是**不可以互换的**，因为一维01背包是从大到小遍历的，保持小的下标是之前的数据。若是调换过来就相当于把每件物品都看成了一件物品，而不是隶属于某一组的物品
  
- 还有一点很重要的就是分组背包的思想可以迁移到树形背包（有依赖的背包问题）的

## 有依赖的背包问题

- 就是一个树形背包，即如果要选一个结点，则其父亲节点必须要被选

### 【例题】[ 有依赖的背包问题](https://www.acwing.com/problem/content/10/)

- 核心：把一个结点的每个孩子结点看做不同的组，即可以当成分组背包来自，那么组内的物品是什么呢，答案就是每组的一个决策就是一个物品（就这题而言就是每一个dp容量值就是一个物品），注意：这个思想很重要，他是所有（应该是所有）树形背包dp的共同特点
- 先看代码再解刨

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define ll long long
#define pb emplace_back

using namespace std;
const int M = 105, N = 1e3 + 5, inf = 1e9;
vector<int> mp[M];
int root, n, m, dp[M][M], w[M], v[M], tmp[M];
int dfs(int u) {
    int sum = v[u]; // 记录本棵树的物品总重量
    dp[u][v[u]] = w[u];
    for (int son : mp[u]) {
        int siz = dfs(son);// 计算孩子节点的决策，并获取他的物品总重量
        for (int i = 0; i <= min(m, sum + siz); ++i) tmp[i] = dp[u][i]; // 还记得01背包的空间优化3吗
        for (int i = 0; i <= siz; ++i) { // 这棵孩子子树选取i容量的物品
            for (int j = v[u]; j <= sum; ++j) { // 之前组选取的容量j
                if (i + j > m) break; // 超过背包容量的不用转移
                dp[u][i + j] = max(dp[u][i + j], tmp[j] + dp[son][i]);
            }
        }
        sum += siz;
    }
    return sum; //返回本树的总容量
}
int main() {
    scanf("%d %d\n", &n, &m);
    for (int i = 1; i <= n; ++i) {
        int p;
        scanf("%d %d %d\n", &v[i], &w[i], &p);
        if (p != -1) mp[p].pb(i);//存树
        else root = i;
    }
    int ans = dfs(root);
    printf("%d\n", dp[root][min(ans, m)]);
    return 0;
}
```

- 核心就是dfs内的函数，就直接解刨里面的吧
- 先来解释`dp`的含义`dp[u][v]`表示在u结点这颗子树中选取容量为`v`的最大价值
- 13行不解释，14行的话很显然，由于要选本结点的子结点则本结点必选，所以就有了14行的初始化
- 然后是17行，这是之前在01背包中第三种空间优化的一种手段，这样做的好处有：1、更新本结点的`dp`时可以顺着来更新（好理解），2、可以防止不必要的转移（具体看第21行dp的第二维转移方式）且保证了整个复杂度不高于$O(nm)$，既保证了每个（点）都只会在题目的LCA上计算一次，否则可能会使复杂度退化到O($nm^2$)，后面会解释
- 然后就是转移思想可以参考[树形背包思考模型](https://blog.csdn.net/hao_6_6/article/details/114259329?spm=1001.2014.3001.5502)
- 接下来状态转移，先说说为什么这样转移，需要提前知道的是下面两个转移方程会导致复杂度不同

$$
dp[u][j] = max(dp[u][j], \ \ dp[son][i] + dp[u][j - i])  \tag{1}
$$

$$
dp[u][i + j] = max(dp[u][i + j],\ \  dp[son][i] + dp[u][j]) \tag{2}
$$



- 有经验的小伙伴可能知道，式子(1)是填表法，式子(2)是**刷表法**。对于树形背包问题有个很玄学的优化就是利用刷表法来更新当前结点，这样就能保证复杂度是O($nm$)而不是O($nm^2$)，我的理解就是刷表法保证了本结点的合法性，并且使得所有结点都只会在他们的LCA上被算一次。反观填表法在有些题目上十分难以拿捏本结点的合法性
- 固以上就是我选择18行到23内部循环的方式的原因
- 没了

## 背包问题求方案数

- 题意就是让我们求01背包最优解的方案

### 【例题】[ 背包问题求方案数](https://www.acwing.com/problem/content/11/)

- 多建立一个数组`f[i][j]`表示前`i`个物品选取容量为`j`的最优解方案数
- 注意初始化`f[0][j]`应当都初始化为1，因为是不超过m容量，所以这样初始化就保证了第一个物品转移有效
- 直接看代码

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <iostream>
#include <cctype>

#define ll long long
using namespace std;
const int M = 1e3 + 5, N = 1e3 + 5, inf = 1e9, mod = 1e9 + 7;
int n, m, dp[M][M];
ll f[M][M];
int main() {
    scanf("%d %d\n", &n, &m);
    for (int i = 0; i <= m; ++i) f[0][i] = 1;
    for (int i = 1; i <= n; ++i) {
        int v, w;
        scanf("%d %d", &v, &w);
        for (int j = 0; j <= m; ++j) {
            dp[i][j] = dp[i - 1][j]; // 不选
            f[i][j] = f[i - 1][j];  //不选
            if (j >= v) {
                if (dp[i - 1][j - v] + w == dp[i][j]) f[i][j] = (f[i][j] + f[i - 1][j - v]) % mod; //如果相等就相加
                if (dp[i - 1][j - v] + w > dp[i][j]) {
                    f[i][j] = f[i - 1][j - v]; // 否则更新值
                	dp[i][j] = dp[i - 1][j - v] + w;
                } 
            }
        }
    }
    printf("%lld\n", f[n][m]);
    return 0;
}
```

- 当然此题也可以空间优化

## 背包问题求具体方案

- 题意是让我们求01背包最优解的一个具体方案，并且输出字典序最小的那个

### 【例题】[背包问题求具体方案](https://www.acwing.com/problem/content/12/)

- 求解具体方案如果只是随便找，则直接在转移结束后倒过来找就好了，但是题目要求的是字典序最小，那么我们要在转移顺序上做一些改动，即从最后一个物品到第一个物品转移，然后再贪心找字典序最小的方案就好了
- 一下递归找方案个人比较喜欢

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

#define pb emplace_back
using namespace std;
const int M = 1e3 + 5, N = 1e3 + 5, inf = 1e9, mod = 1e9 + 7;
int n, m, dp[M][M], v[M], w[M];
vector<int> ans; //记录答案
void solve(int num, int m) {
    if (m == 0 || num == n + 1) {
        for (int& v : ans) printf("%d ", v);
        exit(0); //结束程序
    }
    if (m >= v[num] && dp[num][m] == dp[num + 1][m - v[num]] + w[num]) { // 如果符合转移
        ans.pb(num);//记录答案
        solve(num + 1, m - v[num]);
        ans.pop_back(); // 回溯
    }
    solve(num + 1, m);
}
int main() {
    scanf("%d %d", &n, &m);
    for (int i = 1; i <= n; ++i) scanf("%d %d", v + i, w + i);
    for (int i = n; i; --i) {
        for (int j = 0; j <= m; ++j) {
            dp[i][j] = dp[i + 1][j];
            if (j >= v[i]) dp[i][j] = max(dp[i][j], dp[i + 1][j - v[i]] + w[i]);
        }
    }
    solve(1, m);
    return 0;
}
```

- 注意，这题不能空间优化了

## 结语

- 写了好久好久，早就有把acwing上的背包总结博客写好的念头了，结果从开始计划到现在过了两三个月参完工，我太懒了。
- 这篇博客严格上来说其实并不是所有的背包问题的总结，还有其他背包中比较深究的问题，比如说求解背包问题求解次优解，第k优解等等比较深入的问题，太菜了没学。
- 就这样告一段落吧
