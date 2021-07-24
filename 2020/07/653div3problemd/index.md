# D. Zero Remainder Array题解


[传送门](https://codeforces.com/contest/1374/problem/D)

## 题意

- 给你一个长度为 n 的数组$a_n$，和一个 k，你有一个 x（一开始为0），你有以下两种操作
  - 给 x 加上 1，即$x = x + 1$
  - 选择一个任意一个$a_i$($0 \le i < n$)加上 x，然后 x 加上 1
- 每个元素最多只能被执行一次上诉操作
- 问你执行上述操作，x 最小为多少可以使得数组所有元素都能整除 k

## 思路

- 记录数组的每一个数至少要加多少才可以被 k 整除
- 寻找上述数中的最大的众数，计算 x 加到多少才可以使得这些数都能被 k 整除。
- 例如
  - 对于一个数组 {1，1，1，2，3}，k = 6
  - 则其至少要加的数为 {5，5，5，4，3}
  - 显然最大的众数是 5，其个数是 3，那么对于第一个 5 则需要把 x 加到 5 就行了，对于第二个 5 ，就需要再等到下一个能模 6 为 5 的数，显然就是11($5 + 6 = 11$), 而第三个 5 也是同理就是 17，但这个还不是最终答案，因为题目的第二个操作是先给数组加上 x，再让 $x + 1$,所以最终答案就是 18
- 为什么这样就是对的呢？很简单，如果想要执行题中把所有数都弄成可以整除 k 并且每个数只能执行一次，对于两个相同且不能被 k 整除的数 y，想要把这两个 y 都弄成能被 k 整除，第二个数 y 一定要等到下一次有一个数 x 加上这个 y 使得它能整除 k。
- 所以我们要求出每个数最少加多少才可以被 k 整除，然后求一个最大的众数，计算最后一个众数需要的最小x（因为最后的 x 一定是停在了能使这些最大众数的最后一个数被 k 整除的结果），记得最后的答案要再加一才是最终答案。
- 那么其余的非最大众数呢？别担心，这些数在为最大众数服务的时候就能有一个 x 使得他们的一些能被 k 整除。例如上面的例子，我们在把 x 走到 5 的之前就有个4，可以使得数组中的 2 加上这个 4 能被 6 整除
- 还想不明白就想想模数具有周期性，例如将0 ~ n中的数都摸上 3 ，就会有这么一个规律 ：0 1 2 0 1 2 0 1 2 0 1 2·····
- 代码如下（求众数有点滑稽，因为我不会利用简单代码的求众数的方法）

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef vector<int> vi;
typedef vector<long long> vll;
typedef pair<int, int> pii;
typedef pair<long long, long long> pll;
#define debug printf("(hao)")
#define all(x) x.begin(), x.end()
#define rep(i, a, b) for (int i = (a); i < (b); i++)
#define clr(a, v) memset(a, v, sizeof(a))

void solve() {
    long long n, k;
    scanf("%lld%lld", &n, &k);
    vll arr;
    rep(i, 0, n) {
        long long tmp;
        scanf("%lld", &tmp);
        if (tmp % k != 0) //如果这个数不能被 k 整除
            arr.push_back(k - tmp % k); //对于每个数把最少加多少能被 k 整除的数放到数组里
    }
    if (arr.empty()) {//如果数组的数都能被 k 整除，操作次数就使 0
        printf("0\n");
        return;
    }
    sort(all(arr));
    long long Max = 1, w = arr[0];
    int len = arr.size();
    for (int i = 1; i < len;) { //求最大众数，方法比较蠢
        int sen = 1;            //计算出现个数
        if (arr[i] == arr[i - 1]) {
            while (arr[i] == arr[i - 1] && i < len) {
                sen++;
                i++;
            }
        } else {
            i++;
        }
        if (Max <= sen) Max = sen, w = arr[i - 1];//如果出现次数比之前算的要多
    }
    printf("%lld\n", w + k * (Max - 1) + 1);
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

- 找规律的能力不强
- 面对模没有想到他的一个周期性

## 总结

- 对连续的数模具有一定周期性
- 众数的求法不熟练
- 这题在真正计算的时候用的不是原数组
  
