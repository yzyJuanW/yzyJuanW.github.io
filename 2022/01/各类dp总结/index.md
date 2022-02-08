# 各类dp总结


## 前言

这是一篇自己对各类动态规划和动态规划技巧的总结，未完待续，后续会一边刷题一边添加内容

## 关于填表法和刷表法

在我们做的题中，一般填表法用得较多，但两者各有好处

一般来说动态规划中，状态的转移可以理解成图论中的一条有向边($u \rightarrow v$)，我们以 `u` 作为已求出来的状态，`v` 为需要求的状态，那么填表法便是将目光放在 `v` 上，去寻找已知的 `u` 来更新；而刷表法则是将目光放在`u` 上，利用已知的 `u` 来去更新可抵达的 状态 `v`

具体来说填表法会写成这样 ：`dp[i] = dp[i - j] + w`

而刷表法会写成这样： `dp[i + j] = dp[i] + w`

填表法的**好处**不多说，毕竟平时用得最多的便是填表法

而刷表法的**好处**便是：一般对于一道题中，若是在求本状态时，寻找需要利用到的状态比较困难时，则可以考虑用刷表法

## 关于空间优化

动态规划中，真要考起动态规划来，感觉很少卡空间，但还是说说吧

一般我碰见比较常见的空间优化的方式有两种：

一种是状态转移时，**本状态的维度只用到了上一个状态的维度**，即 `dp[i][x] = dp[i - 1][y]` 这种形式，这样的优化方式有很多，例如开数组时将那一维开成2的大小，又例如直接把那一维删了

另一种**是多维度状态数组超内存**的情况，一般来说这种的特点是，有一维可以利用其它几维间接推出来，固优化方式是直接将那一维删了，然后转移时，那一维的信息直接利用其它维信息推出来。例题 [[NOIP2010]乌龟棋](https://ac.nowcoder.com/acm/contest/24213/1041) : 本题不能直接开5维度的空间

## 关于状态转移——tmp数组

为什么要总结这个？个人感觉这个真的很重要，在树形背包的应用上有**奇效**

tmp数组只是我自己的叫法，具体怎么用得慢慢解释

对于上述所说的 "本状态的维度只用到了上一个状态的维度" 的空间优化，有人是这么处理的

```cpp
int dp[2][N]; //  声明
for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
        dp[i & 1][j] = max(dp[i & 1][j], dp[!(i & 1)][j - x] + y);
    }
}
```

但如果使用tmp数组则可以这样，有两种方式

```cpp
int dp[N], tmp[N]; // 声明
/* 方式一 */
for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) tmp[j] = dp[j]; // 先复制，记录没转移前的状态
    for (int j = 1; j <= m; ++j) {
        dp[j] = max(dp[j], tmp[j - x] + y); // 在转移
    }
}
/* 方式二 */
for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) tmp[j] = -inf; // 先初始化
    for (int j = 1; j <= m; ++j) {
        tmp[j] = max(tmp[j], dp[j - x] + y); // 再转移
    }
    for (int j = 1; j <= m; ++j) dp[j] = tmp[j]; // 最后复制回dp数组里面
}
```

使用tmp数组的好处是思路清晰，处理简单

注意，方式一和方式二各有好处，不同题目的初始条件不同两者的效果可能会不同

## 关于更新状态

寻常来说我们更新状态直接利用递推便可

但有些题目最终利用到的状态并没有多少，但直接递推又不好找转移的状态，并且直接递推复杂度可能顶不住

固一般来说这样的状态转移可以考虑记忆化搜索，类似剪枝一般在寻找状态时只搜索自己需要用到的状态，同时，有时状态可能是无限的，也可能超出了数组的下标承受范围，则可以考虑使用map存储状态

## 关于时间复杂度

一般来说，我们dp的时间复杂度可以从空间大小来推测，因为一般我们的状态若是几乎都要遍历一遍的话，其复杂度的下限便是其空间的大小

## 关于寻找具体方案

对于这种题目，求最后的答案时可以像寻常般进行dp，而反过头来求出答案来源的具体方案我一般是dfs往回找，时间负责度也不高

对于求解对字典序有要求的具体方案，我会反过来dp，然后正过来dfs找具体方案

当然，也看过别的求解具体方案的方式：多开一个数组记录转移的路线，然后像是遍历图一样找路线

## 线性dp

#### 线性dp——背包

对于背包真的不想多说了，刷多了自然有就感觉了，我的《背包问题》这篇博客我也总结得不少了

###### 例题

- [AcWing 2. 01背包问题](https://www.acwing.com/problem/content/2/)  01背包入门题

- [AcWing 3. 完全背包问题](https://www.acwing.com/problem/content/3/)   完全背包入门题

- [AcWing 4. 多重背包问题 I](https://www.acwing.com/problem/content/4/)  多重背包入门题

- [AcWing 5. 多重背包问题 II](https://www.acwing.com/problem/content/5/) 多重背包进阶题

- [AcWing 6. 多重背包问题 III](https://www.acwing.com/problem/content/6/) 多重背包困难题

- [AcWing 7. 混合背包问题](https://www.acwing.com/problem/content/7/) 混合背包

- [AcWing 8. 二维费用的背包问题](https://www.acwing.com/problem/content/8/) 二维背包

- [AcWing 9. 分组背包问题](https://www.acwing.com/problem/content/9/) 分组背包

- [AcWing 11. 背包问题求方案数](https://www.acwing.com/problem/content/11/) 背包问题求方案数

- [AcWing 12. 背包问题求具体方案](https://www.acwing.com/problem/content/12/) 背包问题求具体方案，可以试试上面说的求具体方案的方法

- [NC23413 小A买彩票](https://ac.nowcoder.com/acm/problem/23413) 一个简单的背包问题

- [NC14526 购物](https://ac.nowcoder.com/acm/problem/14526) 01背包+分组背包

- [NC207751 牛牛的旅游纪念品](https://ac.nowcoder.com/acm/problem/207751) 背包问题入门题

- [NC16693 [NOIP2001]装箱问题](https://ac.nowcoder.com/acm/problem/16693) 裸的01背包

- [P1048 [NOIP2005 普及组] 采药](https://www.luogu.com.cn/problem/P1048) 裸的01背包

- [NC17871 CSL分苹果](https://ac.nowcoder.com/acm/problem/17871) 转换成01背包写

- [P1064 [NOIP2006 提高组] 金明的预算方案](https://www.luogu.com.cn/problem/P1064) 我是当成有依赖的背包写的，但也有人用线性方式的dp写出来了，所以放这吧

- [NC14699 队伍配置](https://ac.nowcoder.com/acm/problem/14699) 多维背包的入门题

- [[USACO09DEC]Video Game Troubles](https://www.luogu.com.cn/problem/P2967) 分组背包+01背包，转移用两个方程

- [NC17193 简单瞎搞题](https://ac.nowcoder.com/acm/problem/17193) 分组背包用bitset优化

- [NC16576 [NOIP2012]摆花](https://ac.nowcoder.com/acm/problem/16576) 分组背包

- [P5020 [NOIP2018 提高组] 货币系统](https://www.luogu.com.cn/problem/P5020) 完全背包的应用

- [P1877 [HAOI2012]音量调节](https://www.luogu.com.cn/problem/P1877) 简单背包问题

- [P4158 [SCOI2009]粉刷匠](https://www.luogu.com.cn/problem/P4158) 划分dp + 分组背包，有点进阶

- [CodeForces 755F PolandBall and Gifts](https://codeforces.com/problemset/problem/755/F) 多重背包，说实话有点毒瘤

- [CodeForces - 864E Fire](https://codeforces.com/problemset/problem/864/E) 贪心+01背包

- [CodeForces - 366C](https://codeforces.com/problemset/problem/366/C) 思维+01背包，有点进阶

- [atcoder Knapsack 2](https://atcoder.jp/contests/dp/tasks/dp_e) 01背包+换意dp

- [atcoder Candies](https://atcoder.jp/contests/dp/tasks/dp_m) 多重背包，需要一些优化手段

- [atcoder Tower](https://atcoder.jp/contests/dp/tasks/dp_x) 思维+01背包，有点思维性，多想想

#### 其他线性dp

线性dp是最基础的dp，其他dp可以说是他的延伸，线性dp难起来一般的难点是转移上

线性dp考点有很多，包括空间优化，状态转移和定义，寻找具体方案等

多刷题才是王道

###### 例题

- [NC20875 舔狗舔到最后一无所有](https://ac.nowcoder.com/acm/problem/20875) 这题的转移有点意思

- [NC20650 可爱の星空](https://ac.nowcoder.com/acm/problem/20650) 这题的转移注意状态的空间，建议使用dfs找状态

- [NC51216 花店橱窗](https://ac.nowcoder.com/acm/problem/51216) 这题感觉像是背包+数字三角形的模型，还要求具体方案

- [NC16850 免费馅饼](https://ac.nowcoder.com/acm/problem/16850) 加个时间轴作为状态的一维，然后像数字三角形一样走，反过来遍历就好了，然后正过来找具体方案

- [NC16856 钉子和小球](https://ac.nowcoder.com/acm/problem/16856) 这题有点好玩，初始状态有点意思

- [NC16619 [NOIP2008]传球游戏](https://ac.nowcoder.com/acm/problem/16619) 很简单的线性dp

- [NC16664 [NOIP2004]合唱队形](https://ac.nowcoder.com/acm/problem/16664) 算LIS的入门题吧

- [[NOIP1999] 拦截导弹 ](https://www.luogu.com.cn/problem/P1020) 相信不少人做过这题，Dilworth定理的应用

- [NC15553 数学考试](https://ac.nowcoder.com/acm/problem/15553) 算划分dp吧

- [NC19158 失衡天平](https://ac.nowcoder.com/acm/problem/19158) 这种转移也有点意思，没遇到过可能真要想挺久的	

- [NC13885 Music Problem](https://ac.nowcoder.com/acm/problem/13885) 要用到bitset优化，想想取余的加减法对应的操作吧

- [NC14704 美味菜肴](https://ac.nowcoder.com/acm/problem/14704) 先贪心，后dp，最后找最优值

- [NC207781 迁徙过程中的河流](https://ac.nowcoder.com/acm/problem/207781) 贪心思维的dp

- [[NOIP2008 提高组] 传纸条](https://www.luogu.com.cn/problem/P1006) 建议记忆化搜索，可以空间优化

- [P1004 [NOIP2000 提高组] 方格取数](https://www.luogu.com.cn/problem/P1004) 和传纸条差不多

- [P1052 [NOIP2005 提高组] 过河](https://www.luogu.com.cn/problem/P1052) 空间优化，转移找状态有点恶心

- [NC16590 乌龟棋](https://ac.nowcoder.com/acm/problem/16590) 需要空间优化

- [NC50959 To the Max](https://ac.nowcoder.com/acm/problem/50959) 前缀和与最大连续子串的应用

- [P1169 [ZJOI2007]棋盘制作](https://www.luogu.com.cn/problem/P1169) 思维+最大全为1的正方形+单调栈的应用

- [P2331 [SCOI2005]最大子矩阵](https://www.luogu.com.cn/problem/P2331) 状态有点难想，也有点难转移

- [AcWing 431. 守望者的逃离](https://www.acwing.com/problem/content/433/) 转移挺有意思的，记住特判条件就好了

- [[USACO 2008 Jan S]Running](https://ac.nowcoder.com/acm/problem/24949) 刷表法的应用

- [P2051 [AHOI2009]中国象棋](https://www.luogu.com.cn/problem/P2051) 要结合组合数学转移

- [atcoder Grid 2](https://atcoder.jp/contests/dp/tasks/dp_y) 需要组合数学和走格子的一个数学小结论

## 区间dp

一种具有区间性质的dp，一般来说转移都是从小区间转移到大区间，固一般是先枚举小区间再枚举大区间的即 `dp[l][r] <- dp[l + 1][r - 1]`

有两种遍历方式可以做到

```cpp
//1 比较常见的
for (int len = 1; len <= n; ++len) {
    for (int l = 1, r = l + len - 1; r <= n; ++l, ++r) {
        // 转移
    }
}
//2 可以反向l只会找l+1转移，r只会找r-1转移，固可以l逆着遍历，r顺着遍历
for (int l = n; l >= 1; --l) {
    for (int r = l; r <= n; ++r) {
        // 转移
    }
}
```

两种效果差不多

对于环形的区间dp，一般的处理方式是拆环成链，开两倍的空间

###### 例题

- [NC14701 取数游戏2](https://ac.nowcoder.com/acm/problem/14701) 区间dp入门题

- [NC15447 wyh的问题](https://ac.nowcoder.com/acm/problem/15447) 区间dp题，没什么好说的

- [石子合并](https://ac.nowcoder.com/acm/problem/50493) 经典

- [凸多边形的划分](https://ac.nowcoder.com/acm/problem/50500) 经典

- [P4170 [CQOI2007]涂色](https://www.luogu.com.cn/problem/P4170) 区间dp，感性转移

- [NC16129 小小粉刷匠](https://ac.nowcoder.com/acm/problem/16129) 和涂色差不多，多了个限制条件

- [NC23501 小A的回文串](https://ac.nowcoder.com/acm/problem/23501) 拆环成链

- [NC227595 跳跳跳](https://ac.nowcoder.com/acm/problem/227595) 区间dp，也不难想

- [NC13230 合并回文子串](https://ac.nowcoder.com/acm/problem/13230) 如果能想到是区间dp就挺简单的了

- [P3147 [USACO16OPEN]262144 P](https://www.luogu.com.cn/problem/P3147) 区间dp+换意dp

- [P1005 [NOIP2007 提高组] 矩阵取数游戏](https://www.luogu.com.cn/problem/P1005) 普普通通的区间dp

- [P1040 [NOIP2003 提高组] 加分二叉树](https://www.luogu.com.cn/problem/P1040) 需要一点树的中序和前序遍历的知识

- [HDU 2476 String painter](https://acm.dingbacode.com/showproblem.php?pid=2476) 和涂色差不多，需要预处理，然后来个区间dp，最后划分dp

- [UVA 10617 Again Palindrome](https://vjudge.net/problem/UVA-10617) 求区间中有多少个回文子序列，需要容斥一下

- [POJ 1159 Palindrome](http://poj.org/problem?id=1159) 需要空间优化

- [atcoder Deque](https://atcoder.jp/contests/dp/tasks/dp_l) 思维+区间dp

## 树上dp

个人感觉，树上dp和树上背包是不一样的，固分开来写

树形dp简单的来说就是在树上进行dp

但有些模型个人觉得还是必须要去了解的

例如

- 树上独立集
- 树的最小支配集
- 树的最小点覆盖
- 树的直径
- 树的重心
  - 性质如下
  - 一棵树最少有一个重心，最多有两个重心，若有两个重心，则他们相邻（即连有直接边）
  - 树上所有点到某个点的距离和里，到重心的距离和最小；若有两个重心，则其距离和相同
  - 若以重心为根，则所有子树的大小都不超过整棵树的一半
  - 在一棵树上添加或删除一个叶子节点，其重心最多平移一条边的距离
  - 两棵树通过连一条边组合成新树，则新树重心在原来两棵树的重心的连线上
- 树的中心
- ……

当然还有比较常见的便是换根dp和基环树dp

对于换根dp，换根时个人觉得还是多开一个数组进行记录需要的答案比较好。一般来说思考换根的公式是画图和进行未换根前的公式转换

###### 例题

- [Libre 10157 「一本通 5.2 例 5」皇宫看守](https://loj.ac/p/10157) 树上最小支配集

- [NC51178 没有上司的舞会](https://ac.nowcoder.com/acm/problem/51178) 树上最大独立集

- [Libre 10156 「一本通 5.2 例 4」战略游戏](https://loj.ac/p/10156) 树上最小点覆盖

- [Libre 10159 「一本通 5.2 练习 2」旅游规划](https://loj.ac/p/10159) 树的直径+具体方案

- [POJ 1655 Balancing Act](http://poj.org/problem?id=1655) 树的重心

- [NC202475 树上子链](https://ac.nowcoder.com/acm/problem/202475) 有点像树的直径

- [NC211219 电话网络](https://ac.nowcoder.com/acm/problem/211219) 树上最小支配集

- [NC22598 Rinne Loves Edges](https://ac.nowcoder.com/acm/problem/22598) 直接树上dp就行了

- [P4268 [USACO18FEB]Directory Traversal](https://www.luogu.com.cn/problem/P4268) 换根dp

- [P2986 [USACO10MAR] Great Cow Gathering](https://www.luogu.com.cn/problem/P2986) 换根dp

- [NC51180 Accumulation Degree](https://ac.nowcoder.com/acm/problem/51180) 换根dp

- [NC51222 Strategic game](https://ac.nowcoder.com/acm/problem/51222) 树上最小点覆盖

## 树形背包

树形背包，顾名思义是在树上的或者说是有依赖的背包问题

对于思考方式可以看看之前写的《树形背包思考方式》

一般困难点便是转移的思考、如何定义状态、边和点权哪个是物品重量等

当然还有一个进阶考法是利用**虚树**进行优化

###### 例题

- [AcWing 10. 有依赖的背包问题](https://www.acwing.com/problem/content/10/) 入门题

- [Libre 10154. 「一本通 5.2 例 2」选课](https://loj.ac/p/10154) 入门级别吧

- [Libre 10153. 「一本通 5.2 例 1」二叉苹果树](https://loj.ac/p/10153) 入门级别

- [NC20811 蓝魔法师](https://ac.nowcoder.com/acm/problem/20811) 要学会思考什么是背包模型中的物品重量

- [P1273 有线电视网](https://www.luogu.com.cn/problem/P1273) 树形背包+换意dp

- [P1272 重建道路](https://www.luogu.com.cn/problem/P1272) 这题也不难

- [P3177 [HAOI2015]树上染色](https://www.luogu.com.cn/problem/P3177) 注意背包模型中的物品是什么

- [HDU6540 Neko and tree](http://acm.hdu.edu.cn/showproblem.php?pid=6540) 进阶题

- [P3354 [IOI2005]Riv 河流](https://www.luogu.com.cn/problem/P3354) 有点难

## 状压dp

状态压缩一般指的是利用二进制进行状态的压缩，很少出现三进制这些

当然状态压缩dp常见的考点便是哈密顿通路的模型

注意一个二进制状态寻找子集的搜索方法，其复杂度是O($3^n$)的

```cpp
for (int s1 = s; s1; s1 = (s1 - 1) & s) {
    int s2 = s ^ s1;
    // s1 与 s2 互为 s 的补集，且s1与s2不会重复，即利用s1与s2可以不重不漏地枚举完所有子集
}
```

###### 例题

- [AcWing 292. 炮兵阵地](https://www.acwing.com/problem/content/294/) 基础题
- [P1896 [SCOI2005]互不侵犯](https://www.luogu.com.cn/problem/P1896) 入门题
- [NC17890 方格填色](https://ac.nowcoder.com/acm/problem/17890) 要用快速幂优化
- [NC210981 mixup2 混乱的奶牛](https://ac.nowcoder.com/acm/problem/210981) 算是基础题吧
- [AcWing 291. 蒙德里安的梦想](https://www.acwing.com/problem/content/293/) 状态dp，个人觉得轮廓线的思想来转移会比较好
- [NC15832 Most Powerful](https://ac.nowcoder.com/acm/problem/15832) 好好思考状态
- [P1879 [USACO06NOV]Corn Fields](https://www.luogu.com.cn/problem/P1879) 基础题
- [NC16122 郊区春游](https://ac.nowcoder.com/acm/problem/16122) 本质是求一个哈密顿最小路径的通路
- [NC16544 简单环](https://ac.nowcoder.com/acm/problem/16544) 类似求一个哈密顿回路的方案数
- [P3118 [USACO15JAN]Moovie Mooving](https://www.luogu.com.cn/problem/P3118) 思考状态存什么
- [P3092 [USACO13NOV]No Change](https://www.luogu.com.cn/problem/P3092) 要二分优化
- [atcoder Matching](https://atcoder.jp/contests/dp/tasks/dp_o) 二分图的完美匹配方案数
- [atcoder Grouping](https://atcoder.jp/contests/dp/tasks/dp_u) 你需要学会枚举子集
- [ [CQOI2012]局部极小值](https://www.luogu.com.cn/problem/P3160) 状压dp+容斥（难度较高）

## 数位dp

说起数位dp，求解的过程更像是在树上计数一样

值得注意的是：记忆化递归求解简单易懂，而且几乎不会卡递归的方式

给出递归时的一个套路代码（很多地方更多的是希望根据题意来）

有时候有些数位dp可能对前导0或者前面填的数对后面有影响，则可以在dfs中传参传多几个标志，然后在更新res（12行）时特判就好了

当然，个人感觉核心代码就是11~13行内部的转移方式，一般来说，写题的时候有点纠结状态怎么定义时，会先写这部分

```cpp
int num[100], dp[100];

int dfs(int indx, int limit, /*参数根据题意来添加*/) {
    if (indx == 0) {
        return 1;// 根据题意来返回
    }
	int &ref = dp[indx];
    if (!limit && ref != -1) return ref;
    int res = 0;
    int up = (limit ? num[indx] : 9);
    for (int i = 0; i <= up; ++i) {
        // 更新res
		dfs(indx - 1, limit && i == up);
    }
    if (!limit) ref = res;
    return res;
}

int solve(int x) {
    if (!x) return 1; // 根据题意来决定返回值
    int len = 0;
    while (x) {
        num[++len] = x % 10;
        x /= 10;
    }
    return dfs(len, 1);
}
int main() {
    memset(dp, - 1, sizeof dp); // 根据题意决定初始化的时机和大小
    // 此行省略读入
    cout << solve(r) - solve(l - 1) endl;
}
```

###### 例题

- [Libre 10164. 「一本通 5.3 例 2」数字游戏](https://loj.ac/p/10164) 入门好题
- [Libre 10166. 「一本通 5.3 练习 1」数字游戏](https://loj.ac/p/10166) 和上一题处理差不多
- [[SCOI2009] windy 数](https://www.luogu.com.cn/problem/P2657) 多少人的刚学是做的这题？
- [atcoder abc161D Lunlun Number](https://atcoder.jp/contests/abc161/tasks/abc161_d) 不说应该也能想到怎么做的题
- [CodeForces 204A Little Elephant and Interval](https://codeforces.com/problemset/problem/204/A) 这题让我吹爆记忆化搜的数位dp，处理前导0的形式值得练习
- [CodeForces 1143B Nirvana](https://codeforces.com/contest/1143/problem/B) 这题虽然可以不用数位dp做，但可以当做数位dp的练手题，可以练习一下处理前导0的形式
- [And and Pair](https://nanti.jisuanke.com/t/42578) 19年南昌icpc的C题，重现的时候自己是组合数学写出来的，但这题确实可以用数位dp写，注意是两个限制维度
- [Sum of Log](https://ac.nowcoder.com/acm/contest/9925/C) 20年上海icpc的C题，也是两个限制维度限制的数位dp

~持续更新中……


