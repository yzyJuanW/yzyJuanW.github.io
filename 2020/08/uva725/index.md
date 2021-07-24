# UVA725


## [Division](https://vjudge.net/problem/UVA-725)

- 题意：给你一个数n，让你求出 $abcde / fghij = n$ 的所有结果，a ~ j 的恰好是 0 ~ 9 的数
- 暴力遍历分子就好了，然后检查分母是否合法就行了

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int M = 1e5;
int n;

bool check(int a, int b) {
    bool num[10] = {0};
    if (b < 10000) num[0] = true;
    while (a || b) {
        num[a % 10] = true;
        num[b % 10] = true;
        a /= 10, b /= 10;
    }
    int x = 0;
    for (auto i : num) if (i) x++;
    return x == 10;
}

bool check(int a) {
    bool num[10] = {0};
    if (a < 10000) num[0] = true;
    while (a) {
        num[a % 10] = true;
        a /= 10;
    }
    int x = 0;
    for (auto i : num) if (i) x++;
    return x == 5;
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    bool f = true;
    while (scanf("%d", &n), n) {
        if (f) f = false;
        else puts("");
        bool ok = true;
        for (int i = 11111; i < M; i++) {
            if (i % n == 0 && check(i) && check(i / n) && check(i, i / n)) {
                ok = false;
                printf("%05d / %05d = %d\n", i, i / n, n);
            }
        }
        if (ok) printf("There are no solutions for %d.\n", n);
    }
    return 0;
}
```

