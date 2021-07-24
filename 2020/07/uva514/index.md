# UVA514


## [Rails](https://vjudge.net/problem/UVA-514)

- 让你判断列车出站是否正确，栈的运用

```c++
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <stack>

using namespace std;

const int M = 1e3 + 10;
int n, arr[M];

int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    while (scanf("%d", &n), n) {
        while (scanf("%d", &arr[1]), arr[1]) {
            for (int i = 2; i <= n; i++) {
                scanf("%d", arr + i);
            }
            stack<int> st;
            int in = 1, out = 1;
            bool ok = true;
            while (out <= n) {
                if (in == arr[out]) {
                    in++, out++;
                } else if (st.size() && st.top() == arr[out]) {
                    st.pop();
                    out++;
                } else if (in <= n) {
                    st.push(in++);
                } else {
                    puts("No");
                    ok = false;
                    break;
                }
            }
            if (ok) puts("Yes");
            memset(arr, 0, sizeof arr);
        }
        puts("");
    }

    return 0;
}
```

