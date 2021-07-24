# UVA11212


## [Editing a Book](https://vjudge.net/problem/UVA-11212)

- 题意：给你一个排列，你一次能够剪切一段字串，然后在别的地方插入粘贴，让你求最少的剪切粘贴次数使得排列是1~n按顺序的
- IDA*的题，每次规定递归的层数，长度为n的排列最多是剪切粘贴n - 1次
- 剪枝精髓：移动一次最多改变 3 个数字的后继数字，例如从{a，b，c，d}到{a, c, b, d},只有a, c, b（字母均代表区间）三个区间最后一个数的后继改变了
- 假设当前递归到d层，还有h个数字的后继不正确（最后一个数字的评判依据是是否是最后一个数，即$n - 1$），的最少还要遍历 $d + h / 3$ 层，当前设置了最大层数为Max, 则当 $d + h / 3 > Max$ 即 $3 \times d + h > 3 \times Max$ 就剪枝
- 代码如下，跑了300ms

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

int n, arr[15], tmp[15], kase = 0;

bool check() {
    for (int i = 0; i < n; i++) { if (arr[i] != i + 1) return false; }
    return true;
}

int h() {
    int cnt = 0;
    for (int i = 0; i < n - 1; i++) { if (arr[i] + 1 != arr[i + 1]) cnt++; }
    if (arr[n - 1] != n) cnt++;
    return cnt;
}

bool DFS(int d, int Max) {
    if (check()) return true;  //完成走人
    if (d * 3 + h() > 3 * Max) return false;//剪枝
    int old[15], cut[10];
    memcpy(old, arr, sizeof arr);
    for (int i = 0; i < n; i++) {
        if (i == 0 || old[i] != old[i - 1] + 1) { //剪枝，只选取错误的剪切
            for (int j = i; j < n; j++) {
                int cnt = 0, len = j - i + 1;
                for (int k = 0; k < n; k++) {
                    if (k < i || k > j) cut[cnt++] = arr[k];
                }
                for (int k = 0; k <= cnt; k++) {
                    memcpy(tmp, cut, sizeof(int) * k);
                    memcpy(&tmp[k], &arr[i], sizeof(int) * (len));
                    memcpy(&tmp[k + len], &cut[k], sizeof(int) * (cnt - k));
                    memcpy(arr, tmp, sizeof(int) * n);
                    if (memcmp(arr, old, sizeof(int) * n) == 0) continue;//和之前的一样，剪枝
                    if (DFS(d + 1, Max)) return true;
                    memcpy(arr, old, sizeof arr);
                }
            }
        }
    }
    return false;
}

int main () {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    while (scanf("%d", &n), n) {
        for (int i = 0; i < n; i++) { scanf("%d", arr + i); }
        if (check()) { printf("Case %d: 0\n", ++kase); continue; }
        for (int i = 1; i < n; i++) {
            if (DFS(0, i)) { printf("Case %d: %d\n", ++kase, i); break; }
        }
    }
    return 0;
}
```

