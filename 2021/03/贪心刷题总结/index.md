# 贪心刷题总结


## 前言

- 终于开始刷贪心了，感觉cf很多题都喜欢出贪心，所以决定也练练
- 贪心貌似也有挺系统的分类和一些经典例题，把这些刷了应该就有提升了把

## 总结

- 贪心是指将问题通过当前最优解来求解得到最终的最优解，即局部最优解可以推出总体最优解。
- 给我的感觉就是一种思维题，通过一定技巧把题目旁敲侧击地解决了，但是如果没接触过或者想不出来，基本就很难了，但是贪心的代码实现并不长，所以说贪心即使很基础的算法也是很难的算法。
- 总得来说贪心算法难就难在每道题的贪心策略都不同，是否能贪心也很难看出来。
- 一般在解决贪心题的时候可以把他放到生活中的例子来，想想如果是生活中碰到这题，你会怎么解决让自己最舒服。

## 普通贪心

- [例题1：HDU 2111 Saving HDU](http://acm.hdu.edu.cn/showproblem.php?pid=2111)
  
  - 题意：你有可以装k个宝贝容量的包，现在给你一些宝贝，每个宝贝价值p，有m个，问你最多可以装多少价值的宝贝
  - 思路：将价值排序，先选大的，选到v = 0为止
  - 代码如下
  
  ```cpp
  #include <algorithm>
  #include <cstdio>
  #include <utility>
  #include <vector>
  using namespace std;
  
  int n, v;
  
  int main() {
      while (scanf("%d", &v), v) {
          scanf("%d", &n);
          vector<pair<int, int>> q;
          for (int i = 0; i < n; i++) {
              int p, m;
              scanf("%d%d", &p, &m);
              q.push_back(make_pair(p, m));
          }
          sort(q.begin(), q.end());
          int i = n - 1, ans = 0;
          while (i >= 0 && v) {
              if (q[i].second < v) {
                  ans += q[i].first * q[i].second;
                  v -= q[i].second;
              } else {
                  ans += q[i].first * v;
                  v = 0;
              }
              i--;
          }
          printf("%d\n", ans);
      }
  }
  ```
  
  
  
- [例题2：HDU 3348](http://acm.hdu.edu.cn/showproblem.php?pid=3348)
  
  - 题意：你有1、5、10、50、100的钞票，给你各个钞票的张数，问你要凑齐某价格最多和最少需要多少张，如果凑不出来输出-1
  - 思路：最少的话就从大面值的钞票开始贪心，如果最后剩余需要凑的钱不为0，则凑不齐，至于最多的话，贪心可以这样想：要凑用得最多，那言外之意就是我剩余得最少，所以可以凑最少的贪心策略去凑所有钞票的总价格 - 需要凑的价格，最后剩下的钞票数就是需要凑的价格，固剩下的钞票数量就是最多的答案
  - 总结：这道题的寻找最多的那个答案，一开始死活想不出来，后来看了大佬的思路恍然大悟，所以有所感悟：有时候一个问题顺着想可能很难，那不妨试试逆着来想，所谓正难则反
  
- 代码如下
  
  ```cpp
  #include <bits/stdc++.h>

  using namespace std;
  
  int corn[] = {1, 5, 10, 50, 100}, t, n, nums[6] = {0};
  
  int main() {
      scanf("%d", &t);
      while (t--) {
          int tmp = n, indx = 4, Max = 0, Min = 0, sum = 0, csum = 0;
          scanf("%d", &n);
          for (int i = 0; i < 5; i++) {
              scanf("%d", nums + i);
              sum += nums[i] * corn[i];
              csum += nums[i];
          }
          while (tmp && indx >= 0) {
              int num = tmp / corn[indx];
              if (num > nums[indx]) {
                  num = nums[indx];
              }
              tmp -= num * corn[indx];
              Min += num;
              indx--;
          }
          if (tmp != 0) { printf("-1 -1\n"); continue; }
          //需要凑的最大数目
          tmp = sum - n, indx = 4;
          while (tmp && indx >= 0) {
              int num = tmp / corn[indx];
              if (num > nums[indx]) {
                  num = nums[indx];
              }
              tmp -= num * corn[indx];
              Max += num;
              indx--;
          }
          printf("%d %d\n", Min, csum - Max);
      }
      return 0;
  }
  ```
  
- [例题3：cf 230A Dragons](https://codeforces.com/problemset/problem/230/A)
  
  - 题意：打龙了， 你目前要打n条龙，你的力量是s，你打一个龙的话你的力量必须比龙大，如果你打死一条龙你也可以获得相应的力量，问你是否能把所有的龙都打完
  - 思路：一道很简单的贪心，先从力量小的龙开始打，看看能不能打完就好了
  
- 代码如下
  
  ```cpp
  #include <algorithm>
  #include <cstdio>
  #include <utility>
  #include <vector>
  #define all(x) x.begin(), x.end()
  using namespace std;
  
  vector<pair<int, int>> dr;
int n, s;
  
  int main() {
      scanf("%d%d", &s, &n);
      for (int i = 0; i < n; i++) {
          int x, y;
          scanf("%d%d", &x, &y);
          dr.push_back(make_pair(x, y));
      }
      bool win = true;
      sort(all(dr));
      for (int i = 0; i < n; i++) {
          if (s > dr[i].first) {
              s += dr[i].second;
          } else {
              win = false;
              puts("NO");
              break;
          }
      }
      if (win) puts("YES");
      return 0;
  }
  ```

  
  
- [例题4：51Nod - 1117 聪明的木匠](https://www.51nod.com/Challenge/Problem.html#problemId=1117)
  
  - 题意：一个木段，现要锯成$L_1, L_2, L_3 …… L_n$的长度，但是据一段木段需要消耗相应的体力，例如要把长为8的木段锯成 3 和 5 的话需要消耗3 + 5 = 8的体力，现在问你最少要消耗多少体力锯好
  - 错误的思路：一开始想不出来，单纯的觉得是一个简短的排序不等式的的贪心题，只要先据出最大的目标值，然后一直据下去就是最优解，但这是不对的，最多也就过个他给的样例。
  - 正确的思路①：仔细想想，如果是没按要求切成题目要求目标长度切，那样的话当然是希望一切下去可以让后面的体力没这么多，比如要分成2 2 3 3，这样的话应该是先 5 5 再 2 3 5 最后就 2 3 2 3这样就是最小的，消耗为20， 如果是按照先前的想法就是21，所以先前的思路显然是不对的。那这个思路怎么实现呢，dfs？显然很难实现
  - 正确的思路②：回忆上上题的总结，正难则反，反过来想，现在把问题改成拼成一个完整的木段，消耗最小，这样的话就是拼当前最小的两小木段，最终的答案就是最小的
  
- 代码如下(注释的部分是一开始错误的思路，交上去才发现思路不对)
  
  ```cpp
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
#include <queue>
  
  using namespace std;
  using ll = long long;
  int n, arr[50010], sum[50010];
  priority_queue<int> heap;
  int main() {
      scanf("%d", &n);
      for (int i = 0; i < n; i++) {
          // scanf("%d", &arr[i]);
          int x;
          scanf("%d", &x);
          heap.push(-x);
      }
      int ans = 0;
      while (heap.size()) {
          int x = -heap.top();
          heap.pop();
          int y = -heap.top();
          heap.pop();
        ans += x + y;
  
          if (heap.size()) heap.push(-(x + y));
      }
      printf("%d", ans);
      /* sort(arr, arr + n);
      for (int i = 0; i < n; i++) {
          sum[i + 1] = sum[i] + arr[i];
      }
      ll ans = 0;
      for (int i = n - 1; i >= 1; i--) {
          ans = ans + arr[i] + sum[i] - sum[0];
      }
      printf("%lld\n", ans); */
      return 0;
  }
  ```
  
  
  
- [例题5：HDU 6709 Fishing Master](http://acm.hdu.edu.cn/showproblem.php?pid=6709)
  
  - 题意：钓鱼家有一个锅和一个鱼竿，他有 n 条鱼要钓，钓一条鱼需要 k 时间，他的任务就是把这些鱼都钓上来，并且把它们都煮了，第i条鱼的煮熟所需要的时间为$t_i$，他钓鱼的时候不能往锅里放鱼并且要等他掉好k时间才可做下一件事，他可以在煮鱼的时候钓鱼，他可以同时拥有很多鱼，即他钓好后可以放在身边，因为锅一次只能煮一条鱼。现在问你他至少要花多少时间煮好n条鱼。
  - 思路：
    - 一开始看错题，wa了一下午，~~英语太差的锅~~。回归正题，显然第一条鱼是钓的时间是不可避免的，全部煮鱼的时间也是不可避免的。可以优化的就只有钓鱼多出来的时间了。
    - 注意到只要我煮的鱼时间足够长，那么我就可以在煮鱼的这个空档里面钓足够多的鱼，即可以钓$t_i / k$条，如果鱼快煮好了，即再过$x(x < k)$的时间就煮好鱼了，不难发现这里的$x = t_i \% k$，那么这时候是继续钓鱼呢，还是选择去等鱼煮好然后放下一条（前提是你手上有钓好的鱼）
      - 如果你选择继续钓鱼，那时间就会多花$k - t_i \% k$的时间
      - 如果你选择去等待的话，那你手上就必须要有鱼，没鱼怎么办，那只能选择上个选择，即继续钓鱼
    - 画图出来可能比较直观
    ![fishing master配图](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPICFishintMaster配图.png)
    - 对于这个配图，即便是$t_i < k$公式依旧成立
    - 显然，根据图，我们贪心的策略就是在煮鱼的空档里尽可能多地钓鱼,比如上图，就是钓2条，然后去等待鱼煮好就放下一条，假设这样下去我们总共在钓鱼的空档里钓的鱼数量为$num$
    - 正如上述第一条鱼钓的时间是不可避免的，所以接下来只剩下n - 1条鱼需要我们在煮鱼的空档时钓上来，假设$num \ge n - 1$，即我们在空档里钓的鱼比我们需要钓的鱼还多，那最终答案就是$k + \sum{t_i}$
    - 若$num < n - 1$，即我们在空档的时候钓的鱼不够我们需要钓的，那我们就要在煮鱼的空档多钓一条了，如上图就是钓3条，这样的操作需要我们进行$n - 1 - num$次，很显然，我们希望总时间尽可能地短，那就必须让$k - t_i \% k$尽可能地小
    - 对于上述的操作，代码里面只要用个数组mor记录$k - t_i \% k$，然后再ans里面加上前$n - 1 - num$小的$k - t_i \% k$就好了
  - 这题说得有点多了，因为自己一开始真的觉得有点难
  - 但是听说还有另一种解法就是一种我所不知道的**带反悔的贪心**法
    - 具体做法就是将鱼根据他们的煮的时间从大小排序，并且按顺序这样钓上来
    - 然后幻想自己顺畅无阻地把0到n - 1的鱼一个一个地煮并且可以在每条鱼煮好后可以马上放下一条鱼（即幻想自己每条鱼都能在其空档期间充分钓足够的鱼为下一次煮鱼做准备）
    - 而现实却不尽人意，所以需要每次记录在煮鱼的空档期间总共可以钓的鱼$num$，如果$num$小于我们幻想钓的鱼的数量就反悔，即穿越时空去我在前面其中一个煮鱼的空档里面多钓一条鱼，这个空档的条件依旧是和我的思路一样，即$k - t_i \% k$尽可能地小
    - 这个需要优先队列，具体代码也放出来吧，但是这代码不是我自己写的，是直接复制某大佬的~~懒得自己再写了~~[转载传送门](https://www.cnblogs.com/QAQorz/p/11548854.html)
  
- 代码如下

```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>

using namespace std;
using ll = long long;

int t, n;
ll k, tim[100005], mor[100005];

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d", &t);
    while (t--) {
        scanf("%d%lld", &n, &k);
        for (int i = 0; i < n; i++) {
            scanf("%lld", tim + i);
        }
        ll ans = k, num = 0;
        for (int i = 0; i < n; i++) {
            ans += tim[i];
            num += tim[i] / k;
            mor[i] = k - tim[i] % k;
        }
        if (num < n - 1) {
            sort(mor, mor + n);
            for (int i = 0; i < n - 1 - num; i++) {
                ans += mor[i];
            }
        }
        printf("%lld\n", ans);
        memset(mor, 0, sizeof mor);
        memset(tim, 0, sizeof tim);
  }

    return 0;
}

//下面是大佬的代码
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f
#define debug(x) cout << #x << " = " << x << endl;
using namespace std;
typedef long long LL;

const int mx = 1e5+7;
int a[mx];
priority_queue<int> q;

int main(){
    int t, n, k;
    scanf("%d", &t);
    while (t--){
        while (!q.empty()) q.pop();
        scanf("%d%d", &n, &k);
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        sort(a+1, a+n+1, greater<int>());
        LL ans = k, num = 1;
        for (int i = 1; i <= n; i++){
            ans += a[i];
            num += a[i] / k;
            q.push(a[i] % k);
            if (num < i){
                ans += k-q.top();
                q.pop();
            }
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```

- [例题6：Bin Packing](https://vjudge.net/problem/UVA-1149)

- 题意：给你n个物品，每个物品的重量为$w_i$，你有容量为w的背包，每个背包最多只能装两件物品，问你最少要多少背包来装
  - 贪心就好了，贪心策略：将物品按重量排序，如果最重和最轻的物品一起不超过w就放一起，然后就让次重和次轻的放一起；否则重的单独放在一个背包，然后让次重的背包和最轻的一起，以此循环
  - 代码如下

  ```c++
  #include <cstdio>
  #include <cstring>
  #include <algorithm>

  using namespace std;

  const int M = 1e5 + 5;
  int n, w, wi[M];

  int main () {
      int t;
      scanf("%d", &t);
      while (t--) {
          scanf("%d%d", &n, &w);
          for (int i = 0; i < n; i++) scanf("%d", wi + i);
          sort(wi, wi + n);
          int l = 0, r = n - 1, ans = 0;
          while (l <= r) {
              if (wi[l] + wi[r] <= w) l++, r--;
              else r--;
              ans++;
          }
          if (t) printf("%d\n\n", ans);
          else printf("%d\n", ans);
      }
      return 0;
  }
  ```

## 区间贪心

- [例题1：HDU 2037 今年暑假不AC](http://acm.hdu.edu.cn/showproblem.php?pid=2037)
  
  - 题意：给你一些区间，让你选出尽量多的不相交的区间
  - 思路：把所有区间（x, y）根据y来从小到大排序，然后从第一区间开始，排除和他相交的区间，即对比前一个选定的区间的y和当前要对比的区间的x，如果x < y则不选，否则回到上一步
- 看代码容易懂
```cpp
#include <algorithm>
#include <cstdio>
#include <utility>
#include <vector>

using namespace std;
int n;
int main() {
  while (scanf("%d", &n), n) {
      vector<pair<int, int>> coo;
      int x, y;
      for (int i = 0; i < n; i++) {
          scanf("%d%d", &x, &y);
          coo.push_back(make_pair(y, x));
      }
      sort(coo.begin(), coo.end());
      int num = 0, last = -0x3f3f3f3f;
      for (int i = 0; i < n; i++) {
          if (last <= coo[i].second) {
              num++;
              last = coo[i].first;
          }
      }
      printf("%d\n", num);
  }
}
```



## 领项交换法

- [例题1：LOJ 加工生产调度](https://loj.ac/p/10003)

- 题意：一个物品必须要现在A车间加工，才能在B区间加工个，问你n个物品最短的加工结束时间是多少

- 思路：一道Johnso不等式的题，不知道也没关系，只要你会邻项交换法就能写

  - 首先假设只有两个产品要加工，其中一种最优解，两个下标分别为$i,j$，则其最终加工的时间为
  $$
  max(a_i + b_i, a_i + a_j) + b_j
  $$
  - 假设调换$i,j$，则最终的加工时间为
  $$
  max(a_j + b_j, a_j + a_i) + b_i
  $$
  - 此时我当然是希望以下等式成立
  $$
  max(a_i + b_i, a_i + a_j) + b_j < max(a_j + b_j, a_j + a_i) + b_i
  $$
  - 固我们直接这样排序就可以了(这就是邻项交换法)，当然网上还有对上式继续化简后的不等式，据说是Johnson不等式（我也不是很懂这个不等式，总之挺有意思的）
  $$
  max(a_i + b_i, a_i + a_j) + b_j < max(a_j + b_j, a_j + a_i) + b_i \\
  \Rightarrow max(b_i,a_j) + a_i + b_j < max(b_j, a_i) + a_j + b_i\\
  \Rightarrow max(b_i,a_j) - a_j - b_i < max(b_j, a_i) - a_i - b_j\\
  \Rightarrow max(-a_j,-b_i) < max(-a_i, -b_j)\\
  \Rightarrow min(a_j, b_i) > min(a_i, b_j)
  $$
  - 以上式子均可以放到sort中作为排序的依据
  - ac代码

  ```cpp
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <vector>
  
  using namespace std;
  
  #include <cctype>
  inline long long IO() {
      long long x = 0;
      bool f = false;
      char c = getchar();
      while (!isdigit(c)) {
          if (c == '-') f = true;
          c = getchar();
      }
      while (isdigit(c)) {
          x = (x << 1) + (x << 3) + (c - '0');
          c = getchar();
      }
      return f ? -x : x;
  }
  const int M = 1e5 + 5, N = 1000 + 5, inf = 0x3f3f3f3f;
  int n, m;
  #define ll long long
  struct pairs {
      int i, a, b;
  }p[N];
  int main() {
      n = IO();
      for (int i = 0; i < n; ++i) {
          p[i].i = i, p[i].a = IO();
      }
      for (int i = 0; i < n; ++i) p[i].b = IO();
      sort(p, p + n, [] (pairs &i, pairs &j) {
          return max(i.b, j.a) + i.a + j.b < max(j.b, i.a) + j.a + i.b;
          // return min(j.a, i.b) > min(i.a, j.b);//这个也可以
      });
      int ans = 0, a = 0, b = 0;
      for (int i = 0; i < n; ++i) {
          a += p[i].a;
          b = max(a, b) + p[i].b;
      }
      ans = b;
      printf("%d\n", ans);
      for (int i = 0; i < n; ++i) {
          printf("%d", p[i].i + 1);
          if (i != n - 1) putchar(' ');
      }
      return 0;
  }
  ```

## ddl贪心

- [例题1：智力大冲浪](https://loj.ac/p/10004)

- 一种简单型的$ddl$贪心问题，每个任务都是单位时间就能完成

- 贪心方案：先讲扣的分数降序排序，再从其$ddl$时间开始往前找时间给他完成（因为要尽量空出前面的时间给后面的任务，换句话说就是让当前任务尽可能是接近$ddl$的时间之前完成），若找不到这个时间则扣这个扣款数

- ac代码

  ```cpp
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <vector>
  
  using namespace std;
  
  #include <cctype>
  inline long long IO() {
      long long x = 0;
      bool f = false;
      char c = getchar();
      while (!isdigit(c)) {
          if (c == '-') f = true;
          c = getchar();
      }
      while (isdigit(c)) {
          x = (x << 1) + (x << 3) + (c - '0');
          c = getchar();
      }
      return f ? -x : x;
  }
  const int M = 1e5 + 5, N = 1000 + 5, inf = 0x3f3f3f3f;
  // int a[M], sum[M];
  int n, m;
  #define ll long long
  struct pairs {
      int t, w;
  }p[N];
  bool day[N];
  int main() {
  #ifndef ONLINE_JUDGE
      freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
      freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
  #endif
      m = IO(), n = IO();
      int sum = 0;
      for (int i = 1; i <= n; ++i) p[i].t = IO();
      for (int i = 1; i <= n; ++i) sum += p[i].w = IO();
      sort(p + 1, p + 1 + n, [](pairs& i, pairs& j) {
          return i.w > j.w;
      });
      for (int i = 1; i <= n; ++i) {
          for (int j = p[i].t; j; --j) {
              if (day[j]) continue;
              day[j] = true;
              sum -= p[i].w;
              break;
          }
      }
      printf("%d", m - sum);
      return 0;
  }
  ```

  

- [例题2：家庭作业](https://loj.ac/p/10008)

- 这题是一道进阶版的ddl贪心问题，和[智力大冲浪](https://blog.csdn.net/hao_6_6/article/details/113791085)一样，只是数据范围大了很多

- 首先思路和智力大冲浪是一样的，先做了分高的，时间从这个分高的$ddl$后往前遍历，看看有没有时间能做，能就加上这个分，不能就跳过

- 但是由于这个$ddl$的是长度太长了，两重循环一定会超时的，所以，就要优化一下

- 优化方案一（线段树）
	- 不难发现，我们上面的思路的第二重循环目的就是为了找从$1$到$ddl$时间里面最靠后的时间是多少
	- 所以可以用一个线段树优化，即查找区间最大值
	- 代码如下
	
	```cpp
	#include <cstdio>
	#include <cstring>
	#include <algorithm>
	#include <vector>
	
	using namespace std;
	
	#include <cctype>
	inline long long IO() {
	    long long x = 0;
	    bool f = false;
	    char c = getchar();
	    while (!isdigit(c)) {
	        if (c == '-') f = true;
	        c = getchar();
	    }
	    while (isdigit(c)) {
	        x = (x << 1) + (x << 3) + (c - '0');
	        c = getchar();
	    }
	    return f ? -x : x;
	}
	const int M = 1e6 + 5, N = 7e5 + 5, inf = 0x3f3f3f3f;
	int n, m;
	struct pairs {
	    int t, c;
	}p[M];
	
	int seg[N << 2];
	void build(int l, int r, int node = 1) {
	    if (l == r) {
	        seg[node] = l;
	        return;
	    }
	    int mid = (l + r) >> 1;
	    build(l, mid, node << 1), build(mid + 1, r, node << 1 | 1);
	    seg[node] = max(seg[node << 1], seg[node << 1 | 1]);
	}
	
	void update(int index, int l, int r, int node = 1) {
	    if (l == r) {
	        seg[node] = 0;
	        return;
	    }
	    int mid = (l + r) >> 1;
	    if (index <= mid) update(index, l, mid, node << 1);
	    else update(index, mid + 1, r, node << 1 | 1);
	    seg[node] = max(seg[node << 1], seg[node << 1 | 1]);
	}
	
	int get(int index, int l, int r, int node = 1) {
	    if (r <= index) return seg[node];
	    int mid = (l + r) >> 1;
	    int res = get(index, l, mid, node << 1);
	    if (index > mid) res = max(res, get(index, mid + 1, r, node << 1 | 1));
	    return res;
	}
	
	int main() {
	    n = IO();
	    int ans = 0, d = 0;
	    for (int i = 0; i < n; ++i) p[i].t = IO(), p[i].c = IO(), d = max(d, p[i].t);
	    sort(p, p + n, [] (pairs& i, pairs& j) {
	        return i.c > j.c;
	    });
	    build(1, d);
	    for (int i = 0; i < n; ++i) {
	        int maxv = get(p[i].t, 1, d);
	        if (maxv) {
	            ans += p[i].c;
	            update(maxv, 1, d);
	        }
	    }
	    printf("%d", ans);
	    return 0;
	}
	```
	
	
	
- 优化方案二 （并查集）
  - 上面用线段树无非就是为了找从$1$到$ddl$的最大值
  - 这个过程其实可以用并查集来做，即初始化从$1$到$ddl$的时间里面最大值都是他们自己，当这个时间被用了之后，就让其父亲指向前一个时间
  - 代码如下

  ```cpp
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <vector>
  
  using namespace std;
  
  #include <cctype>
  inline long long IO() {
      long long x = 0;
      bool f = false;
      char c = getchar();
      while (!isdigit(c)) {
          if (c == '-') f = true;
          c = getchar();
      }
      while (isdigit(c)) {
          x = (x << 1) + (x << 3) + (c - '0');
          c = getchar();
      }
      return f ? -x : x;
  }
  const int M = 1e6 + 5, N = 7e5 + 5, inf = 0x3f3f3f3f;
  int n, m;
  struct pairs {
      int t, c;
  }p[M];
  int fa[N];
  int findset(int x) { return x == fa[x] ? x : fa[x] = findset(fa[x]); }
  int main() {
      n = IO();
      int ans = 0, d = 0;
      for (int i = 0; i < n; ++i) p[i].t = IO(), p[i].c = IO(), d = max(d, p[i].t);
      sort(p, p + n, [] (pairs& i, pairs& j) {
          return i.c > j.c;
      });
      for (int i = 0; i <= d; ++i) fa[i] = i;
      for (int i = 0; i < n; ++i) {
          int maxv = findset(p[i].t);
          if (maxv) {
              ans += p[i].c;
              fa[maxv] = maxv - 1;
          }
      }
      printf("%d", ans);
      return 0;
  }
  ```

  

- 优化方案三（优先队列）
  - 这个优化方案在$loj$不开O2优化依然超时
  - 而且这个优化方案其实不是对上面超时的思路进行优化，但是挺好的，只是被$loj$卡了罢了
  - 思路：首先对于一个任务，它会影响它$dll$时间后面的任务，但是不会影响$ddl$之前的任务
  - 所以可以让时间从后往前遍历，对于一个任务，我肯定是想让他在其$ddl$时间之前之内完成，最贪心的方式就是在$ddl$当天完成，腾出前面的空间让给别人。
  - 但是如果是当前的时间，相同的$ddl$有多个任务，那我肯定是先让分高的先做了，如果还有（从后往前遍历时遗留下来没做的任务）比当前时间靠后的$ddl$任务，也是先让分高的先做。
  - 固可以用优先队列来模拟上面的过程
  - 代码如下

  ```cpp
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <vector>
  
  using namespace std;
  
  #include <cctype>
  inline long long IO() {
      long long x = 0;
      bool f = false;
      char c = getchar();
      while (!isdigit(c)) {
          if (c == '-') f = true;
          c = getchar();
      }
      while (isdigit(c)) {
          x = (x << 1) + (x << 3) + (c ^ 48);
          c = getchar();
      }
      return f ? -x : x;
  }
  const int M = 1e6 + 5, N = 7e5 + 5, inf = 0x3f3f3f3f;
  int a[M];
  int n, m;
  #define ll long long
  #include <queue>
  struct pairs {
      int t, c;
  }p[M];
  priority_queue<int> heap;
  int main() {
      n = IO();
      int ans = 0, d = 0;
      for (int i = 0; i < n; ++i) p[i].t = IO(), p[i].c = IO(), d = max(d, p[i].t);
      sort(p, p + n, [] (pairs& i, pairs& j) {
          if (i.t == j.t) return i.c > j.c;
          return i.t > j.t;
      });
      for (int i = 0; d; --d) {
          while (i < n) {
              if (p[i].t < d) break;
              heap.push(p[i++].c);
          }
          if (heap.empty()) continue;
          ans += heap.top();
          heap.pop();
      }
      printf("%d", ans);
      return 0;
  }
  ```

  


