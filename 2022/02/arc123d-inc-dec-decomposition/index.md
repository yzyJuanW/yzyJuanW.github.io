# arc123D Inc, Dec - Decomposition


题意：将一个数组a分成两个数组b和c，使得$a_i = b_i + c_i$，同时b数组要单调不减，c数组要单调不增，问$\sum_{i=1}^n \lvert b_i\rvert + \lvert c_i\rvert$可能的最小值

首先一个突破口就是发现一点：贪心地想，$b_i$、$c_i$确定后，$b_{i+1}$、$c_{i+1}$其中一个是等于上一个的，因为要使得某一个数组尽量上升（或者下降）地慢一些

所以可以发现，一旦$b_1$确定后，整个b、c数组就确定了

还有一点是将c数组取反（全部加个负号），这样b和c都是单调不减的数组了（至于为什么，后面中位数看明白后回头看就懂了）

这样的话$a_i = b_i - c_i$

对于第$i + 1$个，如果$a_{i} \ge a_{i+1}$，则
$$
\begin{aligned}
b_{i+1} &= b_i \newline
c_{i+1} &= b_{i+1} - a_{i+1} \newline
& = a_i + c_i - a_{i+1} \newline
&= c_i + (a_i - a_{i+1})
\end{aligned}
$$
否则
$$
\begin{aligned}
c_{i+1} &= c_i \newline
b_{i+1} &= a_{i+1} + c_{i+1} \newline
& = a_{i + 1} + b_i - a_{i} \newline
&= b_i + (a_{i + 1} - a_{i})
\end{aligned}
$$
整合下就是
$$
\begin{aligned}
b_{i+1} &= b_i + \max(0, \space a_{i+1} - a_i) \newline
c_{i+1} &= c_i + \max(0, \space a_i - a_{i+1})
\end{aligned}
$$
前面说了，假设$b_1$确定了，后面的就全确定了，
假设$b_1 = x$, 则后面的就为${b_1 + \max(0, a_2 - a_1), \space b_2 + \max(0, a_3 - a_2), \space \ldots}$

设这个确定$b_1$后的b数组为p数组，c数组为q数组，变动x只会让所有的p和q数组都同时变动，即
$$
res = \sum_{i=1}^n \lvert p_i - t \rvert + \lvert q_i - t \rvert
$$
求$res$最小，显然t就是p和q拼接后的数组(设为arr)的中位数

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 2e5 + 5;
long long a[N], arr[N + N];

int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n; ++i) {
        scanf("%lld", &a[i]);
    }
    arr[1] = a[1];
    for (int i = 2; i <= n; ++i) {
        arr[i] = arr[i - 1] + max(0ll, a[i] - a[i - 1]);
        arr[i + n] = arr[i + n - 1] + max(0ll, a[i - 1] - a[i]);
    }
    nth_element(arr + 1, arr + n, arr + n + n + 1);
    long long ans = 0;
    for (int i = 1; i <= n + n; ++i) {
        ans += abs(arr[i] - arr[n]);
    }
    printf("%lld\n", ans);
    return 0;
}
```


