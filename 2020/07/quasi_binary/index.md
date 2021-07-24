# Quasi_Binary


## [传送门](https://codeforces.com/problemset/problem/538/B)

- 题意：一个只由1和0组成的十进制数叫做Quasi Binary，例如11（十一）就是，现在给你一个n，让你求出他可能有哪些Quasi Binary的和组成，输出最少的Quasi Binary个数并将他们输出
- 思路：模拟，用个二维数组记录所有的结果位数结果，然后相加输出。例如32就是记录10、10、10、1、1，然后分别将不同位数的相加得到11（10 + 1），11（10 + 1），10（10 + 0）
- 代码如下
  
```c++
#include <algorithm>
#include <cstdio>
#include <cstring>

using namespace std;

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    int n;
    scanf("%d", &n);
    int t = n, sum = 0;
    while (t) {
        sum = max(t % 10, sum);
        t /= 10;
    }
    printf("%d\n", sum);

    int tmp, x = 0, y = 0, numset[7][10] = {0}, w = 1;
    while (n) {
        tmp = n % 10, y = 0;
        while (tmp) {
            numset[x][y++] = w;
            tmp--;
        }
        n /= 10, x++, w *= 10;
    }

    for (int i = 0; i < sum; i++) {
        int res = 0;
        for (int j = 0; j < 7; j++) {
            res += numset[j][i];
        }
        printf("%d ", res);
    }
    return 0;
}
```


