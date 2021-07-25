# UVA12558


## [Egyptian Fractions (HARD version)](https://vjudge.net/problem/UVA-12558)

- 题意：埃及分数问题困难版，问你一个真分数$a/b$，在表达成$a/b = 1/c + 1/d ……$，选的分数尽量少，相同情况下自小的分数尽量大，限制有$k(k \le 5)$个数不能用来做分母，让你求解
- 思路，IDA*的经典题，和普通埃及分数差不多的写法，就是要加一个限制，即有些数不能用
- 一开始写了一个分数的结构体，后来发现没必要，直接IDA*就好了
- 注意几个剪枝的地方，
  - 到达目标层时
    - 判断分子是否为1
    - 判断最后一个分母是否重复
    - 判断最后一个分母是否可用
    - 判断是否是更优解
  - 未达到目标层时
    - 预测选用当前的抉择是否会超出目标层（IDA*的关键剪枝）
    - 判断当前抉择的分母是否可用
- 代码如下

```c++
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;
using ll = long long;

ll kase = 0, can[1005], k, ans[10000], tmp[10000], a, b, x;

ll gcd(ll a, ll b) { ll m = 1; while(m) m = a % b, a = b, b = m; return a; }

bool dfs(ll now, const ll &h, ll first, ll a, ll b) {
    if (now == h) {
        if (a != 1) return false;
        if (b < 1001 && can[b]) return false;
        if (b == tmp[h - 2]) return false;
        tmp[h - 1] = b;
        if (ans[h - 1] == 0) memcpy(ans, tmp, sizeof(ll) * h);
        for (int i = h - 1; i >= 0; i--) {
            if (ans[i] == tmp[i]) continue;
            else if (ans[i] > tmp[i]) memcpy(ans, tmp, sizeof(ll) * h); 
            break;
        }
        return true;
    }
    bool ok = false;
    for (ll i = max(first, b / a) + 1; ; i++) {
        if (a * i >= (h - now + 1) * b) break;
        if (i < 1001 && can[i]) continue;
        tmp[now - 1] = i;
        ll g = gcd(b * i, a * i - b);
        if (dfs(now + 1, h, i, (a * i - b) / g, b * i / g)) ok = true;
    }
    return ok;
}

void solve() {
    scanf("%lld%lld%lld", &a, &b, &k);
    printf("Case %lld: %lld/%lld=", ++kase, a, b);
    while (k--) { scanf("%lld", &x); can[x] = true; }
    for (ll i = 1; ; i++) {
        if (dfs(1, i, 0, a, b)) {
            for (ll j = 0; j < i - 1; j++) printf("1/%lld+", ans[j]);
            printf("1/%lld\n", ans[i - 1]);
            break;
        }
    }
    memset(tmp, 0, sizeof tmp);
    memset(can, 0, sizeof can);
    memset(ans, 0, sizeof ans);
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) solve();
    return 0;
}
```

