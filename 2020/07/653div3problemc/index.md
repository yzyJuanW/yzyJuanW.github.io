# C. Move Brackets题解

[传送门](https://codeforces.com/contest/1374/problem/C)

## 题意

- 给你一串字符串，只有"(" h和")"组成，你每次都能选择一个字符把它移到最左边或者最右边，问你最少移动几次可以使得字符串的括号合法

## 思路

- 一道贪心题（比赛的时候没看出来，太菜了😭）
- 只要计算出合法的括号对，然后用总长度减去合法的括号对的个数，即剩余的不合法长度除以 2 就是最少的步数
- 例如 ： "()))))((((()", 去掉合法的后就是"))))((((", 那么移动的长度就是$8 \div 2 = 4$就是答案
- 这题我一开始想复杂了，对于上一个样例，我以为必须要把下标为2 ~ 5（从0开始）的先一道最左边，再把（原本的）6 ~ 9移到最左边，变成这样："(((())))()()",总次数是8，所以就很搞了，然而正确答案是把6 ~ 9 移到最左边就合法了，就是这样："((((()))))()",总次数就是 4
- 所以最终的思路就是判断合法的有多少对：在博客中遇到了两种方法，一种是用栈，一种是不用栈（根据括号合法的特性一定要左括号先出现， 即从头到尾遍历，用一个变量来储存左括号出现次数，一旦遇到右括号变量值就自减）
- 代码如下（用栈）：

```c++
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef vector<int> vi;
#define debug printf("(hao)")
#define all(x) x.begin(), x.end()
#define rep(i, a, b) for (int i = (a); i < (b); i++)
#define clr(a, v) memset(a, v, sizeof(a))

void solve() {
    int n;
    char bra[55];
    stack<char> res;
    scanf("%d", &n);
    scanf("%s", bra);

    for (int i = 0; i < n; i++) {
        if (res.empty()) res.push(bra[i]);  //栈为空，就无论什么括号都放进去
        else if (bra[i] == ')')             //如果是右括号
            if (res.top() == '(') res.pop();//栈顶是左括号，就弹出
            else res.push(bra[i]);          //否则把右括号入栈
        else res.push(bra[i]);              //如果是左括号，就直接入栈
    }
    printf("%d\n", (int)res.size() >> 1);
}

// #define LOCAL
int main() {
    std::ios::sync_with_stdio(false);
#ifdef LOCAL
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    int T;
    scanf("%d", &T);
    while (T--)
        solve();
    return 0;
}
```

## 反思

- 这题我最开始是误会了求最小移动步数，后来发现是操作次数，还是没看出要贪心
- 遇到括号就直接想想括号的一些小性质（例如：合法的括号长度一定是偶数、括号的合法一定是左括号在前等等）
- 有括号优先考虑是否要用到栈来辅助
  
## 总结

- cf的前几题不是思维就是贪心，代码不难，但是找规律难
- 多刷这种题，我觉得这也是解决一些困难题目的基础

