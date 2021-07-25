# UVA10410


## [Tree Reconstruction](https://vjudge.net/problem/UVA-10410)

- 题意：给你一个DFS和一个BFS遍历的树（具体遍历方式都是小的权值优先），让你求出每个节点的子节点
- 直接说思路：栈的运用，题目说输出任意可能的答案，所以把树当成一个二叉树来做，bfs本质就是树的一层一层的遍历，当一个节点在bfs序中下标为 $x$，则 $x + 1$可以认为是他的兄弟节点，其后面的都是下层的节点；而dfs的本质是一个链式的深度搜索，也就是说dfs中连续的很可能就是树的一条链式。根据以上性质，用栈来模拟dfs的建造过程，再更具上诉bfs的性质来判断某节点是否是栈顶的一个子节点
- 代码如下

```c++
#include <bits/stdc++.h>

using namespace std;

int n, x;
int main() {
    for (; ~scanf("%d", &n);) {
        vector<int> node[1010], dfs(n), bfs(n + 1);
        for (int i = 1; i <= n; i++) {
            scanf("%d", &x);
            bfs[x] = i;
        }
        for (auto &i : dfs) scanf("%d", &i);
        stack<int> st;
        int root = dfs[0];
        st.push(root);
        for (int i = 1; i < n; i++) {
            for (;;) {
                int fa = st.top();
                if (bfs[fa] + 1 < bfs[dfs[i]] || fa == root) {
                    node[fa].push_back(dfs[i]);
                    st.push(dfs[i]);
                    break;
                } else {
                    st.pop();
                }
            }
        }
        for (int i = 1; i <= n; i++) {
            printf("%d:", i);
            for (auto it : node[i]) {
                printf(" %d", it);
            }
            puts("");
        }
    }
    return 0;
}
```

