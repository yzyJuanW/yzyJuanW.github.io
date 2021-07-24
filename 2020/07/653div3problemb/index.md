# B. Multiply by 2, divide by 6题解


[传送门](https://codeforces.com/contest/1374/problem/B)

## 题意

- 给一个数 n,问你只进行以下两种操作，要操作最少多少次能够使得 n 为 1：
  - 把 n 除以 6 （前提是可以整除）
  - 把 n 乘 2
- 输出最少的操作次数，如果无论如何都没办法得到 1，则输出 -1

## 思路

- 首先要知道的是，如果对一个数同时进行两个操作就相当于对 n 除以 3
- 思考一个极端的例子就是：n 的质因子只有 3，那么它一定可以最终得到 1。为什么呢？因为我们可以让每个 3 都乘 2， 这样他就有了因子 6， 便可以做第一个操作了，并且最终得到 1
- 如果这个数既有 2 又有 3 怎么办？ 很简单，只需要把一对 2 和 3 看成 6，最终剩下的因子如果是 2（因为已经无望再去除以 6 了），则不可能得到 1，如果是 3， 则当成只有因子 3 的数看待（即上述的极端情况）
- 最后一种情况就是 n 的质因子除了 3 和 2 还有其他的质因子的话，一定最终无法得到 1
- 所以最终的想法就是：计算出质因子 2 的个数a，和计算出质因子 3 的个数 b, 如果 $a > b$ 则无法得到 1，否则要操作的次数最少为 $(b - a) \times 2 + a$
- 代码如下

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
typedef vector<int> vi;
#define debug printf("(hao)")
#define all(x) x.begin(), x.end()
#define rep(i, a, b) for (int i = (a); i < (b); i++)
#define clr(a, v) memset(a, v, sizeof(a))
//主代码函数
void solve() {
    int n, a = 0, b = 0;
    scanf("%d", &n);
    while (n % 2 == 0) n /= 2, a++; //计算质因子 2 的个数
    while (n % 3 == 0) n /= 3, b++; //计算质因子 3 的个数

    if (n != 1) printf("-1\n");     //如果质因子有其他
    else if (a > b) printf("-1\n"); //如果 2 个数比三多，则无法得到 1
    else printf("%d\n", (b - a) * 2 + a);
}

// #define LOCAL
int main() {
    std::ios::sync_with_stdio(false);
#ifdef LOCAL
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    int T;
    scanf("%d", &T);
    while (T--)
        solve();
    return 0;
}
```

## 反思

- 第一次没做出来是使用了模拟的暴力解法，理论上是过了，第一次提交过了，重判之后又错在了第11个test，后来看了才发现是爆int了~~一定是因为我第一次打cf比赛的原因~~
- 看到int范围就要优先考虑使用long long~~不然这题我也不会错~~
- 最知道思考的是本文的解法，思维题嘛，锻炼自己思维，毕竟不是每道思维题都可以直接模拟

## 总结

- 遇到这种题尝试思考因子的问题
- 在草稿纸上找找规律，毕竟cf就喜欢考这些思维题
