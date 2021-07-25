# UVA690


## [Pipeline Scheduling](https://vjudge.net/problem/UVA-690)

- 历经千辛万苦终于a了这题
- 题意：一台电脑有5个工作单元(unit0 ~ unit4)，有10(0 ~ 9)个程序需要完成，每个程序都要在n$(n < 20>)$的时间片完成，在同一时间里，一个单元只能执行一个程序，开始执行后就要严格按顺序执行完，问你最小的完成10个程序的时间是多少
- 从读题到思考到ac用了近两天，下面我将会从运行时间慢到运行时间快，tle到ac开始阐释解决这题时的坎坷
- 首先是这题怎么dfs
  - 一开始想的时候想到了一个最笨的方法：根据时间一步一步地dfs下去，但这样的话绝对不行，因为如果n = 19,最多会递归190层，就算考虑剪枝，如果答案就是上百的，那也受不了
  - 想了很久终于想到一个办法就是根据程序来递归，即递归（0 ~ 9）这样的话最多也就9层
  - 用一个二维的bool数组run[i][j]来储存状态，代表第i个工作单元在j时间时的运行情况，false代表是空闲的，true代表是正在工作
  - 对于一开始第一个程序即程序0肯定就是从0时刻时开始运行是最优的，所以dfs从程序1开始
  - 对于第$k$个程序来说，如果开始的时间是$s$，则它结束的时间一定是$s + n$，则第$k + 1$个程序可以在$s + 1$到$s + n + 1$之间（包括端点）的时间里开始运行
  - 到这里dfs的运行方式就很明朗了，只需要暴力枚举每个$s + 1$到$s + n + 1$的位置开始运行下一个程序就行了
  - 那么剪枝呢？这个dfs的方法直接对比先前算好最小完成时间的和当前程序的结束时间做对比来剪
- 上述的方法是会tle的，先别急，一步一步走到ac这个开头不能少
- 上述dfs方法的代码

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

int n, Min;
char unit[6][25];
bool run[5][500];

bool check(int s) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < 5; j++)
            if (unit[j][i] == 'X' && run[j][i + s]) return false;
    return true;
}

void dfs(int num, int s) {
    if (s + n >= Min) return;
    if (num == 10) { Min = s + n; return; }
    for (int i = s + 1; i <= s + n; i++) {
        if (!check(i)) continue;
        for (int j = 0; j < 5; j++)
            for (int k = 0; k < n; k++)
                if (unit[j][k] == 'X') run[j][i + k] = true;
        dfs(num + 1, i);
        for (int j = 0; j < 5; j++)
            for (int k = 0; k < n; k++)
                if (unit[j][k] == 'X') run[j][i + k] = false;
    }
}

inline void init(){
    memset(run, 0, sizeof run);
    Min = 0xffff;
}

int main () {
    while (scanf("%d", &n), n) {
        for (int i = 0; i < 5; i++) { scanf("%s", unit[i]); }
        init();
        for (int i = 0; i < n; i++)
            for (int j = 0; j < 5; j++)
                if (unit[j][i] == 'X') run[j][i] = true; //程序0开始运行
        dfs(1, 0);
        printf("%d\n", Min);
    }
    return 0;
}
```

- 上面的代码运行n = 19的时候，要在内心默念三四秒才会出答案，显然是超级慢了
- 下面阐述我是如何一步一步优化到ac甚至更快的结果的
- 对于上述的代码，我自己在运行的时候是很绝望的，自己跑都能感觉到很慢。后来就度娘了一下，学到了一种比较聪明的方法
  - 考虑第一个程序运行时哪些工作单元是在工作中的，上面说过对于下一个单元它只能在$s + 1$到$s + n + 1$内开始，但是并不是所有时间都可以开始的，它下一个能开始运行的时间是具有跳跃性的，就比如说题中的样例，他就只能当前程序运行的第2、3、6、7的时间开始，别的时间开始都会有冲突，对于后面的所有程序都是只能在上一个程序的第2、3、6、7的时间开始
  - 把跳跃的时间用数组jump[cnt]记录下来，这样的好处就是可以少枚举一些不必要的程序开始时间，$cnt$是可跳跃的个数
  - 还有一个好处就是可以大大地剪枝，具体的剪枝操作是：假如当前递归到了第$num$个数字，上一个程序的开始时间是$s$,则最少还要$s + jump[0] \times (10 - num) + n$的时间完成10个程序运行的时间，这样就可以和先前算好的$Min$进行对比剪枝了
- 优化好的代码如下，在电脑上运行20个$n = 19$的不同数据都能很快地出答案，但很可惜，交上去还是tle了

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

int n, jump[25], Min, cnt = 0;
char unit[6][25];
bool run[5][500];

bool check(int s) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < 5; j++)
            if (unit[j][i] == 'X' && run[j][i + s]) return false;
    return true;
}

void dfs(int num, int s) {
    if (s + n >= Min) return;
    if (s + jump[0] * (10 - num) + n >= Min) return;
    if (num == 10) { Min = s + n; return; }
    for (int i = 0; i < cnt; i++) {
        if (!check(s + jump[i])) continue;
        for (int j = 0; j < 5; j++)
            for (int k = 0; k < n; k++)
                if (unit[j][k] == 'X') run[j][s + jump[i] + k] = true;
        dfs(num + 1, s + jump[i]);
        for (int j = 0; j < 5; j++)
            for (int k = 0; k < n; k++)
                if (unit[j][k] == 'X') run[j][s + jump[i] + k] = false;
    }
}

inline void init(){
    memset(run, 0, sizeof run);
    cnt = 0;
    Min = 0xffff;
}

int main () {
    while (scanf("%d", &n), n) {
        for (int i = 0; i < 5; i++) { scanf("%s", unit[i]); }
        init();
        for (int i = 0; i < n; i++)
            for (int j = 0; j < 5; j++)
                if (unit[j][i] == 'X') run[j][i] = true;
        for (int i = 1; i <= n; i++) if(check(i)) jump[cnt++] = i;
        dfs(1, 0);
        printf("%d\n", Min);
    }
    return 0;
}
```

- 这个代码还有优化的空间的，就是二进制状态压缩，对于之前设定的run[5][500]的数组可以进行压缩成一维的run[500]，用二进制代表时间i时5个工作单元的状态，0代表空闲，1代表工作中
- 别忘了，要把输入的最初结果也要二进制压缩
- 这样进行压缩速度快了不少，少了一重长度为5的循环，可能递归常数大的原因，亦或是上面那个代码就tle了一点点的原因，所以二进制优化后直接ac了
- ac代码如下，跑了440ms

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

int n, Min, unit[25], run[500], jump[25], cnt = 0;

bool check(int s) {
    for (int i = 0; i < n; i++) if (unit[i] & run[i + s]) return false;
    return true;
}

void dfs(int num, int s) {
    if (s + n >= Min) return;
    if (s + (10 - num) * jump[0] + n >= Min) return;
    if (num == 10) { Min = s + n; return; }
    for (int i = 0; i < cnt; i++) {
        if (!check(jump[i] + s)) continue;
        for (int j = 0; j < n; j++) run[s + jump[i] + j] |= unit[j];
        dfs(num + 1, jump[i] + s);
        for (int j = 0; j < n; j++) run[s + jump[i] + j] ^= unit[j];//回溯
    }
}

inline void init(){
    memset(run, 0, sizeof run);
    memset(unit, 0, sizeof unit);
    cnt = 0;
    Min = 0xffff;
}

int main () {
    while (scanf("%d", &n), n) {
        init();
        for (int i = 0; i < 5; i++) {
            getchar();
            for (int j = 0; j < n; j++) {
                char c = getchar();
                if (c == 'X') unit[j] |= (1 << i);//压缩
            }
        }
        for (int i = 0; i < n; i++) run[i] = unit[i];
        for (int i = 1; i <= n; i++) if (check(i)) jump[cnt++] = i;
        dfs(1, 0);
        printf("%d\n", Min);
    }
    return 0;
}
```

- 那么还有优化的空间吗？当然还有，注意到n的范围是不超过20的，当我们在考虑下一个程序运行时是只看上一个程序的运行开始时间和结束时间的，所以我们可以反过来压缩，上一份代码是对每个时间里的所有工作单元进行压缩，这次是把每个工作单元的一次工作时间进行压缩
- 换句话说就是上一份代码是对行进行了压缩，这次是对列进行压缩，即每次压缩都是一个n位的二进制数
- 对于下一个程序，由于只看上一个程序，所以只需要存位数为n的二进制状态就行了，同样记得回溯，这次的回溯要用个tmp数组存起之前的二进制状态
- 这次的压缩直接把之前n的循环改成了5的循环，更大限度地优化了时间
- ac代码如下，跑了190ms

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

int n, Min, unit[10], run[10], cnt = 0, jump[25];

bool check(int s) {
    for (int i = 0; i < 5; i++) if (unit[i] & (run[i] >> s)) return false;
    return true;
}

void dfs(int num, int s) {
    if (s + n >= Min) return;
    if (s + (10 - num) * jump[0] + n>= Min) return;
    if (num == 10) { Min = s + n; return; }
    for (int i = 0; i < cnt; i++) {
        if (!check(jump[i])) continue;
        int tmp[5];
        for (int j = 0; j < 5; j++) { 
            tmp[j] = run[j]; 
            run[j] = (run[j] >> jump[i]) | unit[j];
        }
        dfs(num + 1, s + jump[i]);
        for (int j = 0; j < 5; j++) run[j] = tmp[j];
    }
}

inline void init(){
    memset(run, 0, sizeof run);
    memset(unit, 0, sizeof unit);
    Min = 0xffff;
    cnt = 0;
}

int main () {
    while (scanf("%d", &n), n) {
        init();
        for (int i = 0; i < 5; i++) {
            getchar();
            for (int j = 0; j < n; j++) {
                char c = getchar();
                if (c == 'X') unit[i] |= (1 << j);
            }
        }
        for (int i = 0; i < 5; i++) run[i] = unit[i];
        for (int i = 1; i <= n; i++) if(check(i)) jump[cnt++] = i;
        dfs(1, 0);
        printf("%d\n", Min);
    }
    return 0;
}
```

- 这是我优化的最快的了，再快的优化方式，我一个小小蒟蒻想不到了

