# Cutting_Banner


## [传送门](https://codeforces.com/problemset/problem/538/A)

- 题意：给你个字符串，减去一段子串，是否能将剩下的不改变顺序拼成"CODEFORCES"，能则输出"YES"，否则输出"NO"
- 思路：直接枚举减去的长度，然后暴力
- 代码如下

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <string>
#include <vector>

using namespace std;

char aim[] = "CODEFORCES", str[200], tmp[200];

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%s", str);
    if (strcmp(str, aim) == 0) {
        puts("YES");
        return 0;
    }
    int len = strlen(str);
    for (int i = 1; i <= len; i++) {
        for (int j = 0; j + i - 1 < len; j++) {
            int ind = 0;
            for (int k = 0; k < len; k++) {
                if (k < j || k > j + i - 1) {
                    tmp[ind++] = str[k];
                }
            }
            tmp[ind] = '\0';
            if (strcmp(tmp, aim) == 0) {
                puts("YES");
                return 0;
            }
        }
    }
    puts("NO");
    return 0;
}
```

