# UVA672


## [Parentheses Balance](https://vjudge.net/problem/UVA-673)

- 让你判断括号是否合法，栈的经典例题

```c++
#include <bits/stdc++.h>

using namespace std;

int t;

int main () {
    scanf("%d", &t);
    getchar();
    while (t--) {
        char str[150];
        fgets(str, sizeof str, stdin);
        stack<char> st;
        for (int i = 0; str[i] != '\n'; i++) {
            if (str[i] == ')' && st.size() && st.top() == '(') st.pop();
            else if (str[i] == ']' && st.size() && st.top() == '[') st.pop();
            else st.push(str[i]);
        }
        if (st.empty()) puts("Yes");
        else puts("No");
    }
    return 0;
}
```

