# Arc123C 1, 2, 3 - Decomposition


题意：把一个数拆成x个数相加，这x个数的十进制每一位都必须是 1,2,3中的数

分治地思维想

将数拆成两部分 : 

**（部分a：除了个位的其余位的数）（部分b：个位的数）**

如果”部分b“最少可以拆成x次，同时“部分a”拆的次数不超过x次，则整体的次数便是x

例如 29 可以拆成 （部分a：2） 和 （部分b：9）

可以发现9的最少次数为3次（3, 3, 3），而”部分a“的最少次数为1次（2），则29可以拆3次（23，3，3）

注意到“部分b”是可以向前借位的，此时“部分b”的便可以在实现拆4次以上，

我们可以枚举“部分b”的次数，然后看看“部分a”的次数是否满足上述条件，从小枚举，符合便`return`，则答案就是最小

其实可以发现，如果“部分b”的次数为5时，b的范围就是$0 \le b \le 9$，其中0到5的范围是向前借了位，5到9是没有借位的范围。

这说明次数是不会超过5次的，保险起见枚举9次

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
#include <unordered_map>
using namespace std;

typedef long long ll;
unordered_map<ll, int> mp;
int cal(ll x) {
    if (x == 0) return 0;
    if (mp.count(x)) return mp[x];
    for (int i = 1; i <= 9; ++i) { // 枚举次数
        for (int b = i; b <= i * 3; ++b) { // 枚举部分b是什么
            if ((x - b) % 10 != 0) continue;
            if (cal((x - b) / 10) <= i) return mp[x] = i;
        }
    }
}

int main() {
    int t = 1;
    scanf("%d", &t);
    while (t--) {
        ll n;
        scanf("%lld", &n);
        printf("%d\n", cal(n));
    }
    return 0;
}
```
