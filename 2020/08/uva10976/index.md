# UVA10976


## [Fractions Again?!](https://vjudge.net/problem/UVA-10976)

- 给你一个k,让你求所有 $1 / k = 1 / x + 1/ y$ 的答案 $x \ge y$
- 计算可以发现 y 最大不会超过2k,最小是k + 1

```c++
#include <cstdio>
#include <vector>

using namespace std;
using ll = long long;

ll n;

int main() {
    while (~scanf("%lld", &n)) {
        vector<ll> a, b;
        for (ll i = n + 1; i <= n + n; i++) {
            if (n * i % (i - n) == 0) {
                a.push_back(n * i / (i - n));
                b.push_back(i);
            }
        }
        printf("%zu\n", a.size());
        for (size_t i = 0; i < a.size(); i++) {
            printf("1/%lld = 1/%lld + 1/%lld\n", n, a[i], b[i]);
        }
    }
    return 0;
}
```

