# UVA442


## [Matrix Chain Multiplication](https://vjudge.net/problem/UVA-442)

- 矩阵链乘，栈的运用

```c++
#include <bits/stdc++.h>

using namespace std;
using pii = pair<int, int>;

int n;
pii m[30];
char str[100000];
int main() {
#ifndef ONLINE_JUDGE
    freopen("test.in", "r", stdin);
    freopen("test.out", "w", stdout);
#endif
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        char a;
        int x, y;
        scanf("\n%c%d%d", &a, &x, &y);
        m[a - 'A'] = make_pair(x, y);
    }
    getchar();
    while (fgets(str, sizeof str, stdin)) {
        stack<pii> st;
        int len = strlen(str), ans = 0;
        if (str[len - 1] == '\n') str[len-- - 1] = '\0';
        bool ok = true;
        for (int i = 0; i < len; i++) {
            if (isalpha(str[i])) {
                st.push(m[str[i] - 'A']);
            } else if (str[i] == ')') {
                pii a = st.top();
                st.pop();
                pii b = st.top();
                st.pop();
                if (a.first != b.second) {
                    puts("error");
                    ok = false;
                    break;
                }
                ans += a.first * a.second * b.first;//b * a
                st.push(make_pair(b.first, a.second));
            }
        }
        if (ok) printf("%d\n", ans);
    }

    return 0;
}
```

