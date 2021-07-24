# Shooshuns_and_Sequence


## [传送门](https://codeforces.com/contest/222/problem/A)

- 什么鬼，div2也出水题？还是我变强了？😂
- 题意：给你一个长为n的序列，再给你一个k，你每次都必须要这样做：选择第k个数，把他复制到序列的最后，再把序列的第一个数给删了，问你要操作多少次可以使得序列数组的数全部相同，如果根本不可能通过上述操作使得序列数组的数全部相同，则输出-1
- 思路：不难发现，如果一开始第k个数及后面的数有出现不同的数的话，是不可能通过上述操作使得整个数组完全相同，如果可能的话，就往k前找，即往左找，找到第一个与第k个数不同的数，输出他的下标就好了（因为要一直删到那个不同的数），模拟一下就好了。
- 代码如下

```c++
#include <cstdio>

using namespace std;

const int M = 1e5 + 10;

int n, k, arr[M];
int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d%d", &n, &k);
    for (int i = 0; i < n; i++) {
        scanf("%d", arr + i);
    }
    int tmp = arr[k - 1];
    for (int i = k; i < n; i++) {
        if (arr[i] != tmp) {
            printf("-1");
            return 0;
        }
    }
    for (int i = k - 2; i >= 0; i--) {
        if (arr[i] != tmp) {
            printf("%d", i + 1);
            return 0;
        }
    }
    printf("0");
    return 0;
}
```

