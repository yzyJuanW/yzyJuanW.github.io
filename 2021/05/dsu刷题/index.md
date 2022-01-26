# 树上dsu总结


# dsu 树上启发式合并 刷题

## 套路代码

```cpp
int son[M], dep[M], siz[M]; // son记录重儿子
// 寻找重儿子
void dfs(int u, int fa) {
	siz[u] = 1;
	dep[u] = dep[fa] + 1; // 记录深度，有些题不需要
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v == fa) continue;
		dfs(v, u);
		siz[u] += siz[v];
		if (siz[v] > siz[son[u]]) son[u] = v;
	}
}
int flag;
void cal(int u, int fa, int k) {
	// 计算
	// 遍历孩子，并利用 flag 避开重儿子
}

void dsu(int u, int fa, int keep) {
	for (/*遍历下一个结点*/) {
		v = /*下一个结点*/;
		if (v != fa && v != son[u]) dsu(v, u, 0);
	}
	if (son[u]) dsu(son[u], u, 1), flag = son[u]; // 如果存在重儿子
	cal(u, fa, 1); // 计算
	if (flag) flag = 0;
	if (!keep) cal(u, fa, -1); //删除
}
```

## 例题

### [CF570D Tree Requests](https://www.luogu.org/problemnew/show/CF570D)

```cpp
/*
题意：
有一棵 个结点的以 11 号结点为根的有根树。
每个结点都有一个颜色，颜色是以编号表示的，ii 号结点的颜色编号为 cici
如果一种颜色在以 xx 为根的子树内出现次数最多，称其在以 xx 为根的子树中占主导地位。
显然，同一子树中可能有多种颜色占主导地位。
你的任务是对于每一个 i∈[1,n]i∈[1,n]，求出以 ii 为根的子树中，占主导地位的颜色的编号和。
N<=105,ci<=n
*/
#include <algorithm>
#include <cstdio>
#include <cstring>
#define ll long long
#define rep(u, v, e) for (int i = head[u], v = e[i].to; ~i; i = e[i].next, v = e[i].to)
using namespace std;
const int N = 1e5 + 5, M = 2e5 + 5;

int head[N], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = -1; }

struct edges {
    int to, next;
    int w;
    void add(int t, int n, int w) {
        to = t, next = n, this->w = w;
    }
}edge[N << 1]; //无向图则需要乘2

inline void add(int u, int v, int w = 1) {
    edge[++cnt].add(v, head[u], w);
    head[u] = cnt;
}
int son[N], siz[N];
void dfs(int u, int fa) {
    siz[u] = 1;
    rep(u, v, edge) {
        if (v == fa) continue;
        dfs(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v;
    }
}
ll col[N], ans[N], flag, num[N], now, sum, tot;

void cal(int u, int fa, int k) {
    num[col[u]] += k;
    if (k > 0) {
        if (num[col[u]] == tot) sum += col[u];
        else if (num[col[u]] > tot) tot = num[col[u]], sum = col[u];
    }
    rep(u, v, edge) {
        if (v == fa || v == flag) continue;
        cal(v, u, k);
    }
}

void dsu(int u, int fa, int keep) {
    rep(u, v, edge) {
        if (v == fa || v == son[u]) continue;
        dsu(v, u, 0);
    }
    if (son[u]) dsu(son[u], u, 1), flag = son[u];
    now = u;
    cal(u, fa, 1);
    ans[u] = sum;
    if (flag) flag = 0;
    if (!keep) cal(u, fa, -1), tot = 0;
}

int main() {
    int n;
    scanf("%d", &n);
    init();
    for (int i = 1; i <= n; ++i) scanf("%lld", col + i);
    for (int i = 1; i < n; ++i) {
        int u, v;
        scanf("%d %d", &u, &v);
        add(u, v), add(v, u);
    }
    dfs(1, 0);
    dsu(1, 0, 1);
    for (int i = 1; i <= n; ++i) printf("%lld ", ans[i]);
    return 0;
}
```

- 反思：本题第二次做，sum值必须要开全局且是单值，而不是数组（即记录每个节点的答案），而是如代码所示的做法，直接用ans在dsu内复制，用sum值不开数组开全局的好处就是会一直保留重儿子的信息‘

### [CF570D Tree Requests](https://www.cnblogs.com/StarRoadTang/p/14033777.html)

```cpp
/* 
给定一个以1为根的n个节点的树，每个点上有一个字母(a−z)
每个点的深度定义为该节点到1号节点路径上的点数.
每次询问 a,b， 查询以 a 为根的子树内深度为 b 的节点上的字母重新排列之后是否能构成回文串.
*/

```


