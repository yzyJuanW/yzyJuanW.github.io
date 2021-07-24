# Olympiad


## [传送门](https://codeforces.com/problemset/problem/222/D)

- 题意：给你两个同长数组代表n个人两场比赛的成绩，再给你一个x，某两个数组的和$a_i + b_j \ge x$，问你两数组组合成满足式子的排名最高和最低是多少。具体看样例就懂了。
- 思路：此题对于最高排名一定是1，即第一名，而最低排名的话，一开始知道是一个双指针的贪心，但是就是不知道怎么贪，后来看到大佬的想法豁然开朗：只要找到组合后大于等于x的个数有多少就好了。
- 具体找法：一个数组a大到小排序，一个数组b小到大排序，然后只要从a从头开始一个一个遍历，从b数组中从头找能与a数组的数加起来大于等于x的数就好了，然后记录个数，就是答案
- 心得：贪心题目是难想，想到之后就很简单，代码量也不大，还有一个比较考验的就是模拟的功力了（从人的思维去想想如果这不是一道编程题，而是一个生活的益智题，你会怎么做？然后再用代码实现,说的简单，感觉还是要多刷题）
- 代码如下

```c++
#include <algorithm>
#include <cstdio>

using namespace std;

const int M = 1e5 + 5;
int n, x, a[M], b[M];

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d%d", &n, &x);
    for (int i = 0; i < n; i++) {
        scanf("%d", a + i);
    }
    for (int i = 0; i < n; i++) {
        scanf("%d", b + i);
    }
    printf("1 ");
    sort(a, a + n);
    sort(b, b + n);
    reverse(a, a + n); //a从大到小排序
    int j = 0, ans = 0;
    for (int i = 0; i < n; i++) {
        while (j < n && b[j] + a[i] < x) {
            j++;
        }
        if (j >= n) break;
        ans++;
        j++;
    }
    printf("%d", ans);
    return 0;
}
```

