# UVA129


## [Krypton Factor](https://vjudge.net/problem/UVA-129)

- 定义一个串是容易串：有相邻的相同的字串；反之就是困难串
- 让你求只利用前k个大写字母的第n个困难串
- dfs模拟一下，值得注意的是剪枝时只看当前串的后缀即可，例如遍历到ABCDEFGH时，就只判断H和G、GH和EF、FGH和CDE、EFGH和ABCD就行了
- 代码如下

```c++
#include <cstdio>
#include <cstring>

using namespace std;

int n, L, num = 0;
bool ok = true;
char ans[100], a[50], b[50];

bool check(int len) {
    if (!len) return false;
    for (int i = 1; i + i <= len + 1; i++) {
        memcpy(a, &ans[len - i + 1], sizeof(char) * i);
        memcpy(b, &ans[len - i - i + 1], sizeof(char) * i);
        a[i] = '\0', b[i] = '\0';
        if (strcmp(a, b) == 0) return true;
    }
    return false;
}

void dfs(int cur) {
    if (num > n) return;
    if (num == n) {
        size_t len = strlen(ans), s = 0, l = 0;
        for (size_t i = 0; i < len; i++) {
            if (s++ == 4) { if (l != 15) printf(" "); s = 1, l++; }
            if (l == 16) { puts(""); l = 1; }
            printf("%c", ans[i]);
        }
        printf("\n%zu\n", len);
        return;
    }
    for (int i = 0; i < L; i++) {
        ans[cur] = i + 'A', ans[cur + 1] = '\0';
        if (check(cur)) continue;
        num++;
        dfs(cur + 1);
    }
}
int main() {
    while (scanf("%d%d", &n, &L), n + L) {
        num = 0;
        dfs(0);
        memset(ans, 0, sizeof ans);
    }
    return 0;
}
```

