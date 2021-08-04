# 数据结构板子


### 1. 并查集

#### 简便的路径压缩版

```cpp
const int Max = 1e5 + 10;
int fa[Max];
inline void init() { for (int i = 0; i < Max; i++) fa[i] = i; }

int findfa(int x) {
    return x == fa[x] ? x : fa[x] = findfa(fa[x]);
}

void Un(int a, int b) {
    int fa1 = findfa(a);
    int fa2 = findfa(b);
    if (fa1 != fa2) fa[fa1] = fa2;
}
```

#### 网络赛版

```cpp
class UF {
public:
    vector<int> parent;
    vector<int> size;
    int n;
    // 当前连通分量数目
    int cnt;
    
public:
    UF(int _n): n(_n), cnt(_n), parent(_n), size(_n, 1) {
        int i = 0;
        for (auto &x : parent) x = i++;
    }
    
    int findset(int x) {
        return parent[x] == x ? x : parent[x] = findset(parent[x]);
    }
    
    bool unite(int x, int y) {
        x = findset(x);
        y = findset(y);
        if (x == y) {
            return false;
        }
        if (size[x] < size[y]) {
            swap(x, y);
        }
        parent[y] = x;
        size[x] += size[y];
        --cnt;
        return true;
    }
    
    bool conn(int x, int y) {
        x = findset(x);
        y = findset(y);
        return x == y;
    }
};
```

### 2. 树状数组

#### 单点修改与区间查询

```cpp
// 修改复杂度与查询复杂度O(logn)
#define lb(x) ((x) & (-x))
#define ll long long
const int N = 1e6 + 5, M = 2e5 + 5;
ll n, m, a[N], bit[N];
// 初始化
void build(int n) {
    for (int i = 1; i <= n; ++i) {
        bit[i] += a[i];
        int j = lb(i) + i;
        if (j <= n) bit[j] += bit[i];
    }
}
// 单点修改
void update(int index, ll val, int n) {
    while (index <= n) {
        bit[index] += val;
        index += lb(index);
    }
}
// 前缀查询
ll get(int index, int n) {
    ll res = 0;
    while (index) {
        res += bit[index];
        index -= lb(index);
    }
    return res;
}
// 区间和查询
ll get(int l, int r, int n) { return get(r, n) - get(l - 1, n); }
```

#### 区间修改，区间查询

1. 设$d[i] = a[i] - a[i - 1]$
2. 则 $a[ x ] = \sum_{i = 1}^{x}{d_i}$
3. 设 $sum[ x ] = \sum_{i = 1}^{x}{a_i}$
4. 即 $sum[ x ] = d[1] + d[1] + d[2] + d[1] + d[2] + d[3] + …… + d[1] + ……+ d[n]$
5. 化简得 $sum[ x ] = \sum_{i = 1}^{x}{d_i \times (n - i + 1)}$
6. 得 $sum[ x ] = (n + 1) \times \sum_{i = 1}^{x}{d_i} - \sum_{i = 1}^{x}{i \times d_i}$
7. 固开两个树状数组，一个维护差分数组$d_i$，一个维护$i \times d_i$

```cpp
#define lb(x) ((x) & (-x))
#define int long long
const int N = 5e3 + 5, M = 1e6 + 5;
int n, m, d[M], id[M];
// 基础树状数组单点更新
void update(int i, int val, int *bit) {
    while (i <= n) {
        bit[i] += val;
        i += lb(i);
    }
}
// 单点修改
void update(int i, int val) {
    update(i, val, d), update(i, val * i, id);
}
// 区间修改
void update(int l, int r, int val) {
    update(l, val, d), update(r + 1, -val, d);
    update(l, l * val, id), update(r + 1, (-val) * (r + 1), id);
}
// 前缀查询
int get(int i, int *bit) {
    int res = 0;
    while (i) res += bit[i], i -= lb(i);
    return res;
}
// 区间和查询
int get(int l, int r) {
    int res = get(r, d) * (r + 1) - get(r, id);
    res -= get(l - 1, d) * l - get(l - 1, id);
    return res;
}
```

#### 网络赛类封装版

```cpp
template<class T>
class BIT{
    inline int lb(int x) { return x & (-x); }
    vector<T> bit;
    T getSum(int i) {
        T ret = 0;
        while (i > 0) {
            ret += bit[i];
            i -= lb(i);
        }
        return ret;
    }
    int n;
public:
    BIT (int n, T val) : bit(n + 1, val), n(n) {};
    BIT (int n, T *a) { rebuild(n, a); }
    void rebuild(int n, T *a) {
        bit.clear();
        bit.push_back();
        this->n = n;
        for (int i = 1; i <= n; ++i) {
            bit.push_back(a[i]);
        }
        for (int i = 1; i <= n; ++i) {
            if (i + lb(i) <= n) {
                bit[i + lb(i)] += bit[i];
            }
        }
    }
    void point_update(int index, T val) {
        while (index <= n) {
            bit[index] += val;
            index += lb(index);
        }
    }

    T get(int l, int r) {
        if (l > r) return 0;
        return getSum(r) - getSum(l - 1);
    }
};
```

### 3. 线段树

#### 精简版

```cpp
// 宏
#define ls ((node) << 1)
#define rs (((node) << 1) | 1)
// 变量
const int N = 2e5 + 5;
ll seg[N << 2], lazy[N << 2], arr[N];
// 操作
ll op(ll a, ll b) { return a + b; }
// 题意不同，函数内部不同
void push_down(int l, int r, int node) {
    if (!lazy[node]) return; // 这里如果0也有意义的话多开一个数组标记
    int mid = (l + r) >> 1;
    lazy[ls] += lazy[node], lazy[rs] += lazy[node];
    seg[ls] += (mid - l + 1) * lazy[node];
    seg[rs] += (r - mid) * lazy[node];
    lazy[node] = 0;
}
// 初始化
void build(int l, int r, int node) {
    if (l == r) {
        seg[node] = arr[l];
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls), build(mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}
// 单点修改
void update(int indx, ll v, int l, int r, int node) {
    if (l == r) { // 题意不同，这里更新操作不同
        seg[node] = v; 
        return;
    }
    int mid = (l + r) >> 1;
    if (indx <= mid) update(indx, v, l, mid, ls);
    else update(indx, v, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}
// 区间修改
void update(int ql, int qr, ll v, int l, int r, int node) {
    if (ql <= l && r <= qr) { // 题意不同，这里更新操作不同
        lazy[node] += v;
        seg[node] += (r - l + 1) * v;
        return;
    }
    push_down(l, r, node);
    int mid = (l + r) >> 1;
    if (ql <= mid) update(ql, qr, v, l, mid, ls);
    if (qr > mid) update(ql, qr, v, mid + 1, r, rs);
    seg[node] = op(seg[ls], seg[rs]);
}
// 区间查找
ll get(int ql, int qr, int l, int r, int node) {
    if (ql <= l && r <= qr) return seg[node];
    push_down(l, r, node); // 保证单点的情况下这句话可以注释掉
    int mid = (l + r) >> 1;
    ll ret = 0; // 题意不同，初始化不同
    if (ql <= mid) ret = get(ql, qr, l, mid, ls);
    if (qr > mid) ret = op(get(ql, qr, mid + 1, r, rs), ret);
    return ret;
}
```

#### 结构体版

```cpp
#include <bits/stdc++.h>
using namespace std;

const int M = 1e6;
typedef long long ll;
template<class T>
struct Tree{
    #define ls(node) (node << 1)
    #define rs(node) ((node << 1) | 1)
    Tree(int len = 10): len(len) {}
    T sum[M << 2], lazy[M << 2], arr[M];
    int len;

private:
    void pushup(const int node) {// 写题目要求维护的代码，如求和，最大最小……
        sum[node] = max(sum[ls(node)], sum[rs(node)]);
    }

    void pushdown(int l, int r, const int node) {//同pushup
        int mid = (l + r) >> 1;
        lazy[ls(node)] += lazy[node], lazy[rs(node)] += lazy[node];
        sum[ls(node)] += (mid - l + 1) * lazy[node];
        sum[rs(node)] += (r - mid) * lazy[node];
        lazy[node] = 0;
    }

    void build(int l, int r, int node) {
        if (l == r) {
            sum[node] = arr[l];
            return;
        }
        int mid = (l + r) >> 1;
        build(l, mid, ls(node)), build(mid + 1, r, rs(node));
        pushup(node);
    }

    void update(int ql, int qr, T v, int l, int r, int node) {
        if (ql <= l && r <= qr) {
            sum[node] += (r - l + 1) * v;//更新操作根据题目要求更改
            lazy[node] += v;
            return;
        }
        if (lazy[node]) pushdown(l, r, node);
        int mid = (l + r) >> 1;
        if (ql <= mid) update(ql, qr, v, l, mid, ls(node));
        if (qr > mid) update(ql, qr, v, mid + 1, r, rs(node));
        pushup(node);
    }

    T getAsk(int ql, int qr, int l, int r, int node) {
        if (ql <= l && r <= qr) return sum[node];//上同
        if (lazy[node]) pushdown(l, r, node);
        T res = 0;
        int mid = (l + r) >> 1;
        if (ql <= mid) res = getAsk(ql, qr, l, mid, ls(node));
        if (qr > mid) res = max(res, getAsk(ql, qr, mid + 1, r, rs(node)));
        return res;
    }

public:
    //以下为可以，直接调用的函数
    void build() { build(1, len, 1); }
    void update(int ql, int qr, T v) { update(ql, qr, v, 1, len, 1); }
    void update(int index, T v) { update(index, index, v, 1, len, 1); }
    T getAsk(int ql, int qr) { return getAsk(ql, qr, 1, len, 1); }
    void clear() {
        memset(arr, 0, sizeof(T) * (len + 10));
        memset(sum, 0, sizeof(T) * (len << 2));
        memset(lazy, 0, sizeof(T) * (len << 2));
    }
    T& operator[] (int x) { return arr[x]; }
};

int main(){
    return 0;
}
```

#### 类版-网络赛

```cpp
#include <bits/stdc++.h>

using namespace std;


/**
 * 使用说明：
 * 本线段树板子可用于多数 网络赛
 * 需要注意的是此板子在性能方面由于三层封装，固较简洁版的线段树性能慢，但灵活性高（适合网络赛抢时间）
 * 使用时只需要修改 "可修改区的上下界" 中间的代码部分
 * lazy使用结构体封装是应付在某些题目在区间修改时有两种操作，下面给的例题有示例
 * vals是最终题目需要维护的东西（sum, max, min……），针对不同的操作只需修改结构体内的加法（+）操作即可
 * 同时若题目需要区间修改，则直接修改区间修改部分的代码即可，单点修改同理
 * 注意：区间修改针对不同的操作，lazy中的加等于（+=）也要具体根据题意修改
 * 例题1：洛谷
*/
/****************************可修改区上界******************************/
template<class T>
struct lazy{
    T lazy1;
    lazy(T lazy1 = 0){
        this->lazy1 = lazy1;
    }
    void operator+= (const lazy<T> &b) {
        this->lazy1 += b.lazy1;//常见操作
    }
    inline void del() {
        this->lazy1 = 0; // 常见操作
    }
};
template<class T>
struct vals{
    T sum;
    //初始化构造，用来方便做运算的初始化-
    vals(int x = 0) {
        sum = x;//常见初始化，方便后面做 + 的操作
    } 
    // 操作区 
    constexpr vals operator+ (const vals& b){
        vals res;
        res.sum = this->sum + b.sum;//常见操作
        return res;
    }
    //建树初始化区
    inline void init(const T &v) {
        // this->sum = v; // 常见操作，即和本身一样
    }
    // 区间更新区
    inline void update(const int &l, const int &r, const lazy<T> &v) {
        // this->sum += (r - l + 1) * v.lazy1;// 常见操作
        // printf("%d %d %d %d\n", l, r, v, (r - l + 1) * v); // 常见操作
    }
    // 单点更新区
    inline void update(const lazy<T> &v) {
        // this->sum += v; // 常见操作
    }
    //初始化，通常在多组输入题时才调用
    inline void del() {
        this->sum = 0;
    }
};
/****************************可修改区下界******************************/

/********************************以下区域建议不动（除特殊题目）*************************************/
template<class T>
class segtree{
private:
    const static int maxn = 1e5 + 10;
    const int start = 1;
    struct lazy_tabs{
        lazy<T> v;
        bool flag;
        lazy_tabs (bool flag = false) : flag(flag) {}
        // 懒标记更新
        void update(const lazy<T> &v) { this->v += v, flag = true; }
        // 懒标记删除
        void del() { this->v.del(), flag = false; }
    }lazy_tab[maxn << 2];
    vals<T> val[maxn << 2];
    //计算左右孩子节点下标
    inline static constexpr int ls(const int &indx) { return indx << 1;}
    inline static constexpr int rs(const int &indx) { return indx << 1 | 1;}
    // 上放
    inline void push_up(const int &pos) { val[pos] = val[ls(pos)] + val[rs(pos)]; }
    // 下放
    void push_down(const int &l, const int &r, const int &pos) {
        if (!lazy_tab[pos].flag) return; // 若没有标记则返回
        const auto &v = lazy_tab[pos].v;
        lazy_tab[ls(pos)].update(v), lazy_tab[rs(pos)].update(v);
        const int mid = (l + r) >> 1;
        val[ls(pos)].update(l, mid, v), val[rs(pos)].update(mid + 1, r, v);  // 更新儿子节点
        lazy_tab[pos].del();
    }
    // 建树
    void build(const int &l, const int &r, const int &pos, const T *a) {
        if (l == r) {
            val[pos].init(a[l]);
            return;
        }
        const int mid = (l + r) >> 1;
        build(l, mid, ls(pos), a), build(mid + 1, r, rs(pos), a);
        push_up(pos);
    }
    // 单点修改
    void update(const int &indx, const int &l, const int &r, 
                        const lazy<T> &v, const int &pos) {
        if (l == r) {
            val[pos].update(v);
            return;
        }
        const int mid = (l + r) >> 1;
        if (indx <= mid) update(indx, l, mid, ls(pos));
        else update(indx, mid + 1, r, rs(pos));
        push_up(pos);
    }
    //区间修改
    void update(const int &ql, const int &qr, const lazy<T> &v,
                        const int &l, const int &r, const int &pos) {
        if (ql <= l && r <= qr) { // 如果在查询区间内
            lazy_tab[pos].update(v);    //懒更新
            val[pos].update(l, r, v);   // 区间值更新
            return;
        }
        push_down(l, r, pos);
        const int mid = (l + r) >> 1;
        if (ql <= mid) update(ql, qr, v, l, mid, ls(pos));
        if (qr > mid) update(ql, qr, v, mid + 1, r, rs(pos));
        push_up(pos);
    }
    //区间查询
    vals<T> get(const int &ql, const int &qr, const int &l, 
                        const int &r, const int &pos) {
        if (ql <= l && r <= qr) return val[pos];
        push_down(l, r, pos);
        vals<T> res;
        const int mid = (l + r) >> 1;
        if (ql <= mid) res = res + get(ql, qr, l, mid, ls(pos));
        if (qr > mid) res = res + get(ql, qr, mid + 1, r, rs(pos));
        return res;
    }
public:
    /***********************一下均可调用****************************************/
    int n;
    segtree (int n = -1) : n(n) {}
    // 建树
    void build(T *a) { build(start, n, start, a); }
    // 查询
    vals<T> get(int ql, int qr) { return get(ql, qr, start, n, start); }
    //单点修改
    void update(int indx, lazy<T> val) { update(indx, start, n, val, start); }
    //区间修改
    void update(int ql, int qr, lazy<T> val) { update(ql, qr, val, start, n, start); }
    //清空-
    // 切记，先用再清空，因为通常是多组输入时太调用
    void clear() {
        assert(n != -1);
        int len = n << 2;
        assert(n < maxn);
        for (int i = 1; i <= len; ++i) {
            lazy_tab[i].del();
            val[i].del();
        }
    }
};

int main() {
#ifndef ONLINE_JUDGE
    freopen("D:/MYCODE/vsCode-c/test.in", "r", stdin);
    freopen("D:/MYCODE/vsCode-c/test.out", "w", stdout);
#endif
    return 0;
}
```

### 4. ST表

```cpp
const int M = 1e5 + 5;
int st[M][30], lg[M];
// st表预处理, 注意下标从1开始到n结束
void init(int *a, int n) {
	lg[0] = -1;
    for (int i = 1; i <= n; ++i) lg[i] = lg[i >> 1] + 1, st[i][0] = a[i];
    for (int j = 1; j <= lg[n]; ++j) {
        int k = 1 << (j - 1);
        for (int i = 1; i + k - 1 <= n; ++i) {
            st[i][j] = max(st[i][j - 1], st[i + k][j - 1]);
        }
    }
}
// 询问
// 尽可能让l + 2^(len) - 1接近r
int get(int l, int r) {
    int x = lg[r - l + 1];
    return max(st[l][x], st[r - (1 << x) + 1][x]);
}
```

### 5. 分块

```cpp
const int N = 1e5 + 5, M = 500;
#define ll long long
ll a[N];
int belong[N];
struct blocks {
    int l, r;
    ll lazy;
    blocks() : lazy(0){}
}b[M];
// 以下函数是基本不变的
void build(int n) {
    int siz = sqrt(n), cnt = n / siz;
    if (n % siz) ++cnt;
    for (int i = 1; i <= cnt; ++i) {
        b[i].l = (i - 1) * siz + 1;
        b[i].r = i * siz;
    }
    b[cnt].r = n;
    for (int i = 1; i <= n; ++i) belong[i] = (i - 1) / siz + 1;
}

```

### 6. 莫队

```cpp
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>

using namespace std;

const int M = 1e5 + 10;
int n, m, block, arr[M], pos[M], ans[M], res;
struct MO{
    int l, r, k;
    MO(int l = 0, int r = 0, int k = 0) : l(l), r(r), k(k) {}
}q[M];

bool cmp(MO a, MO b) {
    if (pos[a.l] ^ pos[b.l]) {//不在同一个块
        return  pos[a.l] < pos[b.l];
    }
    if (pos[a.l] & 1) return a.r < b.r;
    return b.r < a.r;
}
void add(int x) {

}
void del(int x) {

}

void solve() {
    int l = 1, r = 0;
    for (int i = 0; i < m; i++) {
        while (l > q[i].l) add(--l);
        while (l < q[i].l) del(l++);
        while (r < q[i].r) add(++r);
        while (r > q[i].r) del(r--);
        ans[q[i].k] = res;//res根据题目意思来
    }
}
void init() {
    scanf("%d %d", &n, &m);
    block = sqrt(n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", arr + i);
        pos[i] = i / block;
    }
    for (int i = 0; i < m; i++) {
        int l, r;
        scanf("%d %d", &l, &r);
        q[i] = MO(l, r, i);
    }
    sort(q, q + m, cmp);
}

int main() {
    init();
    solve();
    return 0;
}
```

### 7. 平衡树

#### fhq treap

```cpp
// 洛谷板子题
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <random>
#include <cctype>
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}
using namespace std;
const int N = 4e5 + 10;
mt19937 rnd(233);
struct treap{
    int val, l, r, size, key;
}fhq[N];
int root, cnt;
inline void update(int now) {
    fhq[now].size = fhq[fhq[now].l].size + fhq[fhq[now].r].size + 1;
}

int new_node(int val) {
    fhq[++cnt] = {.val = val, .l = 0, .r = 0, .size = 1, .key = rnd()};
    return cnt;
}
void split(int now, int val, int &x, int &y) {
    if (!now) { x = y = 0; return; }
    if (fhq[now].val <= val) x = now, split(fhq[now].r, val, fhq[now].r, y);
    else y = now, split(fhq[now].l, val, x, fhq[now].l);
    update(now);
}

int merge(int x, int y) {
    if (!x || !y) return x | y;
    // 大根堆
    if (fhq[x].key > fhq[y].key) { //右下角
        fhq[x].r = merge(fhq[x].r, y), update(x);
        return x;
    }
    // 左下角
    fhq[y].l = merge(x, fhq[y].l), update(y);
    return y;
}
// 插入
inline void insert(int val) {
    int x, y;
    split(root, val, x, y);
    root = merge(merge(x, new_node(val)), y);
}
// 按值删除
inline void del(int val) {
    int x, y, z;
    split(root, val, x, z);
    split(x, val - 1, x, y);
    y = merge(fhq[y].l, fhq[y].r);
    root = merge(merge(x, y), z);
}
// 按值获取排名
inline int getrank(int val) {
    int x, y, ans;
    split(root, val - 1, x, y);
    ans = fhq[x].size + 1;
    root = merge(x, y);
    return ans;
}
// 按排名获取值
inline int getval(int rank) {
    int now = root;
    while (now) {
        if (fhq[fhq[now].l].size + 1 == rank) break;
        else if (fhq[fhq[now].l].size >= rank) now = fhq[now].l;
        else rank -= fhq[fhq[now].l].size + 1, now = fhq[now].r;
    }
    return fhq[now].val;
}
// 求前驱，即严格比val小的最大值
inline int pre(int val) {
    int x, y;
    split(root, val - 1, x, y);
    int now = x;
    while (fhq[now].r) now = fhq[now].r;
    root = merge(x, y);
    return fhq[now].val;
}
// 求后继，即严格比val大的最小值
inline int nxt(int val) {
    int x, y;
    split(root, val, x, y);
    int now = y;
    while (fhq[now].l) now = fhq[now].l;
    root = merge(x, y);
    return fhq[now].val;
}

int main() {
    int t = IO();
    while (t--) {
        int q = IO(), val = IO();
        if (q == 1) insert(val);
        else if (q == 2) del(val);
        else if (q == 3) printf("%d\n", getrank(val));
        else if (q == 4) printf("%d\n", getval(val));
        else if (q == 5) printf("%d\n", pre(val));
        else printf("%d\n", nxt(val));
    }
    return 0;
}
```

#### spaly

#### 替罪羊

### 8. 左偏树

### 9. 主席树

1. 主席树（静态）洛谷模板题

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cctype>
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}
using namespace std;

/*************************************离散化********************************************/
// vt存放可用于查询原本的数（用离散化值），打表后用于查询离散化表（用下标）
vector<int> vt;
inline int get_id(const int &x) { return lower_bound(vt.begin(), vt.end(), x) - vt.begin() + 1; }
inline void erase_vt() {
    sort(vt.begin(), vt.end());
    vt.erase(unique(vt.begin(), vt.end()), vt.end());
}
// 打表, 注意，原数组下标要从1开始，返回离散化后的表大小
inline int id_table(int n, int *a, vector<int> &res) { 
    res.emplace_back(0);
    for (int i = 1; i <= n; ++i) res.emplace_back(get_id(a[i]));
    return vt.size();
}

/*************************************主席树********************************************/
const int N = 2e5 + 5;
struct nodes{
    int l, r, sum;
    nodes() : sum(0) {}
}hjt[N << 5];
int root[N], cnt; // 记录每个根结点的内存池编号， 内存池
int build(int l, int r) {
    int now = ++cnt; // 内存申请
    if (l < r) {
        int mid = (l + r) >> 1;
        hjt[now].l = build(l, mid);
        hjt[now].r = build(mid + 1, r);
    }
    return now;
}
// 插入新节点的操作
int update(int pre, int l, int r, int x) {
    int now = ++cnt; // 内存申请
    hjt[now] = hjt[pre], ++hjt[now].sum; // 继承
    if (l < r) { // 寻找拼接点
        int mid = (l + r) >> 1;
        if (x <= mid) hjt[now].l = update(hjt[now].l, l, mid, x); // 如果x在左边，则让当前新节点的左孩子接继承后的左孩子
        else hjt[now].r = update(hjt[now].r, mid + 1, r, x); // 否则同理
    }
    return now;
}
// 返回第qr版本的主席树 - 第ql版本的主席树， 注意返回的是离散化后的值
int get(int ql, int qr, int l, int r, int k) {
    if (l == r) return l;
    int mid = (l + r) >> 1;
    int dif = hjt[hjt[qr].l].sum - hjt[hjt[ql].l].sum;
    if (k <= dif) return get(hjt[ql].l, hjt[qr].l, l, mid, k); // 左孩子上
    return get(hjt[ql].r, hjt[qr].r, mid + 1, r, k - dif); // 右孩子上
}

/*************************************主函数********************************************/
int a[N];
int main() {
    int n = IO(), m = IO();
    for (int i = 1; i <= n; ++i) a[i] = IO(), vt.emplace_back(a[i]);
    erase_vt();
    vector<int> id;
    int siz = id_table(n, a, id);
    root[0] = build(1, siz);
    for (int i = 1; i <= n; ++i) root[i] = update(root[i - 1], 1, siz, id[i]);
    while (m--) {
        int l = IO(), r = IO(), k = IO();
        printf("%d\n", vt[get(root[l - 1], root[r], 1, siz, k) - 1]);
    }
    return 0;
}
```

### 10. LCA

```cpp
// 洛谷板子题
// 注意，尽量让结点编号从1开始
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <cctype>
#define ll long long
inline long long IO() {
    long long x = 0;
    bool f = false;
    char c = getchar();
    while (!isdigit(c)) {
        if (c == '-') f = true;
        c = getchar();
    }
    while (isdigit(c)) {
        x = (x << 1) + (x << 3) + (c - '0');
        c = getchar();
    }
    return f ? -x : x;
}
using namespace std;
const int maxn = 5e5 + 5, maxm = 5e5 + 5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt;

struct edges {
    int to, next;
    void add(int t, int n) {
        to = t, next = n;
    }
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v) {
    edge[++cnt].add(v, head[u]);
    head[u] = cnt;
}

int fa[maxn][35], dep[maxn], lg[maxn];
/* 另一种写法
void dfs(int u, int f) {
    deep[u] = deep[f] + 1;
    fa[u][0] = f;
    for (int i = 1; (1 << i) <= deep[u]; ++i) fa[u][i] = fa[fa[u][i - 1]][i - 1];
    for (int& v : mp[u]) {
        if (v ^ f) dfs(v, u);
    }
}

int lca(int a, int b) {
    if (deep[a] < deep[b]) swap(a, b);
    for (int i = 18; ~i; --i) if (deep[fa[a][i]] >= deep[b]) a = fa[a][i];
    if (a == b) return a;
    for (int i = 20; ~i; --i) {
        if (fa[a][i] != fa[b][i]) a = fa[a][i], b = fa[b][i];
    }
    return fa[a][0];
}
*/
void dfs(int u, int f) {
    fa[u][0] = f;
    dep[u] = dep[f] + 1;
    for (int i = 1; i <= lg[dep[u]]; ++i) fa[u][i] = fa[fa[u][i - 1]][i - 1];
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v ^ f) dfs(v, u);
    }
}
void init(int root, int n) { // 通过检查代码，反向51行dep[root] = -1没意义，具体细节以后填这个坑
    dep[root] = lg[0] = -1;
    memset(head, -1, sizeof head); cnt = 0;
    for (int i = 1; i <= n; ++i) lg[i] = lg[i >> 1] + 1;
}
int lca(int a, int b) {
    if (dep[a] < dep[b]) swap(a, b);
    while (dep[a] > dep[b]) a = fa[a][lg[dep[a] - dep[b]]];
    if (a == b) return a;
    for (int i = lg[dep[a]]; ~i; --i) {
        if (fa[a][i] != fa[b][i]) a = fa[a][i], b = fa[b][i];
    }
    return fa[a][0];
}
int main() {
    
    int n = IO(), m = IO(), root = IO();
    init(root, n);
    for (int i = 1; i < n; ++i) {
        int u = IO(), v = IO();
        add(u, v), add(v, u);
    }
    dfs(root, 0);
    while (m--) {
        int a = IO(), b = IO();
        printf("%d\n", lca(a, b));
    }
    return 0;
}
```

### 11. 树链剖分

```cpp
// 洛谷板子题
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <iostream>
#include <cmath>
#include <bitset>
using namespace std;
#define ll long long

const int N = 1e5 + 5, M = 2e5 + 5;
const int maxn = 1e5 + 5, maxm = 2e5 + 5;
const int INF = 0x3f3f3f3f;

int head[maxn], cnt;

//初始化
void init() { memset(head, -1, sizeof head); cnt = -1; }

struct edges {
    int to, next;
    void add(int t, int n) {
        to = t, next = n;
    }
}edge[maxm << 1]; //无向图则需要乘2

inline void add(int u, int v) {
    edge[++cnt].add(v, head[u]);
    head[u] = cnt;
}

/*******************************树链剖分**********************************/
int fa[N], dep[N], siz[N], son[N];
void dfs1(int u, int f) {
    fa[u] = f, siz[u] = 1;
    dep[u] = dep[f] + 1;
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == f) continue;
        dfs1(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[son[u]]) son[u] = v; // 找重儿子
    }
}
int v[N]; // 点上的权值
int tim, dfn[N], top[N], w[N]; // w的下标是时间戳，对应的是相应时间戳上的点的点权
void dfs2(int u, int t) {
    dfn[u] = ++tim, top[u] = t;
    w[tim] = v[u];
    if (!son[u]) return;
    dfs2(son[u], t);
    for (int i = head[u]; ~i; i = edge[i].next) {
        int v = edge[i].to;
        if (v == fa[u] || v == son[u]) continue;
        dfs2(v, v);
    }
}
/*******************************线段树******************************/
inline int ls(const int& x) { return x << 1;}
inline int rs(const int& x) { return x << 1 | 1;}
ll seg[N << 2], lazy[N << 2], p;
int n, m;
inline ll op(const ll& a, const ll& b) {
    // seg[x] = max(seg[ls(x)], seg[rs(x)]);
    return (a + b) % p;
}
inline void push_down(const int& l, const int& r, const int& node) {
    if (!lazy[node]) return;
    lazy[ls(node)] += lazy[node], lazy[rs(node)] += lazy[node];
    lazy[ls(node)] %= p, lazy[rs(node)] %= p;
    int mid = (l + r) >> 1;
    seg[ls(node)] = (lazy[node] * (mid - l + 1) + seg[ls(node)]) % p;
    seg[rs(node)] = (lazy[node] * (r - mid) + seg[rs(node)]) % p;
    lazy[node] = 0;
}

void build(int l, int r, int node = 1) {
    if (l == r) {
        seg[node] = w[l];
        return;
    }
    int mid = (l + r) >> 1;
    build(l, mid, ls(node)), build(mid + 1, r, rs(node));
    seg[node] = op(seg[ls(node)], seg[rs(node)]);
}

void update(int ql, int qr, ll x, int l = 1, int r = n, int node = 1) {
    if (ql <= l && r <= qr) {
        lazy[node] = (lazy[node] + x) % p;
        seg[node] = (seg[node] + (r - l + 1) * x) % p;
        return;
    }
    push_down(l, r, node);
    int mid = (l + r) >> 1;
    if (ql <= mid) update(ql, qr, x, l, mid, ls(node));
    if (qr > mid) update(ql, qr, x, mid + 1, r, rs(node));
    seg[node] = op(seg[ls(node)], seg[rs(node)]);
}

int get(int ql, int qr, int l = 1, int r = n, int node = 1) {
    if (ql <= l && r <= qr) return seg[node];
    push_down(l, r, node);
    int mid = (l + r) >> 1, res = 0;
    if (ql <= mid) res = get(ql, qr, l, mid, ls(node));
    if (qr > mid) res = op(res, get(ql, qr, mid + 1, r, rs(node)));
    return res;
}
/********************************树上操作**********************************/
void update_chain(int x, int y, ll z) {// 将树从 x 到 y 结点最短路径上所有节点的值都加上 z。
    while (top[x] != top[y]) {
        if (dep[top[x]] < dep[top[y]]) swap(x, y);
        update(dfn[top[x]], dfn[x], z);
        x = fa[top[x]];
    }
    if (dep[x] > dep[y]) swap(x, y);
    update(dfn[x], dfn[y], z);
}

ll get_chain(int x, int y) {//求树从 x 到 y 结点最短路径上所有节点的值之和。
    int res = 0;
    while (top[x] != top[y]) {
        if (dep[top[x]] < dep[top[y]]) swap(x, y);
        res = op(res, get(dfn[top[x]], dfn[x]));
        x = fa[top[x]];
    }
    if (dep[x] > dep[y]) swap(x, y);
    return op(res, get(dfn[x], dfn[y]));
}

void update_son(int x, ll z) {// 将以 x 为根节点的子树内所有节点值都加上 z
    update(dfn[x], dfn[x] + siz[x] - 1, z);
}
ll get_son(int x) {// 求以 x 为根节点的子树内所有节点值之和
    return get(dfn[x], dfn[x] + siz[x] - 1);
}
/********************************主函数************************************/

int main() {
    std::ios::sync_with_stdio(false);
    cout.tie(0), cin.tie(0); 
    init();
    int root;
    cin >> n >> m >> root >> p;
    for (int i = 1; i <= n; ++i) cin >> v[i];
    for (int i = 1; i < n; ++i) {
        int u, v;
        cin >> u >> v;
        add(u, v), add(v, u);
    }
    dfs1(root, root), dfs2(root, root);
    build(1, n);
    while (m--) {
        int q, x, y, z;
        cin >> q >> x;
        if (q == 1) {
            cin >> y >> z;
            update_chain(x, y, z % p);
        } else if (q == 2) {
            cin >> y;
            cout << get_chain(x, y) << endl;
        } else if (q == 3) {
            cin >> z;
            update_son(x, z);
        } else {
            cout << get_son(x) << endl;
        }
    }
    return 0;
}
```

