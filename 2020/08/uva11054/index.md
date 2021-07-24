# UVA11054


## [Wine trading in Gergovia](https://vjudge.net/problem/UVA-11054)

- 题意：有n个村庄，每个村庄都有对酒的需求，正数代表要送出多少酒，负数代表需要多少酒，把k单位的酒运到相邻的村庄要k单位的体力，让你计算最小需要的体力
- 对于第一个村庄，他的需求量x无论正负，都需要右边的村庄搬过来或者搬过去右边的村庄，然后把多余的需求继承到下一个村庄，和紫书上讲的思想差不多
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>

using namespace std;
using ll = long long;
const int M = 1e5 + 10;
int n;
ll arr[M], ans;

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (scanf("%d", &n), n) {
        ans = 0;
        for (int i = 0; i < n; i++) { scanf("%lld", arr + i); }
        for (int i = 0; i < n - 1; i++) {
            ans += abs(arr[i]), arr[i + 1] += arr[i];
        }
        printf("%lld\n", ans);
        memset(arr, 0, sizeof arr);
    }
    return 0;
}
```

