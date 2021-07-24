# E1. Reading Books (easy version)题解


[传送门](https://codeforces.com/contest/1374/problem/E1)

## 题意

- 小A和小B要读书，现在共又 n 本书，对于每本书都有三个参数，$t_i$：阅读的时间、$a_i$：小A喜欢读的书、$b_i$：小B喜欢读的书
- 现在小A和小B都要读 k 本书，但是他俩只读自己喜欢的书
- 问你小A和小B最少要读多久可以读到 k 本书，（对于都喜欢的书，时间是可以只算一次的）
- 如果他俩有一个没读到 k 本书就输出 -1

## 思路

- 一道思维贪心题~~cf好狠呀~~，网上有很多的解法，大多数都是模拟+贪心，但是我看到了一位大佬的贪心思路，醍醐灌顶，下面就用它的思路了
- 对于每个书本可以分为以下四类
  - 小A和小B都喜欢的书
  - 只有小A喜欢的书
  - 只有小B喜欢的书
  - 他俩都不喜欢的书
- 显然最后一类可以不管~~不要呀，看书长知识~~，目前只考虑第二三类的书，可以分别用数组$a_n$和$b_n$记录这些书的阅读时间，然后从小到大排序，精彩的来了，把$a_i 和 b_i$合成一本书当作是第一类的书（妙呀,我怎么没想到，~~太笨了~~，太菜了，刷题少），然后放入第一类，再来对第一类的书的阅读时间排序，选前 k 个书，记录阅读时间就是最终答案
  代码如下

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef vector<int> vi;
typedef vector<long long> vll;
typedef pair<int, int> pii;
typedef pair<long long, long long> pll;
#define debug printf("(hao)")
#define all(x) x.begin(), x.end()
#define rep(i, a, b) for (int i = (a); i < (b); i++)
#define clr(a, v) memset(a, v, sizeof(a))

const int M = 2e5 + 10;
vi a, b, t;

void solve() {
    int n, k;
    scanf("%d%d", &n, &k);
    rep(i, 0, n) {
        int _t, _a, _b;
        scanf("%d%d%d", &_t, &_a, &_b);
        if (_a && _b) t.push_back(_t);  // 第一类
        else if (_a) a.push_back(_t);   // 第二类
        else if (_b) b.push_back(_t);   //第三类
    }
    sort(all(a));
    sort(all(b));
    rep(i, 0, (int)min(a.size(), b.size())) t.push_back(a[i] + b[i]);//精辟之处

    if ((int)t.size() < k) {//如果不够 k 本，就输出 -1
        printf("-1\n");
        return;
    }
    sort(all(t));
    int res = 0;
    rep(i, 0, k) res += t[i];
    printf("%d\n", res);
}

// #define LOCAL
int main() {
    std::ios::sync_with_stdio(false);
#ifdef LOCAL
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    // int T;
    // scanf("%d", &T);
    // while (T--)
    solve();
    return 0;
}
```

## 反思

- 比赛的时候没做到这题，一个字：菜
- 一开始用结构体来分析每一本书，后来才学到了把书本分类，然后贪心
- 贪心渐渐地不会了
- 以后cf前几题看看和贪心有没有关系

## 总结

- 这个结构题的题很巧妙地把他拆开，从而没有用到结构题，好处就是排序压力小
- 这场比赛很喜欢思维题和贪心
- 当有两个（或多个）共同影响时，要考虑把他们拆开来计算
- ……

