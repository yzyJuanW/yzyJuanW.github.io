# 数位dp总结


# 数位dp总结

### 数位dp思考形式与套路

- 对于区间的询问，一般利用的是前缀和的思想来求得答案
- 在求解答案时，一般是从高位到低位开始对答案进行累计
- 求解数位dp时，更像是在求解树上的信息
- 某一位中在未超过限制位的信息可提前预处理出来
- 值得注意的
  - 大多数时候要记录前几位设置为最高数的信息
  - 若到了最后一位并将其设置为最高数时还需要特判
  - 0要根据题意思考是否需要特判
  - 允许前导0和非允许前导0是不一样的

- 一般递归版的数位dp求解代码

```cpp
int num[100], dp[100];

int dfs(int indx, int limit, /*参数根据题意来添加*/) {
    if (indx == 0) {
        return 1;// 根据题意来返回
    }
	int &ref = dp[indx];
    if (!limit && ref != -1) return ref;
    int res = 0;
    int up = (limit ? num[indx] : 9);
    for (int i = 0; i <= up; ++i) {
        // 更新res
		dfs(indx - 1, limit && i == up);
    }
    if (!limit) ref = res;
    return res;
}

int solve(int x) {
    if (!x) return 1; // 根据题意来决定返回值
    int len = 0;
    while (x) {
        num[++len] = x % 10;
        x /= 10;
    }
    return dfs(len, 1);
}
int main() {
    memset(dp, - 1, sizeof dp);
    // 此行省略读入
    cout << solve(r) - solve(l - 1) endl;
}
```

- 一般递推版的数位dp求解代码

```cpp
void prework() { 
    // 对每一位未超过最高数时的信息进行预处理
}

int solve(int x) {
    int num[10], len = 0, res = 0; // 不同题意res初始化不同，即一般是0算不算的区别
    if (!x) return 1;// 返回什么根据题意来
    while (x) { // 抠每一位出来
        num[++len] = x % 10;
        x /= 10;
    }
    int last = 0; // 记录前几位需要信息，初始化根据题意来
    for (int i = len; i >= 1; --i) {
        for (int j = 0; j < num[i]; ++j) { // 注意如果题目对前导0有要求可在第一位开始从1开始再到25行处处理第一位如果是0的情况
            if (/*j 和 last放在一起不符合题意*/) continue;
            res += // 对每一位填未超过此位的数字时的答案进行累加，一般这里利用到prework预处理出来的东西
        }
        // 来到这一步说明此位填num[i]
        if (/*此位填num[i]不符合题意*/) break;
        last += // 更新last
        if (last /*符合题意*/  && i == 1) {
            // 更新res
        }
    }
    for (int i = len - 1; i >= 1; --i) { // 假设不能有前导0，则枚举从次高位开始到低位
        for (int j = 1; j < 10; ++j) { // 枚举每一位都放1~9
            res += // 更新res
        }
    }
    return res;
}

int main() {
    prework();
    // 此行省略读入
    cout << solve(r) - solve(l - 1) endl;
}
```

### 例题

#### LOJ 10166 数字游戏

- 递归写法

```cpp
/*
由于科协里最近真的很流行数字游戏，某人又命名了一种取模数，这种数字必须满足各位数字之和 mod n为0。现在大家又要玩游戏了，指定一个整数闭区间[a,b]，问这个区间内有多少个取模数。
*/

#include <iostream>
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>
#include <cctype>
#define ll long long
#define pb push_back
ll IO() {
    ll x = 0;
    int f = 0, c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = 1;
        c = getchar();
    }
    while (isdigit(c)) x = (x << 1) + (x << 3) + c - '0', c = getchar();
    return f ? -x : x;
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
using namespace std;
const int N = 1e4 + 5, M = 1 << 10;
const ll mod = 1e9 + 7;
int num[N], n;

ll dp[20][101];
ll dfs(int x, int limit, int m) {
    ll &res = dp[x][m];
    if (!limit && ~res) return res;
    if (x == 0) return m == 0;
    ll sum = 0;
    int up = (limit ? num[x] : 9);
    for (int i = 0; i <= up; ++i) {
        sum += dfs(x - 1, limit && i == up, (m + i) % n);
    }
    if (!limit) res = sum;
    return sum;
}

ll solve(ll x) {
    if (!x) return 1;
    int len = 0;
    while (x) num[++len] = x % 10, x /= 10;
    return dfs(len, 1, 0);
}

void problem(ll l, ll r) {
    n = IO();
    memset(dp, -1, sizeof dp);
    print(solve(r) - solve(l - 1));
    puts("");
}



int main() {
    int n = 1, l, r;
    while (~scanf("%d %d", &l, &r)) problem(l, r);
    return 0;
}
```

- 递推写法

```cpp
#include <bits/stdc++.h>
#define ll long long
ll IO() {
    ll x = 0;
    int f = 0;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = 1;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + c - '0';
        c = getchar();
    }
    return f ? -x : x;
}
using namespace std;

ll dp[110][110];
int num[110], l, r, n;

int f(int x) {
    return (x % n + n) % n;
}

int solve(int x) {
    if (!x) return 1;
    int len = 0;
    while (x) {
        num[++len] = x % 10;
        x /= 10;
    }
    int last = 0, res = 0;
    for (int i = len; i >= 1; --i) {
        for (int j = 0; j < num[i]; ++j) {
            res += dp[i - 1][f(n - last - j)];
        }
        last = (num[i] + last) % n;
        if (last == 0 && i == 1) res += 1;
    }
    return res;
}


void problem() {
    dp[0][0] = 1;
    for (int i = 1; i <= 31; ++i) {
        for (int j = 0; j < n; ++j) {
            dp[i][j] = 0;
            for (int k = 0; k < 10; ++k) {
                // dp[i][j] += dp[i - 1][f(j + k)];
                dp[i][j] += dp[i - 1][f(j - k)];
            }
        }
    }
    printf("%d\n", solve(r) - solve(l - 1));
}


int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    // int t = 1;
    while (~scanf("%d %d %d", &l, &r, &n)) {
        problem();
    }
    return 0;
}
```

#### LOJ windy数

- 递归写法

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>
#include <cctype>
#define ll long long
#define pb push_back
ll IO() {
    ll x = 0;
    int f = 0, c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = 1;
        c = getchar();
    }
    while (isdigit(c)) x = (x << 1) + (x << 3) + c - '0', c = getchar();
    return f ? -x : x;
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
using namespace std;
const int N = 1e4 + 5, M = 1 << 10;
const ll mod = 1e9 + 7;
int num[N], n;

ll dp[20][15], len;
ll dfs(int x, int limit, int last) {
    if (x == 0) {
        return 1;
    }
    ll &ref = dp[x][last], sum = 0;
    if (!limit && ~ref) return ref;
    int up = (limit ? num[x] : 9);
    for (int i = 0; i <= up; ++i) {
        if (abs(last - i) < 2) continue;
        sum += dfs(x - 1, limit && up == i, last == 12 && i == 0 ? 12 : i);
    }
    if (~limit) ref = sum;
    return sum;
}

ll solve(ll x) {
    if (!x) return 1;
    len = 0;
    while (x) num[++len] = x % 10, x /= 10;
    return dfs(len, 1, 12);
}

void problem(ll l, ll r) {
    memset(dp, -1, sizeof dp);
    // print(solve(r));
    print(solve(r) - solve(l - 1));
}



int main() {
    int n = 1, l, r;
    while (~scanf("%d %d", &l, &r)) problem(l, r);
    return 0;
}
```

- 递推写法

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>
#include <cctype>
#define ll long long
#define pb push_back
ll IO() {
    ll x = 0;
    int f = 0, c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = 1;
        c = getchar();
    }
    while (isdigit(c)) x = (x << 1) + (x << 3) + c - '0', c = getchar();
    return f ? -x : x;
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
using namespace std;
const int N = 1e4 + 5, M = 1 << 10, mod = 1e8;
int dp[20][20];

void prework() {
    for (int i = 0; i < 10; ++i) dp[1][i] = 1;
    for (int i = 2; i <= 10; ++i) {
        for (int j = 0; j < 10; ++j) {
            for (int k = 0; k < 10; ++k) {
                if (abs(j - k) < 2) continue;
                dp[i][j] += dp[i - 1][k];
            }
        }
    }
}

int solve(int x) {
    int num[15], len = 0, res = 0;
    if (!x) return 1;
    while (x) num[++len] = x % 10, x /= 10;
    int last = 100;
    for (int i = len; i >= 1; --i) {
        for (int j = (i == len); j < num[i]; ++j) {
            if (abs(last - j) < 2) continue;
            res += dp[i][j];
        }
        if (abs(last - num[i]) < 2) break;
        last = num[i];
        if (i == 1) res += 1;
    }
    for (int i = len - 1; i >= 1; --i) for (int j = 1; j < 10; ++j)
        res += dp[i][j];
    return res + 1;
}

void problem() {
    int l = IO(), r = IO();
    prework();
    // print(solve(r));
    // puts("");
    print(solve(r) - solve(l - 1));
}



int main() {
    int n = 1;
    while (n--) problem();
    return 0;
}
```
