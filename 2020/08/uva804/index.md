# UVA804


## [Petri Net Simulation](https://vjudge.net/problem/UVA-804)

- 每个T变迁都有限制，必须是指定的P库有足够的token才行（注意，T指定的P库不一定只有一个），直接模拟就好了

```c++
#include <bits/stdc++.h>

using namespace std;

int p[110], np, nt, kase = 0;

struct T {
    int in[110], out[110];
    T() {
        memset(in, 0, sizeof in);
        memset(out, 0, sizeof out);
    }
};

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    for (; scanf("%d", &np), np;) {
        for (int i = 1; i <= np; i++) {
            scanf("%d", p + i);
        }
        scanf("%d", &nt);
        int x;
        T ts[110];
        for (int i = 1; i <= nt; i++) {
            for (; scanf("%d", &x), x;) {
                if (x < 0) ts[i].in[-x]++;
                else ts[i].out[x]++;
            }
        }
        int nf, n;
        scanf("%d", &nf);
        bool ok = true;
        for (n = 0; n < nf && ok;) {
            for (int i = 1; i <= nt; i++) {
                ok = true;
                for (int j = 1; j <= np; j++) {
                    if (p[j] < ts[i].in[j]) {
                        ok = false;
                        break;
                    }
                }
                if (ok) {
                    for (int j = 1; j <= np; j++) {
                        p[j] -= ts[i].in[j];
                        p[j] += ts[i].out[j];
                    }
                    n++;
                    break;
                }
            }
        }
        vector<int> ans;
        for (int i = 1; i <= np; i++) {
            if (p[i] != 0) {
                ans.push_back(i);
            }
        }
        if (n < nf) printf("Case %d: dead after %d transitions\n", ++kase, n);
        else printf("Case %d: still live after %d transitions\n", ++kase, nf);
        printf("Places with tokens:");
        for (auto i : ans) {
            printf(" %d (%d)", i, p[i]);
        }
        puts("\n");
        memset(p, 0, sizeof p);
    }

    return 0;
}
```

