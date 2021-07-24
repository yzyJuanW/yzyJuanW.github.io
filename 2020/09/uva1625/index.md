# UVA1625


## [Color Length](https://vjudge.net/problem/UVA-1625)

- 题意：给你两个长度分别为$n、m(1 \le n, m \le 5000)$的字符串，让你求出组成新串的最小$l(c)$（具体组成方式看原题）
- 个人感觉这题很难，一度怀疑自己的能力和紫书题目的难易分布，最后费了九牛二虎之力终于想通为什么了，所以本文可能会花大篇幅来解释这题是怎么想的
- 思路：一开始看着有点LCS的影子，想着想着就写出了DP的递归函数，但是却死活想不出下一步怎么递归，即如下代码的$[]$片段

```c++ {.line-numbers}
int DP(int a, int b) {
    if (vis[a][b]) return dp[a][b];
    vis[a][b] = true;
    int &res = dp[a][b];
    return res = min(DP(a - 1, b) + [], DP(a, b - 1) + []);
}
```

- 显然，可以猜测dp方程是

$$
dp[i][j] = min\begin{cases}
dp[i - 1][j] + ?_1\\ \\
dp[i][j - 1] + ?_2\\
\end{cases}
$$

- 想了好久终于还是去看了别人的博客题解，没怎么看懂，于是看了许多人的博客题解，大概知道了一件事：这题的难点就在于求 $?_1 和 \ \ ?_2$，即需要预处理就出这个 $?_1 和 \ \ ?_2$
- 先来说说这个 $?_1 和 \ \ ?_2$到底是什么东西
  - 首先，如果我们暴力直接来求这个 $?_1 和 \ \ ?_2$，，即每转移一次就求一次 $?_1 和 \ \ ?_2$，这样的话不仅麻烦，而且还不好想，最重要的是时间复杂度太大了
  - 那么如何来理解这个转移所需要的 $?_1 和 \ \ ?_2$呢？这需要先了解dp代表的是什么意思：
    - **$dp[i][j]$代表的是：从字符串1中选取了前i个字符和字符串2中选取了前j个字符拼接成的字符产生的总$l(c)$值的最小值**
    - 举个例子，拿题目中给的**GBBY**和**YRRGB**，如果在字符串1中选取了G，从字符串2中选取了Y的话，则$dp[1][1]$就是1，代表G和Y在**这种情况下**（注意这个“这种情况下”，意味着如果换了两个只有第一个字符相同的字符串的话$dp[i][j]$的值可能就会不一样，这个很重要，现在先不说明，下面在解释 $?_1 和 \ \ ?_2$时会用上）的最小总$l(c)$为1
    - 解释一下为什么是1，因为Y和G都还没有全部从字符串1和2中全部拿出来，即在字符串1和2中都还有未拿出来拼接的Y和G，则当前拼接成的字符串YG对未来拼接影响$l(c)$最小是$1(Y) + 0(G) = 1$（拼成GY的dp也是一样的值，即$1(G) + 0(Y) = 1$）
    - 如果字符串2是选取了前3个即YRR的话，则最小的总$l(c)$值就是4，即$dp[1][3] = 4$，具体拼接方式是YRRG，因为R字母已经全部抽取了出来所以其$l(c) = 1$，总值就是$4 = 1(R) + 3(Y) + 0(G)$
    - 可能很难懂，没关系，当理解了 $?_1 和 \ \ ?_2$后可以再回来看这个dp的含义
  - 说了这么多还是每开始说明 $?_1 和 \ \ ?_2$是什么东西，别着急，还需要几个要理解的：
    - 用紫书上的说法就是：“**当把一个颜色移到最终序列前，需要把所有“已经出现但还没结束”的颜色的$l(c)$值加1。更进一步地，因为并不关心每个颜色的$l(c)$，所以只需要知道有多少种颜色已经开始但尚未结束。**”
    - 一开始我就是一直在纠结怎么记录每种颜色在之前的拼接中放的位置，卡了很久
    - 举个例子来说明紫书上的意思：还是那个题目中给的**GBBY**和**YRRGB**，假如现在已经拼接成了GY，则$dp[1][1] = 1$，接下来再从字符串2中抽出个R，则拼成的结果的$dp[1][2] = 3$（假如拼接成的是GYR），则分别加的两个值就是G加了1，和Y加了1，因为加了一个字母R导致字符串的长度加了1并且G和Y都没全部抽取出来，固都需要加一
    - 若再从字符串2中抽取出R来的话，$dp[1][3] = 4$，可能有人会疑惑为什么是4而不是6，因为R和Y和G都要加1，总加的值就是3，所以答案不应该是$3 + 3 = 6$吗?，先冷静，还记得吗，dp的含义。我们拼接的应该是YRRG的样子才是最小值，这个时候R已经全部抽出来了，所以R值不用加1，完全可以计算他本身的$l(c)$值，所以总的$dp[1][3] = 3(Y) + 1(R) + 0(G) = 4$
    - 已经懂的可以跳过这一段的，原谅我再啰嗦一下，假如再从字符串2抽取一个G，则$dp[1][4] = 4(Y) + 1(R) + 1(G) = 6$，即G和R都已经全部抽取出来了，要加一的是Y，而G和R只需计算自身的$l(c)$就行了
    - 发现没有至始至终Y的$l(c)$是一直在加一的，而其他的由于中途已经全部抽取出来了，固没有加一而是计算自身的$l(c)$值
  - 到这里就可以解密?是什么了：$add[i][j]$代表从字符串抽取了前i个字符和从字符串抽取了前j个字符中**已经出现但还没有全部抽取出来的字符有多少个**
  - 因为只有**已经出现但还没有全部抽取出来的字符**在我们不断地拼接新的字符的时候才要加一
  - 关于$add[i][j]$的预处理转移方式：
    - 首先是要记录每个字符在两个字符串中的开始和结束位置，这个目的是知道当前字符是不是**已经出现但还没有全部抽取出来的字符**
    - 对于$add[i][j]$当$i > 1$并且$j > 1$时，其可以从两个方面转移过来，即$add[i][j - 1]$和$add[i - 1][j]$，可以这么想：从两个字符串中分别抽取前i、j个字符的前一个状态可以是**从前两个字符串中分别抽取前i - 1、j**或者**前i、j - 1个字符**的状态，对应分只要考虑字符串1的第i个字符是不是已经抽取完了或者字符串2的第j个字符是不是已经抽取完了，都是可以的，代码片段只会从一种中计算出来，而不是两种计算方式都用上~~我要最求速度~~
    - 如果如果当前新拼接的字符是**已经出现但还没有全部抽取出来的字符**，则$add[i][j]$就自加一，如果是**已经出现并且全部都抽取出来的字符**，则$add[i][j]$就自减一
    - 具体结果就是如图：
  ![uva1625](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC紫书动态一六二五.png)
  - dp前对add的预处理代码：

  ```c++
  struct strings{
      int s, t;
      strings(int s = INF, int t = 0) : s(s), t(t) {}
  };

  void init(char *a, char *b) {
      int n = strlen(a + 1), m = strlen(b + 1);
      strings aa[27], bb[27];
      for (int i = 1; i <= n; i++) {//记录每个字符在字符串1中的开始位置和结束位置
          strings &t = aa[a[i] - 'A'];
          t.s = min(t.s, i), t.t = i;
      }
      for (int i = 1; i <= m; i++) {//记录每个字符在字符串2中的开始位置和结束位置
          strings &t = bb[b[i] - 'A'];
          t.s = min(t.s, i), t.t = i;
      }
      for (int i = 0; i <= n; i++) {
          for (int j = 0; j <= m; j++) {
              add[i][j] = 0;
              bool ok = true;//就计算一次add[i][j]
              if (i) {
                  add[i][j] = add[i - 1][j];
                  int x = a[i] - 'A';
                  if (aa[x].s == i && bb[x].s > j) add[i][j]++;//如果是已经出现但还没有全部抽取出来的字符
                  if (aa[x].t <= i && bb[x].t <= j) add[i][j]--;//如果是已经出现并且全部都抽取出来的字符
                  ok = false;
              }
              if (ok && j) {
                  add[i][j] = add[i][j - 1];
                  int x = b[j] - 'A';
                  if (bb[x].s == j && aa[x].s > i) add[i][j]++;
                  if (bb[x].t <= j && aa[x].t <= i) add[i][j]--;
              }
              // printf("%d ", add[i][j]);
          }
          // puts("");
      }
      // printf("%d", aa['R' - 'A'].t);
  }
  ```

- 接下来就是dp的转移方程了

$$
dp[i][j] = min\begin{cases}
dp[i - 1][j] + add[i - 1][j], \ \ i > 0 \ \ ①\\ \\
dp[i][j - 1] + add[i][j - 1], \ \ j > 0 \ \ ② \\
\end{cases}
$$

- 注意①式和②式代表的含义是不同的，其中①式代表**抽取前i、j个字符的之前状态时抽取前i - 1、j个字符的状态**，即第i个字符一定是放在拼接后的最后一位，②式同理，给个转移图体会体会

![uva1625](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC紫书一六二五.png)

- 还不懂的可以草稿纸模拟一下，从上面介绍dp内涵开始重看
- 最终ac代码

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int M = 5e3 + 10, INF = 0x3f3f3f3f;
int add[M][M], dp[M][M];

struct strings{
    int s, t;
    strings(int s = INF, int t = 0) : s(s), t(t) {}
};


void init(char *a, char *b) {
    int n = strlen(a + 1), m = strlen(b + 1);
    strings aa[27], bb[27];
    for (int i = 1; i <= n; i++) {
        strings &t = aa[a[i] - 'A'];
        t.s = min(t.s, i), t.t = i;
    }
    for (int i = 1; i <= m; i++) {
        strings &t = bb[b[i] - 'A'];
        t.s = min(t.s, i), t.t = i;
    }
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= m; j++) {
            add[i][j] = 0;
            bool ok = true;
            if (i) {
                add[i][j] = add[i - 1][j];
                int x = a[i] - 'A';
                if (aa[x].s == i && bb[x].s > j) add[i][j]++;
                if (aa[x].t <= i && bb[x].t <= j) add[i][j]--;
                ok = false;
            }
            if (ok && j) {
                add[i][j] = add[i][j - 1];
                int x = b[j] - 'A';
                if (bb[x].s == j && aa[x].s > i) add[i][j]++;
                if (bb[x].t <= j && aa[x].t <= i) add[i][j]--;
            }
            // printf("%d ", add[i][j]);
        }
        // puts("");
    }
    // printf("%d", aa['R' - 'A'].t);
}


int DP(char *a, char *b) {
    int n = strlen(a + 1), m = strlen(b + 1);
    dp[0][0] = 0;
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= m; j++) {
            if (i) {
                dp[i][j] = dp[i - 1][j] + add[i - 1][j];
            }
            if (j) {
                if (i) dp[i][j] = min(dp[i][j], dp[i][j - 1] + add[i][j - 1]);
                else dp[i][j] = dp[i][j - 1] + add[i][j - 1];
            }
        }
    }
    return dp[n][m];
}


void solve(){
    char a[M], b[M];
    gets(a + 1), gets(b + 1);
    init(a, b);
    printf("%d\n", DP(a, b));
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    int n;
    scanf("%d", &n);
    getchar();
    while (n--) solve();
    return 0;
}
```

