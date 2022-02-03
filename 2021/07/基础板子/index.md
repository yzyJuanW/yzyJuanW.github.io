# 基础板子


### 1. 二分

#### 整数二分

```cpp
// 1
while (l < r) {
	int mid = (l + r) >> 1;
	if (check(mid)) l = mid + 1;
	else r = mid;
}
// 2
while (l < r) {
	int mid = (l + r + 1) >> 1;
	if (check(mid)) l = mid;
	else r = mid - 1;
}
// 常用
while (l <= r) {
	int mid = (l + r) >> 1;
	if (check(mid)) ans = mid, l = mid + 1;
	else r = mid - 1;
}
```

#### 浮点数二分

```cpp
// 通用版
while (r - l > 1e-5) {
	double mid = (l + r) / 2;
	if (check(mid)) l = mid;
	else r = mid;
}
// 防卡精度
for (int i = 0; i < 100; ++i) {
	double mid = (l + r) / 2;
	if (check(mid)) l = mid;
	else r = mid;
}
```

### 2. 离散化

```cpp
template<class T>
struct discrete {
    vector<T> v;
    int init() { // 离散化，返回离散化后的个数
        sort(v.begin(), v.end());
        v.erase(unique(v.begin(), v.end()), v.end());
        return v.size();
    }
    int size() { return v.size(); }
    void add(const T x) { v.push_back(x); }
    int operator[] (const T x) {// 查找一个值离散化后的值
        return lower_bound(v.begin(), v.end(), x) - v.begin() + 1;
    }
    T kth(int k) { return v[k - 1]; } // 查找排第k的是多少，查找范围[1, n]
};
```

### 3. 高精

#### 压位版

```cpp {.line-numbers}
#include <vector>
#include <iomanip>
#include <algorithm>
#include <string>
#include <iostream>

using namespace std;

typedef long long ll;
/*
用前必读：
    本高精采用可调压位式运算对于不同需求记得更改下面的压位代码
    本代码暂时只支持
        高精 + 高精
        高精1 - 高精2 （高精1 > 高精2）
        高精 * 低精
        高精 / 低精
        高精 * 高精
        高精 % 低精
        高精 与 高精 的大小对比
        高精 += 高精
        高精1 -= 高精2 （高精1 > 高精2）
        高精 *= 低精
        高精 /= 低精
        高精 *= 高精
        高精 %= 低精
    尚未完成的功能
        高精 / 高精
        高精 % 高精
        高精1 - 高精2 （高精1 < 高精2）
        ……
    其余请读者自己体会，也许还有bug，但对于所有运算均在洛谷模板题上验证过
*/

class hint{
public:
    static const int w = 1e8, wsize = 8;//压位8个0
    vector<ll> a;
    // int len;
       /* 初始化 */
    hint(ll);
    hint(string);
    ll& operator[] (int x) { return a[x]; }
    ll operator[] (int x) const { return a[x]; }
    int len() const { return a.size(); }
    /* 比较(未验证) */
    bool operator< (const hint &b) const;
    bool operator> (const hint &b) const { return b < *this; }
    bool operator<= (const hint &b) const { return !(b < *this); }
    bool operator>= (const hint &b) const { return !(*this < b); }
    bool operator!= (const hint &b) const { return b < *this || *this < b; }
    bool operator== (const hint &b) const { return !(b < *this) && !(*this < b); }
    /* 各种运算 */
    // hint operator+ (hint&); // 高精 + 高精
    friend hint operator+ (const hint&, const hint&);
    friend hint operator- (const hint&, const hint&);
    friend hint operator* (const hint&, const hint&);
    hint operator* (const int&); // 高精 * 低精
    hint operator/ (const int&); // 高精 / 低精
    hint operator% (const int&);       // 高精 % 低精
    hint& operator+= (const hint&);     // 高精 += 高精
    hint& operator*= (const int&);      // 高精 *= 低精
    hint& operator*= (const hint&);     // 高精 *= 高精
    hint& operator-= (const hint&);     // 高精 -= 高精
    hint& operator/= (const int&);      // 高精 /= 低精
    hint& operator%= (const int&);      // 高精 %= 低精
    /* 输入输出重载 */
    friend istream& operator>> (istream&, hint&) ;
    friend ostream& operator<< (ostream&, const hint&);
};
hint::hint(ll x = 0) {
    a.clear();
    do {
        a.push_back(x % w);
        x /= w;
    } while (x);
}
hint::hint(string s) {
    a.clear();
    reverse(s.begin(), s.end());
    int n = s.size();
    for (int i = 0; i < n; i += wsize) {
        string tmp = s.substr(i, wsize);
        reverse(tmp.begin(), tmp.end());
        a.push_back(stoll(tmp));
    }
}
/* 比较(未验证) */
bool hint::operator< (const hint& b) const {
    if (len() != b.len()) return len() < b.len();
    for (int i = len() - 1; i > 0; --i) {
        if (a[i] != b.a[i]) return a[i] < b.a[i];
    }
    return 0;
}
/* 各种运算 */
hint& hint::operator+= (const hint& b) {
    while (len() < b.len()) a.push_back(0);
    ll c = 0;
    int n = len(), m = b.len();
    for (int i = 0; i < n; ++i, c /= w) {
        c += a[i];
        if (i < m) c += b[i];
        a[i] = c % w;
    }
    if (c) a.push_back(c);
    return *this;
}
hint operator+ (const hint& a, const hint& b) {
    hint res = a;
    return (res += b);
}
hint& hint::operator-= (const hint& b) {
    // assert(*this >= b);
    ll t = 0;
    for (int i = 0, n = len(), m = b.len(); i < n; ++i) {
        t = a[i] - t;
        if (i < m) t -= b[i];
        a[i] = (t + w) % w, t = (t < 0);
    }
    while (len() > 1 && !a.back()) a.pop_back();
    return *this;
}
hint operator-(const hint& a, const hint& b) {
    hint res = a;
    return (res -= b);
}
hint& hint::operator*= (const int& b) {
    ll t = 0;
    for (int i = 0, n = len(); i < n || t; ++i, t /= w) {
        t += a[i] * b;
        if (i < n) a[i] = t % w;
        else a.push_back(t % w);
    }
    while (a.size() > 1 && !a.back()) a.pop_back();
    return *this;
}
hint hint::operator* (const int& b) {
    hint res = *this;
    return (res *= b);
}
hint operator* (const hint& a, const hint& b) {
    int n = a.len(), m = b.len();
    hint res;
    res.a.resize(n + m, 0);
    for (int i = 0; i < n; ++i) {
        ll up = 0;
        for (int j = 0; j < m; ++j) {
            ll tmp = a[i] * b[j] + res[i + j] + up;
            res[i + j] = tmp % hint::w;
            up = tmp / hint::w;
        }
        if (up) res[i + m] = up;
    }
    while (res.len() > 1 && !res.a.back()) res.a.pop_back();
    return res;
}
hint& hint::operator*= (const hint& b) {
    return *this = *this * b;
}
hint& hint::operator/= (const int& b) {
    ll r = 0;
    for (int i = len() - 1; i >= 0; --i) {
        r = r * w + a[i];
        if (r < b) a[i] = 0;
        else a[i] = r / b, r %= b;
    }
    while (len() > 1 && !a.back()) a.pop_back();
    return *this;
}
hint hint::operator/ (const int& b) {
    hint res = *this;
    return (res /= b);
}
hint& hint::operator%= (const int& b) {
    ll r = 0;
    for (int i = len() - 1; i >= 0; --i) {
        r = r * w + a[i];
        if (r >= b) r %= b;
    }
    return *this = hint(r);
}

hint hint::operator% (const int& b) {
    hint res = *this;
    return res %= b;
}

/* 输入输出重载 */
istream& operator>> (istream &in, hint &res) {
    string s;
    in >> s;
    res = hint(s);
    return in;
}
ostream& operator<< (ostream &out, const hint& res) {
    out << res.a.back();
    for(int i = res.len() - 2; i >= 0; --i) {
        out << setw(8) << setfill('0') << res[i];
    }
    return out;
}

int main() {
    hint a;
    int b;
    cin >> a >> b;
    cout << a / b;
    return 0;
}
```

#### 简化版

```cpp
#define bint vector<int>


int cmp(const bint& x, const bint& y) { // 1:x>y, 0:x=y, -1:x<y
    if (x.size() != y.size()) {
        return x.size() > y.size() ? 1 : -1;
    }
    for (int i = x.size() - 1; i >= 0; --i) {
        if (x[i] != y[i]) {
            return x[i] > y[i] ? 1 : -1;
        }
    }
    return 0;
}
// 正整数加
bint add(const bint& x, const bint& y) {
    if (x.size() < y.size()) return add(y, x);
    bint res;
    int c = 0, n = x.size(), m = y.size();
    for (int i = 0; i < n; ++i, c /= 10) {
        c += x[i];
        if (i < m) c += y[i];
        res.push_back(c % 10);
    }
    if (c) res.push_back(c);
    return res;
}
// 正整数 x >= y
bint sub(const bint& x, const bint& y) {
    bint res;
    int n = x.size(), m = y.size();
    for (int i = 0, t = 0; i < n; ++i, t = (t < 0)) {
        t = x[i] - t;
        if (i < m) t -= y[i];
        res.push_back((t + 10) % 10);
    }
    while (res.size() > 1 && res.back() == 0) res.pop_back();
    return res;
}

bint mul(const bint& x, const int y) {
    bint res;
    int t = 0, n = x.size();
    for (int i = 0; i < n || t; ++i, t /= 10) {
        if (i < n) t += x[i] * y;
        res.push_back(t % 10);
    }
    while (res.size() > 1 && res.back() == 0) res.pop_back();
    return res;
}

bint mul(const bint& x, const bint& y) {
    int n = x.size(), m = y.size();
    bint res(n + m, 0);
    for (int i = 0; i < n; ++i) {
        int up = 0;
        for (int j = 0; j < m; ++j) {
            int tmp = x[i] * y[j] + res[i + j] + up;
            res[i + j] = tmp % 10;
            up = tmp / 10;
        }
        if (up) res[i + m] = up;
    }
    while (res.size() > 1 && res.back() == 0) res.pop_back();
    return res;
}

bint div(const bint& x, const int y, ll &r) {
    bint res;
    for (int i = x.size() - 1; i >= 0; --i) {
        r = r * 10 + x[i];
        res.push_back(r / y);
        r %= y;
    }
    reverse(res.begin(), res.end());
    while (res.size() > 1 && res.back() == 0) res.pop_back();
    return res;
}


void print(const bint& x) {
    for (int i = x.size() - 1; i >= 0; --i) {
        cout << x[i];
    }
}
```

### 4. 差分

```cpp
const int M = 1e5 + 10;
int dif[M], arr[M], res[M];

void change(int l, int r, int v) { // [l, r] + v
    dif[l] += v, dif[r + 1] -= v;
}
void init(int n) {
    dif[1] = arr[1];
    for (int i = 2; i <= n; ++i) dif[i] = arr[i] - arr[i - 1];
}
void get_res(int n) {
    res[1] = dif[1];
    for (int i = 2; i <= n; ++i) {
        res[i] = dif[i] + res[i - 1];
    }
}
```

### 5.三分

```cpp
double ts(int l, int r) {
    double ans1 = 0x3f3f3f3f, ans2 = 0x3f3f3f3f;
    while (l < r) {
        int lmid = l + (r - l) / 3;
        int rmid = r - (r - l) / 3;
        ans1 = f(lmid), ans2 = f(rmid);
        if (ans1 <= ans2) r = rmid - 1; // 凹函数的最小值， 凸函数<=反过来
        else l = lmid + 1;
    }
    return min(ans1, ans2); //凸函数这里记得改成max
}
```

### 6. cout控制宽度和精度

```cpp
#include <iomanip>
// setw()只对其后面紧跟的输出产生作用
cout << setw(8) << setfill('0') << 123 << endl; // 00000123
cout.precision(3); // 之后的输出都置保留3位有效数字
cout.setf(ios::fixed); // 固定小数点，即之后的都保留3位小数
cout.unsetf(ios::fixed); // 取消固定小数点
```

### 7. 快读快输

```cpp
inline char getc() {// 重写getchar
    static char buf[1 << 21], *p1 = buf, *p2 = buf;
    return p1 == p2 && (p2 = (p1 = buf) + 
        fread(buf, 1, 1 << 21, stdin), p1 == p2) ? EOF : *p1++;
}
inline ll IO() {
    long long x = 0;
    int f = 0, c = getchar();
    for (; !isdigit(c) && c != -1; c = getchar()) {
        if (c == '-') f = 1;
    }
    //if (c == -1) exit(0); 
    for (; isdigit(c); c = getchar()) {
        x = (x << 1) + (x << 3) + (c ^ '0');
    }
    return f ? -x : x;
}
void getstr(string &s) { // 字符串快读
    int c = getchar();
    while (c < 33 && c != -1) c = getchar();
    //if (c == -1) exit(0);
    s.clear();
    for(;c > 32; c = getchar()) s.push_back(c);
}
void print(ll x) {
    if (x < 0) putchar('-'), x = -x;
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
void print(const string& s) { for (char c : s) putchar(c); }
template <typename T>
void print(T x, char c) { print(x); putchar(c); }
```

