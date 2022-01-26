# FHQ树


# FHQ Treap

## 简介

- fhq treap主要有两个操作：分裂、合并
- 作为平衡树，他所有的平衡树操作都是基于这两个操作实现的

## 关于结点的存储与生成新的结点

- 由于一个结点要维护挺多信息的，所以用结构体来存储一个结点会比较好
- 如代码，存放的有：结点的权值、左孩子、右孩子、子树的大小、关键值（用随机函数赋值）,其中左右孩子值为0代表是叶子，即无内存池编号

```cpp
const int N = 4e5 + 10;
struct treap{ 
    int val, l, r, size, key;
}fhq[N];
```



- 而生成一个新的结点和主席树的动态开点是有点像的，看代码就能懂了

```cpp
int root, cnt; // 因为只有一棵树，所以只要一个root记录树的根节点就行了，cnt为内存池
mt19937 rnd(233); // 随机函数，头文件 random
int new_node(int val) {
    fhq[++cnt] = {.val = val, .l = 0, .r = 0, .size = 1, .key = rnd()};
    return cnt;
}
```

- 注意，由于我们多维护了一个树的大小，固我们还要写一个更新结点的函数

```cpp
inline void update(int now) { // 左孩子的大小 + 右孩子的大小 + 1
    fhq[now].size = fhq[fhq[now].l].size + fhq[fhq[now].r].size + 1;
}
```



## 分裂

- 分裂操作有两种
- 第一种是按照权值分裂，一边分裂成小于等于某个值的，另一边分裂成大于某个值的
- 具体细节看代码

```cpp
void split(int now, int val, int &x, int &y) { // 分裂成x y两棵treap树
    if (!now) { x = y = 0; return; } // 如果当前结点为空，则不作分裂
    // 如果当前结点的值小于等于分裂要求的值，则让x树接上当前结点，并递归下去找当前结点的右孩子和y树应该拼接哪个结点
    if (fhq[now].val <= val) x = now, split(fhq[now].r, val, fhq[now].r, y); 
    else y = now, split(fhq[now].l, val, x, fhq[now].l); // 否则同理
    update(now); // 因为分裂了，记得更新
}
```

- 第二种就是按照数的大小分裂
- 待补……

## 合并

- 合并的话也不难
- 显然刚刚分裂时分裂出来的x和y，x树上的所有权值都小于y树上的权值
- 具体细节看代码（以大根堆为例）

```cpp
int merge(int x, int y) {
    if (!x || !y) return x | y;
    // 大根堆
    if (fhq[x].key > fhq[y].key) { //如果x的key值大于y的key值，则根据堆和平衡树的性质
        fhq[x].r = merge(fhq[x].r, y), update(x); // 让x的右子树为（x的右子树和y合并的结果的根）， 记得更新结点
        return x; // 返回合并后的根结点的内存池的编号
    }
    // 否则同理
    fhq[y].l = merge(x, fhq[y].l), update(y); 
    return y; // 返回合并后的根
}
```

## 平衡树的基本操作

### 1、插入

- 首先先将整棵平衡树分裂成$\le val$的x子Treap树和$>val$的y子treap树
- 然后再新建一个权值为$val$的结点
- 让x和新结点合并，再和y合并

```cpp
inline void insert(int val) {
    int x, y;
    split(root, val, x, y);
    root = merge(merge(x, new_node(val)), y);
}
```

### 2、按值删除

- 将整棵树分裂成x、y、z，分别为$<val$、$=val$、$>val$
- 然后将y的根结点删除
- 最后再合并回去

```cpp
inline void del(int val) {
    int x, y, z;
    split(root, val, x, z); // 分裂1
    split(x, val - 1, x, y); //分裂2
    y = merge(fhq[y].l, fhq[y].r); // 删除y的根结点
    root = merge(merge(x, y), z); // 最后合并回去
}
```

### 3、按值获取排名

- 将整棵树分裂成x（$< val$）和y（$\ge val$）
- $val$的排名就是x的数的大小+1

```cpp
inline int getrank(int val) {
    int x, y, ans; 
    split(root, val - 1, x, y); // 分裂
    ans = fhq[x].size + 1; // 记录答案
    root = merge(x, y); // 合并回去
    return ans;
}
```

### 4、按排名获取值

- 获取一个排名其实就是找一棵子树的左子树大小 + 1为rank
- 从根结点一直往下找就可以了

```cpp
inline int getval(int rank) {
    int now = root;
    while (now) {
        if (fhq[fhq[now].l].size + 1 == rank) break; // 找到了
        else if (fhq[fhq[now].l].size >= rank) now = fhq[now].l; // 在左子树上
        else rank -= fhq[fhq[now].l].size + 1, now = fhq[now].r; // 寻找右子树记得改动排名
    }
    return fhq[now].val;//最后的答案
}
```

### 5、求一个数的前驱或后继

- 找$val$的前驱只需要将整棵树分裂成x（$< val$）和y（$\ge val$）
- 然后x树中的最大值就是$val$的前驱，一直往右子树找就可以了
- 后继的找法同理

```cpp
// 求前驱
inline int pre(int val) {
    int x, y;
    split(root, val - 1, x, y);
    int now = x;
    while (fhq[now].r) now = fhq[now].r;
    root = merge(x, y);
    return fhq[now].val;
}
// 求后继
inline int nxt(int val) {
    int x, y;
    split(root, val, x, y);
    int now = y;
    while (fhq[now].l) now = fhq[now].l;
    root = merge(x, y);
    return fhq[now].val;
}
```

## 完整代码

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
// 求前驱
inline int pre(int val) {
    int x, y;
    split(root, val - 1, x, y);
    int now = x;
    while (fhq[now].r) now = fhq[now].r;
    root = merge(x, y);
    return fhq[now].val;
}
// 求后继
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


