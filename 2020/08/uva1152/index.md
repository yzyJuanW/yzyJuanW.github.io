# UVA1152


## [4 Values whose Sum is 0](https://vjudge.net/problem/UVA-1152)

- 题意：给你4个长度为$n(n \le 4000)$的集合，让你在4个集合里分别取a、b、c、d，使得$a + b + c + d = 0$，问你有多少种取法
- 按紫书说的“中途相遇法”，从象个不同的方向来解决问题，最终“汇集”在一起
- 用哈希记录$a + b$，然后再计算$-c - d$的哈希个数就好了，用slt提供的unordered_map足够过这题了，跑了6000ms多，a掉了
- 后来看了一位大佬自己写的hash，跑了一下，平均650ms，一个很快的哈希表
- 原理大概和线性探查法差不多，不过这里用的不是取余（取余很慢），这里用的位运算里的与运算，保证了数值在给定的哈希范围内
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int M = 4e3 + 10, H = 0x7fffff;
int n, arr[5][M];

struct Hash{
    int real[H + 5], val[H + 5]
    void clear() { memset(val, 0, sizeof val); }
    int& operator[] (const int& num) {
        int i = num & H;
        while (val[i] && real[i] != num) i = (i + 1) & H;
        real[i] = num;
        return val[i];
    }
}h;

void solve() {
    scanf("%d", &n);
    h.clear();
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < 4; j++) {
            scanf("%d", &arr[j][i]);
        }
    }
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            h[arr[0][i] + arr[1][j]]++;
        }
    }
    int ans = 0;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            ans += h[- arr[2][i] - arr[3][j]];
        }
    }
    printf("%d\n", ans);
}

int main() {
    int T, f = 1;
    scanf("%d", &T);
    while (T--) {
        if (f) f = !f;
        else puts("");
        solve();
    }
    return 0;
}
```

