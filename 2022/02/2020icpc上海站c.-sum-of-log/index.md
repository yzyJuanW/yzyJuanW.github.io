# 2020ICPC上海站C. Sum of Log


题意：给你x，y，求 $\sum_{i=0}^x \sum_{j = \lbrack i = 0 \rbrack}^y \lbrack i \And j = 1\rbrack \lfloor log_2(i + j) + 1 \rfloor$

对于这种题（求范围内符合条件的数对）类型的题，有点类似19年南昌icpc的C题的数位dp思路

首先 $i \And j = 1$ 是一个切入口，一开始想错没注意到这个条件，后来发现后便觉得这题的难度小了一大半

然后要了解到$\lfloor log(x) + 1\rfloor$可以理解成二进制最高位1的位置

最后就是求和，定死最高位1在`i`的条件进行累加，注意，对于两个数的某一个数，如果`i`超过了或者等于他的最高位的位数，则这个数在dp的时候要有 `limit`限制

看代码好理解些

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 1e5 + 5, mod = 1e9 + 7;
int a[40], b[40], dp[35][2][2][2];

int change(int x, int* num) {
    int len = 0;
    fill_n(num, 40, 0);
    while (x) num[++len] = x % 2, x /= 2;
    return len;
}

int dfs(int id, int limit1, int limit2, int ish) { // ish看看当前是不是最高位
    if (id == 0) return 1;
    int& res = dp[id][limit1][limit2][ish];
    if (res != -1) return res;
    res = 0;
    int up1 = limit1 ? a[id] : 1;
    int up2 = limit2 ? b[id] : 1;
    for (int i = 0; i <= up1; ++i) {
        for (int j = 0; j <= up2; ++j) {
            if (i & j) continue;
            if (ish && i + j == 0) continue;
            res += dfs(id - 1, limit1 && i == up1, limit2 && j == up2, 0);
            if (res >= mod) res %= mod;
        }
    }
    return res;
}

void problem() {
    int n, m;
    scanf("%d %d\n", &n, &m);
    int len1 = change(n, a), len2 = change(m, b);
    int len = max(len1, len2);
    int ans = 0;
    memset(dp, -1, sizeof dp);
    for (int i = len; i >= 1; --i) {
        ans += 1ll * i * dfs(i, i >= len1, i >= len2, 1) % mod;
        if (ans >= mod) ans %= mod;
    }
    printf("%d\n", ans);
}


int main() {
    int t = 1;
    scanf("%d", &t);
    while (t--) {
        problem();
    }
    return 0;
}
```


