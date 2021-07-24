# UVA806


## [Spatial Structures](https://vjudge.net/problem/UVA-806)

- 类似于计算像素的，读懂题意直接模拟就好了
- 毒瘤题

```c++
#include <bits/stdc++.h>

using namespace std;
using ll = long long;
int n, kase = 0;
char g[70][70], m[70][70];
vector<ll> ans;

void dfs(int r1, int c1, int r2, int c2, ll all, int d, int pos) {
    if (r1 == r2) {
        if (m[r1][c1] == '1') ans.push_back(all + pos * pow(5, d - 1));
        return;
    }
    int sum1 = 0, sum2 = 0, sum3 = 0, sum4 = 0;
    for (int i = r1; i <= (r2 + r1) / 2; i++) {
        for (int j = c1; j <= (c2 + c1) / 2; j++) {
            sum1 += m[i][j] - '0';
        }
    }
    for (int i = r1; i <= (r2 + r1) / 2; i++) {
        for (int j = (c1 + c2) / 2 + 1; j <= c2; j++) {
            sum2 += m[i][j] - '0';
        }
    }
    for (int i = (r1 + r2) / 2 + 1; i <= r2; i++) {
        for (int j = c1; j <= (c1 + c2) / 2; j++) {
            sum3 += m[i][j] - '0';
        }
    }
    for (int i = (r1 + r2) / 2 + 1; i <= r2; i++) {
        for (int j = (c1 + c2) / 2 + 1; j <= c2; j++) {
            sum4 += m[i][j] - '0';
        }
    }
    int sum = sum1 + sum2 + sum3 + sum4;
    ll x = 0;
    if (d) x = all + pos*pow(5, d - 1);
    if (sum == (r2 - r1 + 1) * (c2 - c1 + 1)) {
        if (x) ans.push_back(x);
        if (d == 0) ans.push_back(0);
        return;
    }
    if (sum1 != 0) dfs(r1, c1, (r2 + r1) / 2, (c2 + c1) / 2, x, d + 1, 1);
    if (sum2 != 0) dfs(r1, (c1 + c2) / 2 + 1, (r2 + r1) / 2, c2, x, d + 1, 2);
    if (sum3 != 0) dfs((r1 + r2) / 2 + 1, c1, r2, (c1 + c2) / 2, x, d + 1, 3);
    if (sum4 != 0) dfs((r1 + r2) / 2 + 1, (c1 + c2) / 2 + 1, r2, c2, x, d + 1, 4);
}


int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    bool flag = true;
    for (; scanf("%d", &n), n;) {
        if (flag) flag = !flag;
        else puts("");
        if (n < 0) {
            int x;
            for (int i = 1; i <= -n; i++)
                for (int j = 1; j <= -n; j++)
                    g[i][j] = '.';
            for (; scanf("%d", &x), ~x;) {
                int tmp[100] = {0}, len;
                int r1 = 1, c1 = 1 , r2 = -n, c2 = -n;
                for (len = 0; x; len++) {
                    tmp[len] = x % 5;
                    x /= 5;
                }
                for (int i = 0; i < len; i++) {
                    int tp = tmp[i];
                    if (tp == 1) r2 = (r2 + r1) / 2, c2 = (c2 + c1) / 2;
                    if (tp == 2) c1 = (c1 + c2) / 2 + 1, r2 = (r1 + r2) / 2;
                    if (tp == 3) r1 = (r1 + r2) / 2 + 1, c2 = (c1 + c2) / 2;
                    if (tp == 4) r1 = (r1 + r2) / 2 + 1, c1 = (c1 + c2) / 2 + 1;
                }
                for (int i = r1; i <= r2; i++)
                    for (int j = c1; j <= c2; j++)
                        g[i][j] = '*';
            }
            printf("Image %d\n", ++kase);
            for (int i = 1; i <= -n; i++) {
                for (int j = 1; j <= -n; j++){
                    printf("%c", g[i][j]);
                }
                puts("");
            }
        }else {
            for (int i = 1; i <= n; i++) scanf("%s", &m[i][1]);
            dfs(1, 1, n, n, 0, 0, 0);
            int len = ans.size();
            sort(ans.begin(), ans.end());
            printf("Image %d\n", ++kase);
            int h = 0, f = 1;
            for (int i = 0; i < len; i++) {
                if (f) f = 0;
                else printf(" ");
                printf("%lld", ans[i]);
                if (++h == 12 && i != len - 1) {
                    h = 0;
                    f = 1;
                    puts("");
                }
            }
            if (len != 0) puts("");
            printf("Total number of black nodes = %d\n", len);
            ans.clear();
        }
    }
    return 0;
}
```

