# 主席树


# 主席树

## 简介

- 主席树是用来求区间第k大的一个可持续化数据结构
- 主要思想就是权值线段树+前缀和思想

## 几个问题

### 1. 用权值线段树+前缀和的原因

- 首先、如果我们要求一个固定区间的第k大，其中一个可以使用数据结构就是权值线段树
- 由于题目要我们求的是不定区间的区间第k大，我们可以用前缀和的思想建立和区间同长个数的权值线段树，这样当我们要求$[l, r]$的第k大的时候，我们就可以用第$r$个权值线段树 **减去** 第$l - 1$个权值线段树得到的权值线段树来求第k大
- 为了节省空间，我们可以使第$i$个和第$i - 1$个 权值线段树公用一些结点

### 2. 内存池

- 这个有点像链式前向星的思路，每次要添加新的边就`++cnt`，并让对应的边的下标为`cnt`
- 同理，我们可以在每次新加一个结点时，就`++cnt`，并让对应的结点的下标为`cnt`
- 可以把这里的`cnt`想象成我们在为新的指针申请地址时的地址号

### 3. 不同版本的权值线段树的存储

- 显然，我们要找一颗树，只需要找他的根结点就可以了
- 固我们可以记录每个版本（不同时间线）的权值线段树的根结点的内存池下标

### 4. 离散化

- 因为我们要建立的是权值线段树，固需要对数据进行离散化，本文的离散化做了预处理打表

## 结构体存放树的结点

- 利用结构体存放结点的好处就是访问方便，代码好写

 ```cpp
const int N = 2e5 + 5;
struct nodes{
    int l, r, sum; // 左孩子的内存池编号、 右孩子内存池编号、 线段树的结点权值
    nodes() : sum(0) {} 
}hjt[N << 5]; // 开多点，一边都是32倍管够
int root[N], cnt; // 记录每个根结点的内存池编号， 内存池
 ```



## 建树

- 看AgOH的视频里面并没有加这个建树的操作，但是我觉得还是挺有必要加的，因为后面的一些可持续化数据结构能够用得上
- 初始建树其实可以想象成我们前缀和的第0项

 ```cpp
int build(int l, int r) {
    int now = ++cnt; // 内存申请
    if (l < r) {
        int mid = (l + r) >> 1;
        hjt[now].l = build(l, mid);     // 
        hjt[now].r = build(mid + 1, r); // 
    }
    return now; // 返回当前节点的内存池编号
}
 ```

## 插入新的权值

- 插入新的权值即新建一颗权值线段树
- 但注意，由于要复用之前的一些结点，固有些东西的写法和普通的权值线段树是不一样的，具体看代码

 ```cpp
// 插入新节点的操作
int update(int pre, int l, int r, int x) { // 插入一个新的权值为x
    int now = ++cnt; // 内存申请
    hjt[now] = hjt[pre], ++hjt[now].sum; // 继承上一个版本的结点，即复用，因为新加了一个值，所以结点权值+1
    if (l < r) { // 寻找拼接点
        int mid = (l + r) >> 1;
        if (x <= mid) hjt[now].l = update(hjt[now].l, l, mid, x); // 如果x在左边，则让当前新节点的左孩子接继承后的左孩子
        else hjt[now].r = update(hjt[now].r, mid + 1, r, x); // 否则同理
    }
    return now;
}
 ```



![主席树插入](https://cdn.jsdelivr.net/gh/haofish/ImgHosting/haofishPIC主席树.png)

- 代码的第4行的第一语句就像是图中一个8号新节点用两个红色的边与连向1号结点的两个孩子
- 代码的第8行就像是图中将8号结点的右孩子边**"剪掉"**再用一个新的绿色边和下一个新结点相连

## 询问

- 查询是最后的操作了，具体做法主要的思想就是前缀和的思想
- 计算左孩子的$l, r$版本权值和的差，然后再对比
- 每次查询可以想象成只有一颗线段树上查找第k大

 ```cpp
// 返回第qr版本的主席树 - 第ql版本的主席树， 注意返回的是离散化后的值
int get(int ql, int qr, int l, int r, int k) {
    if (l == r) return l; // 返回权值点上的权值
    int mid = (l + r) >> 1;
    int dif = hjt[hjt[qr].l].sum - hjt[hjt[ql].l].sum; // 计算左孩子的版本权值和的差
    if (k <= dif) return get(hjt[ql].l, hjt[qr].l, l, mid, k); // 答案在左孩子上
    return get(hjt[ql].r, hjt[qr].r, mid + 1, r, k - dif); // 答案在右孩子上，记得这时是找第k - dif的
}
 ```

## 完整代码

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


