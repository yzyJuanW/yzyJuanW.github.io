# UVA1605


## [Building for UN](https://vjudge.net/problem/UVA-1605)

- 题意：有一栋楼，有$n(n \le 50)$个国家要开会，每个国家至少分配一个位置，让你输出一种方案，H层，每层W行L列，使得任意两个国家要能相邻，层数也算相邻
- 按紫书说的构造一种解出来，只需要两层就好了，每层$n \times n$的格子，第一层第i行是第i个国家,第二层第j列是第j个国家，
- 这个模型有点像放筷子，有n双筷子，分别都有左和右，要想让每个左筷子都能接触到其他右筷子的方案就是在第一层筷子横着放，第二层竖着放就行了
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

int n;
char str[55];

void solve() {
    printf("%d %d %d\n", 2, n, n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) printf("%c", str[i]);
        puts("");
    }
    puts("");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) printf("%c", str[j]);
        puts("");
    }
}

int main() {
    for (int i = 0; i < 26; i++) str[i] = 'a' + i;
    for (int i = 0; i < 26; i++) str[26 + i] = 'A' + i;
    while (~scanf("%d", &n)) solve();
    return 0;
}
```

