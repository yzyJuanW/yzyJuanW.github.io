# UVA225


## [Golygons](https://vjudge.net/problem/UVA-225)

- 题意：一个网格图，现在给你图中$k(k \le 50)$个障碍的坐标（坐标可能为负），不能继续上次的方向继续走也不能后退，这就意味着只能左右走，第$i$次走$i$个单位，从(0, 0)点出发，走了$n(n \le 20)$次恰好回到(0, 0)点，让你输出所有可能的路线，按字典序输出
- 因为深度已知，所以我才用了DFS的方法，考虑到坐标可能有负数，所以我把坐标原点移动了一下，将(1000, 1000)记为原点，其余点也一样有同样变化，因为 $n \le 20$， 所以1000够用
- 将答案用vector<char\>的一维数组存起来，因为vector里面自带字典序的排序，所以最后可以直接sort(ans, ans + num)排序（详细看代码），当然用vector<string\>来存，这样就不用再而外加多一维数组了，string也有push_back()（用append()也可以）和pop_back()函数，这样最后的排序就必须是sort(ans.begin(), ans.end());
- 一个方案中一个坐标不能被访问两次，用个vis的布尔数组来记录是否被访问过
- dfs两次，因为最开始是4个方向都可以走的
- 最后记得回溯
- 代码如下

```c++
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <vector>

using namespace std;

const int M = 1e3;
int t, k, bn, num;
bool isb[M + M][M + M], vis[M + M][M + M];
const char dir[] = "ewns";
const int dx[] = {1, -1, 0, 0};
const int dy[] = {0, 0, 1, -1};
vector<char> ans[1000], tmp;

void init() {
    num = 0;
    memset(isb, 0, sizeof isb);
    memset(vis, 0, sizeof vis);
    for (auto &i : ans) i.clear();
}

void dfs(int a, int b, int n, int d) {
    if (n == k) {
        if (a != M || b != M) return;
        ans[num++] = tmp;
        return;
    }
    for (int i = d; i < 4 - 2 + d; i++) {
        int newx = a, newy = b;
        bool flag = true;
        for (int j = 0; j <= n; j++) {//走n+1步
            newx += dx[i], newy += dy[i];
            if (isb[newx][newy]) { flag = false; break; }//遇到障碍就不行
        }
        if (!flag || vis[newx][newy]) continue;
        vis[newx][newy] = true;
        tmp.push_back(dir[i]);
        dfs(newx, newy, n + 1, 2 - d);
        tmp.pop_back();
        vis[newx][newy] = false;
    }
}

int main () {
    scanf("%d", &t);
    while (t--) {
        init();
        scanf("%d%d", &k, &bn);
        while (bn--) {
            int x, y;
            scanf("%d%d", &x, &y);
            isb[x + M][y + M] = true;//障碍
        }
        dfs(M, M, 0, 0);
        dfs(M, M, 0, 2);
        sort(ans, ans + num);//排序
        for (int i = 0; i < num; i++) {
            for (auto x : ans[i]) printf("%c", x);
            puts("");
        }
        printf("Found %d golygon(s).\n\n", num);
    }

    return 0;
}
```

