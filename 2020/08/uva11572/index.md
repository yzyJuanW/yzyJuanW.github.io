# UVA11572


## [Unique Snowflakes](https://vjudge.net/problem/UVA-11572)

- 题意：给你一个长度为$n(n \le 1000000)$的数组，问你最长有不重复数字的连续子序列有多长
- 用set和map的速度都差不多，set记录是否重复，map记录当前数字在此之前最近出现的位置，如果没有就是-1
- 如果嫌set和map慢的话直接用unordered_set或者unordered_map
- 这里用了上次偶遇了一种很牛逼很简洁的hash方式后就喜欢上这个hash，跑进了100ms
- 代码如下

```c++ {.line-numbers}
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

const int M = 1e6 + 10, H = 0xfffff;
int n, arr[M], last[M];

struct Hash{
    int real[H + 5], val[H + 5];
    void clear() { memset(val, -1, sizeof val); }//初始化-1
    int& operator[] (const int &k) {
        int i = k & H;
        while (real[i] != k && ~val[i]) i = (i + 1) & H;
        real[i] = k;
        return val[i];
    }
}h;

int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        scanf("%d", &n);
        h.clear();
        memset(last, -1, sizeof last);
        for (int i = 0; i < n; i++) {
            scanf("%d", arr + i);
            last[i] = h[arr[i]];
            h[arr[i]] = i;
        }
        int ans = 0, l = 0, r = 0;
        while (r < n) {
            while (r < n && last[r] < l) r++;
            ans = max(ans, r - l);
            l = last[r] + 1;
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

