# 字符串板子


### 1. KMP

```cpp
const int M = 1e6 + 5;
//普通版本
void getnext(char *x, int len, int *nxt) {
    int i = 0, j;
    j = nxt[0] = -1;
    while (i < len) {
        while(j != -1 && x[i] != x[j]) j = nxt[j];
        nxt[++i] = ++j;
    }
}
//略微优化版本
void getNext(char *x, int len, int *nxt) {
    int i = 0, j;
    j = nxt[0] = -1;
    while (i < len) {
        while (j != -1 && x[i] != x[j]) j = nxt[j];
        if (x[++i] == x[++j]) nxt[i] = nxt[j];
        else nxt[i] = j;
    }
}
// y是主串
int nxt[M];
int kmpCount(char *y, int n, char *x, int m) {
    int i = 0, j = 0, ans = 0;
    getnext(x, m, nxt);
    while (i < n) {
        while (j != -1 && y[i] != x[j]) j = nxt[j];
        ++i, ++j;
        if (j >= m) ++ans, j = nxt[j];
    }
    return ans;
}
```

### 2. 字符串Hash

```cpp
unsigned int DJBHash(const char *str) {
    unsigned int hash = 5381;
    while (*str) hash += (hash << 5) + (*str++);
    return (hash & 0x7fffffff); //7个f
}

unsigned int BKDRHash(const char *str) {
    unsigned int seed = 131; // 31 131 1313 13131 131313...
    unsigned int hash = 0;
    while (*str) hash = hash * seed + (*str++);
    return (hash & 0x7fffffff);
}
#define ull unsigned long long
ull strhash(const char *s) {
    ull seed = 1313, res = 0; // 31 131 1313 13131
    while (*s) res = res * seed + (*s++);
    return res;
}

```

### 3. 马拉车

```cpp
string s,t;
int p[maxn*2+5];

string init(string s){
    t="";
    t.push_back('$');
    t.push_back('#');
    for(int i=0;i<s.size();i++){
        t.push_back(s[i]);
        t.push_back('$');
    }
    t.push_back('^');
    return t;
}

int manacher(string s){
    init(s);
    int id=0,mx=0;
    for(int i=1;i<t.size()-1;i++){
        if(mx>i)p[i]=min(p[id*2-i],mx-i);
        else p[i]=1;
        for(;t[i+p[i]]==t[i-p[i]];p[i]++);
        if(p[i]+i>mx){
            mx=p[i]+i;
            id=i;
        }
    }
    int res=p[0]-1;
    for(int i=0;i<t.size();i++)res=max(res,p[i]-1);
    return res;
}
```

### 4. exkmp

```cpp
void pre_exkmp(char x[], int m, int next[]) {
    next[0] = m;
    int j = 0;
    while (j + 1 < m && x[j] == x[j + 1]) j++;
    next[1] = j;
    int k = 1;
    for (int i = 2; i < m; i++) {
        int p = next[k] + k - 1;
        int L = next[i - k];
        if (i + L < p + 1) next[i] = L;
        else {
            j = max(0, p - i + 1);
            while (i + j < m && x[i + j] == x[j]) j++;
            next[i] = j;
            k = i;
        }
    }
}
void exkmp(char x[], int m, char y[], int n, int next[], int extend[]) {
    pre_exkmp(x, m, next);
    int j = 0;
    while (j < n && j < m && x[j] == y[j]) j++;
    extend[0] = j;
    int k = 0;
    for (int i = 1; i < n; i++) {
        int p = extend[k] + k - 1;
        int L = next[i - k];
        if (i + L < p + 1) extend[i] = L;
        else {
            j = max(0, p - i + 1);
            while (i + j < n && j < m && y[i + j] == x[j]) j++;
            extend[i] = j;
            k = i;
        }
    }
}
```

