# UVA839


## [Not so Mobile](https://vjudge.net/problem/UVA-839)

- 题意：天平平衡，题意很好懂，就是让你判断给的天平平不平衡，直接递归读入就好了

```c++
#include <bits/stdc++.h>

using namespace std;

int t, w;
bool ok;

void read(int &w) {
    int wl, dl, wr, dr;
    scanf("%d%d%d%d", &wl, &dl, &wr, &dr);
    if (wl == 0) read(wl);
    if (wr == 0) read(wr);
    w = wl + wr;
    if (wl * dl != wr * dr) ok = false;
}

int main() {
    scanf("%d", &t);
    while (t--) {
        ok = true;
        read(w);
        if (ok) puts("YES");
        else puts("NO");
        if (t) puts("");
    }
    return 0;
}
```

- 总结：这题是一个很特殊的树，在输入时就开始递归，值得学习

