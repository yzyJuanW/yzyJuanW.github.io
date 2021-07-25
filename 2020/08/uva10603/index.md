# UVA10603


## [Fill](https://vjudge.net/problem/UVA-10603)

- 题意：给你3个没有刻度的杯子，每个杯子的都有自己的容量，现利用这三个杯子量出体积为d的水，现在问最少的倒水量，如果量不到d，就量和d想接近的d'
- 按照书中的说法这是一个隐式图，求最短路，最短路的评判标志是倒水量

```c++
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>
#include <queue>

using namespace std;

const int M = 210;
int cap[3], d, ans[M];
bool vis[M][M];

struct nodes{
    int v[3], d;
    nodes(){}
    nodes(int v0, int v1, int v2, int d) :d(d) {
        v[0] = v0, v[1] = v1, v[2] = v2;
    }
    bool operator < (const nodes& tmp) const {
        return d > tmp.d;
    }
};


void init() {
    memset(ans, -1, sizeof ans);
    memset(vis, 0, sizeof vis);
}

void update(nodes &u) {
    for (int i = 0; i < 3; i++) {
        if (ans[u.v[i]] < 0 || ans[u.v[i]] > u.d) ans[u.v[i]] = u.d;
    }
}

void BFS() {
    init();
    scanf("%d%d%d%d", cap, cap + 1, cap + 2, &d);
    priority_queue<nodes> q;
    nodes s(0, 0, cap[2], 0);
    q.push(s);
    vis[0][0] = true;
    while (q.size()) {
        nodes u = q.top();
        q.pop();
        update(u);
        if (ans[d] > 0) break;
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (j == i) continue;
                if (!u.v[i] || u.v[j] == cap[j]) continue;
                int pour = min(cap[j], u.v[i] + u.v[j]) - u.v[j];
                nodes v = u;
                v.v[i] -= pour, v.v[j] += pour, v.d += pour;
                if (vis[v.v[0]][v.v[1]])continue;
                vis[v.v[0]][v.v[1]] = true;
                q.push(v);
            }
        }
    }
    while (ans[d] < 0 && d > 0) { d--; }
    printf("%d %d\n", ans[d], d);
}

int main () {
    int T;
    scanf("%d", &T);
    while (T--) BFS();
    return 0;
}
```

