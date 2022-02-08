# Abc161D LunlunNumber


题意：一个数被称为lunlun数（无前导0）则说明每个相邻的数相差不超过1，例如345，问你从1开始第n个lunlun数是几

还记得windy数（数位dp入门题）吗，对，如果让我们n以内的windy数的个数显然可以数位dp

那么这道题也是同理，随着数的增加，lunlun数的个数也不减，具有单调性，所以我们可以求一个x内的lunlun数的个数，看看其是不是超过n个，那么问题就变成了找第一个x其范围内lunlun数的个数大于等于n，显然二分找

其实记录本题的目的是记录类似这种前导0需要特判数位dp的处理方式代码16~20行

当然，还有一种办法就是dfs多传一个参，标志是否前面全是0，然后再类似代码中的判断一样进行处理

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
typedef long long ll;
int num[20];
ll dp[20][13];

ll dfs(int id, int pre, int limit) {// pre为上一个数填了什么，12表示前面全是0， 
    if (id == 0) return 1;
    ll& ret = dp[id][pre], res = 0;
    if (!limit && ret != -1) return ret;
    int up = limit ? num[id] : 9;
    for (int i = 0; i <= up; ++i) {
        if (pre == 12) {
            res += dfs(id - 1, i == 0 ? 12 : i, limit && i == up);
        } else if (abs(pre - i) <= 1){
            res += dfs(id - 1, i, limit && i == up);
        }
    }
    if (!limit) ret = res;
    return res;
}

ll solve(ll x) {
    int len = 0;
    while (x) {
        num[++len] = x % 10;
        x /= 10;
    }
    return dfs(len, 12, 1) - 1;
}

int main() {
    memset(dp, -1, sizeof dp);
    int n;
    scanf("%d", &n);
    ll l = 1, r = 3234566667ll, ans = 1;
    while (l <= r) {
        ll mid = (l + r) >> 1;
        if (solve(mid) >= n) r = mid - 1, ans = mid;
        else l = mid + 1;
    }
    printf("%lld\n", ans);
    return 0;
}
```




