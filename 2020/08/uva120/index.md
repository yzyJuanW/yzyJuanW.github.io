# UVA120


## [Stacks of Flapjacks](https://vjudge.net/problem/UVA-120)

- 题意：给你一个序列，每次选一个数k，从序列的后往前数第k个数然后把它及它之前时数都翻转一下，问你一种选法使得序列从小到大排好序
- 有点像选择排序，不过为了不影响后面的，应该先把排好最大的数，如果已经就位就排第二大的数，具体策略是先把它调到最前面再调到相应的正确位置，一直排到有序为止
- 代码如下

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>

using namespace std;

int cnt, arr[50], tmp[50];
char line[150];

void solve() {
    memcpy(tmp, arr, sizeof(int) * cnt);
    sort(tmp, tmp + cnt);
    int indx = cnt - 1;
    for (int i = cnt - 1; i >= 0; i--) {
        if (tmp[indx] == arr[i]) {
            indx--;
        } else {
            int nums = i, k = cnt - i;
            while (arr[nums] != tmp[indx]) { nums--, k++; }
            if(nums != 0) { reverse(arr, arr + nums + 1); printf("%d ", k); }
            reverse(arr, arr + i + 1);
            printf("%d ", cnt - i);
            indx--;
        }
    }
    puts("0");
}

bool read() {
    if (fgets(line, sizeof line, stdin) == nullptr) return false;
    printf("%s", line);
    char *tmp = line;
    cnt = 0;
    while (1) {
        sscanf(tmp, "%d", &arr[cnt++]);
        tmp = strchr(tmp, ' ');
        if (tmp == nullptr) break;
        tmp++;
    }
    return true;
}

int main() {
    while (read()) solve();
    return 0;
}
```

