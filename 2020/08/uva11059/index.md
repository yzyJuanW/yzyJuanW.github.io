# UVA11059


## [Maximum Product](https://vjudge.net/problem/UVA-11059)

- 给你一个数组，让你求子字串的最大乘积，如果没有就输出0
- 暴力解就好了

```c++
#include <cstdio>
#include <algorithm>
#include <vector>

using namespace std;

using ll = long long;
int n, kase = 0;

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while(~scanf("%d", &n)) {
        ll Max = 0;
        vector<ll> arr(n);
        for (auto &x : arr) scanf("%lld", &x);
        for (int i = 0; i < n; i++) {
            ll tmp = arr[i];
            Max = max(Max, tmp);
            for (int j = i + 1; j < n; j++) {
                tmp *= arr[j];
                Max = max(Max, tmp);
            }
        }
        printf("Case #%d: The maximum product is %lld.\n\n", ++kase, Max);
    }
    return 0;
}
```

