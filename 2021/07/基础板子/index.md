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

#### 复杂版

```cpp {.line-numbers}
#include <cstdio>
#include <cctype>
#include <cstring>
#include <algorithm>
#include <string>
#include <iostream>

using namespace std;

typedef long long ll;
/*
用前必读：
    本高精采用可调压位式运算对于不同需求记得更改下面的压位代码
    注意笔者只封装了 bign * int 没有封装 int * bign , 用时注意顺序
    若出现 bign * x,中 x 不是常量变量或者不是变量（例如具体数字 3 4 5……）
        记得把封装的与int相关的 运算符重载函数 的const和取地址符&去掉，防止报错
    若是爆栈请尝试开全局变量或者把数组改小即 M 改小
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
    其余请读者自己体会
*/

const int w = 1e8, M = 1e4, wsize = 8;//压位8个0
const char pout[] = "%08lld";//记得修改
struct bign{
    ll num[M];
    char str[M * wsize];
    int len;
    void clear() { memset(num, 0, sizeof num); len = 0;}
       /* 初始化 */
    bign() : len(0) { clear(); }
    bign(int n);
    bign(ll n);
    bign(char str[]);
    bign(string str);
    void change(); 
    void operator= (const int x) { *this = bign(x); }
    void operator= (const ll x) { *this = bign(x); }
    void operator= (char x[]) { *this = bign(x); }
    void operator= (string x) { *this = bign(x); }
    ll &operator[] (int x) { return num[x]; }
    /* 输出 */
    void print();
    void print() const;
    /* 比较(未验证) */
    bool operator< (const bign &b) const;
    bool operator> (const bign &b) const { return b < *this; }
    bool operator<= (const bign &b) const { return !(b < *this); }
    bool operator>= (const bign &b) const { return !(*this < b); }
    bool operator!= (const bign &b) const { return b < *this || *this < b; }
    bool operator== (const bign &b) const { return !(b < *this) && !(*this < b); }
    /* 各种运算 */
    bign operator+ (const bign &b) const; // 高精 + 高精
    bign operator* (const int &b) const; // 高精 * 低精
    bign operator* (const bign &b);      // 高精 * 高精
    bign operator- (const bign &b) const; // 高精 - 高精
    bign operator/ (const int &b) const; // 高精 / 低精
    bign operator% (const int &b);       // 高精 % 低精
    void operator+= (const bign &b);     // 高精 += 高精
    void operator*= (const int &b);      // 高精 *= 低精
    void operator*= (const bign &b);     // 高精 *= 高精
    void operator-= (const bign &b);     // 高精 -= 高精
    void operator/= (const int &b);      // 高精 /= 低精
    void operator%= (const int &b);      // 高精 %= 低精
    /* 输入输出重载 */
    friend istream& operator>> (istream &in, bign &res) ;
    friend ostream& operator<< (ostream &out, const bign &res) ;

};
bign::bign(int n) : len(0) {
    clear();
    while (1) {
        num[++len] = n % w, n /= w;
        if (!n) break;
    }
}
bign::bign(ll n) : len(0) {
    clear();
    while (1) {
        num[++len] = n % w, n /= w;
        if (!n) break;
    }
}
bign::bign(char str[]) : len(0) {
    clear();
    int l = strlen(str) - 1;
    for (int i = l; i >= 0; i -= wsize) {
        ll tmp = 0, k = 1;
        for (int j = 0; j < wsize && i - j >= 0; j++, k *= 10) {
            tmp += (str[i - j] - '0') * k;
        }
        num[++len] = tmp;
    }
}
bign::bign(string str) : len(0) {
    clear();
    int l = str.size() - 1;
    for (int i = l; i >= 0; i -= wsize) {
        ll tmp = 0, k = 1;
        for (int j = 0; j < wsize && i - j >= 0; j++, k *= 10) {
            tmp += (str[i - j] - '0') * k;
        }
        num[++len] = tmp;
    }
}
void bign::change() {
    int l = strlen(str) - 1;
    len = 0;
    for (int i = l; i >= 0; i -= wsize) {
        ll tmp = 0, k = 1;
        for (int j = 0; j < wsize && i - j >= 0; j++, k *= 10) {
            tmp += (str[i - j] - '0') * k;
        }
        num[++len] = tmp;
    }
}
/* 输出 */
void bign::print() {
    printf("%lld", num[len]);
    for (int i = len - 1; i > 0; i--) printf(pout, num[i]);
    puts("");
}

void bign::print() const {
    printf("%lld", num[len]);
    for (int i = len - 1; i > 0; i--) printf(pout, num[i]);
    // puts("");
}

/* 比较(未验证) */
bool bign::operator< (const bign &b) const {
    if (len != b.len) return len < b.len;
    for (int i = len; i > 0; i--) {
        if (num[i] != b.num[i]) return num[i] < b.num[i];
    }
    return false;
}

/************************常用****************************/
/* 各种运算 */
bign bign::operator+ (const bign &b) const {
    bign res = *this;
    if (res.len < b.len) res.len = b.len;
    for (int i = 1; i <= res.len; i++) {
        res.num[i] += b.num[i];
        res.num[i + 1] += res.num[i] / w;
        res.num[i] %= w;
    }
    while (res.num[res.len + 1]) res.len++;
    return res;
}

bign bign::operator* (const int &b) const {
    bign res;
    ll carry = 0;
    for (int i = 1; i <= len; i++) {
        ll tmp = num[i] * b + carry;
        res.num[++res.len] = tmp % w;
        carry = tmp / w;
    }
    while (carry) {
        res.num[++res.len] = carry % w;
        carry /= w;
    }
    return res;
}

bign bign::operator* (const bign &b) {
    bign res;
    for (int i = 1; i <= len; i++) {
        ll up = 0;
        for (int j = 1; j <= b.len; j++) {
            ll tmp = num[i] * b.num[j] + res.num[i + j - 1] + up;
            res.num[i + j - 1] = tmp % w;
            up = tmp / w;
        }
        if (up) res.num[i + b.len] = up;
    }
    res.len = len + b.len;
    while (res.len > 1 && res.num[res.len] == 0) res.len--;
    return res;
}
/********************************************************/

bign bign::operator- (const bign &b) const {
    bign res = *this;
    for (int i = 1; i <= len; i++) {
        if (res.num[i] < b.num[i]) res.num[i] += w, res.num[i + 1]--;
        res.num[i] -= b.num[i];
    }
    while (res.len > 1 && res.num[res.len] == 0) res.len--;
    return res;
}

bign bign::operator/ (const int &b) const {
    bign res;
    res.len = len;
    ll r = 0;//余数
    for (int i = len; i > 0; i--) {
        r = r * w + num[i];
        if (r < b) res.num[i] = 0;
        else res.num[i] = r / b, r %= b;
    }
    while (res.len > 1 && res.num[res.len] == 0) res.len--;
    return res;
}

bign bign::operator% (const int &b) {
    ll r = 0;//余数
    for (int i = len; i > 0; i--) {
        r = r * w + num[i];
        if (r >= b) r %= b;
    }
    return bign(r);
}
void bign::operator+= (const bign &b) {
    if (len < b.len) len = b.len;
    for (int i = 1; i <= len; i++) {
        num[i] += b.num[i];
        num[i + 1] += num[i] / w;
        num[i] %= w;
    }
    while (num[len + 1]) len++;
}
void bign::operator*= (const int &b) {
    for (int i = 1; i <= len; i++) num[i] *= b;
    for (int i = 1; i <= len; i++) {
        num[i + 1] += num[i] / w;
        num[i] %= w;
    }
    while(num[len + 1]) {
        len++;
        num[len + 1] = num[len] / w;
        num[len] %= w;
    }
}

void bign::operator*= (const bign &b) {
    *this = *this * b;
}

void bign::operator-= (const bign &b) {
    for (int i = 1; i <= len; i++) {
        if (num[i] < b.num[i]) num[i] += w, num[i + 1]--;
        num[i] -= b.num[i];
    }
    while (len > 1 && num[len] == 0) len--;
}

void bign::operator/= (const int &b) {
    ll r = 0;
    for (int i = len; i > 0; i--) {
        r = r * w + num[i];
        if (r < b) num[i] = 0;
        else num[i] = r / b, r %= b;
    }
    while (len > 1 && num[len] == 0) len--;
}

void bign::operator%= (const int &b) {
    ll r = 0;
    for (int i = len; i > 0; i--) {
        r = r * w + num[i];
        if (r >= b) r %= b;
    }
    *this = bign(r);
}

istream& operator>> (istream &in, bign &res) {
    in >> res.str;
    res.change();
    return in;
}
ostream& operator<< (ostream &out, const bign &res) {
    res.print();
    return out;
}

int main() {
    return 0;
}
```

#### 简化版

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
#include <string>
#include <vector>
#define ll long long
using namespace std;
// 坑： 没有处理好正负号
const int wsize = 8;//压位8个0
const ll w = 1e8;
const char pout[] = "%08lld";//记得修改
struct bint {
    vector<ll> a;
    bint() : a(0) {}
    bint(ll x);
    bint(string s);
    void pb(ll x) { a.push_back(x); }
    int len() { return (int)a.size() - 1; }
    void operator= (const int x) { *this = bint(x); }
    void operator= (const ll x) { *this = bint(x); }
    void operator= (char x[]) { *this = bint(x); }
    void operator= (string x) { *this = bint(x); }
    ll &operator[] (int x) { return a[x]; }
    /* 输出 */
    void print();
    /* 比较(未验证) */
    bool operator< (bint b);
    bool operator- ();
    bool operator> (bint b) { return b < *this; }
    bool operator<= (bint b) { return !(b < *this); }
    bool operator>= (bint b) { return !(*this < b); }
    bool operator!= (bint b) { return b < *this || *this < b; }
    bool operator== (bint b) { return !(b < *this) && !(*this < b); }
    /* 各种运算 */
    bint operator+ (bint b);  // 高精 + 高精
    bint operator* (int b);  // 高精 * 低精
    bint operator* (bint b);      // 高精 * 高精
    bint operator- (bint b);  // 高精 - 高精
    bint operator/ (int b);  // 高精 / 低精
    bint operator% (int b);       // 高精 % 低精
};

bint::bint(ll n) {
    a.clear();
    a.push_back(n < 0 ? -1 : 1);
    while (1) {
        a.push_back(n % w), n /= w;
        if (!n) break;
    }
}

bint::bint(string s) {
    a.clear();
    if (s[0] == '-') {
        a.push_back(-1);
        s = s.substr(1);
    } else a.push_back(1);
    int len = s.size() - 1;
    for (int i = len; i >= 0; i -= wsize) {
        ll tmp = 0, k = 1;
        for (int j = 0; j < wsize && i - j >= 0; ++j, k *= 10) {
            tmp += (s[i - j] - '0') * k;
        }
        a.push_back(tmp);
    }
}


void bint::print() {
    if (a[0] < 0) putchar('-');
    printf("%lld", a.back());
    for (int i = len() - 1; i > 0; --i) printf(pout, a[i]);
}


bool bint::operator< (bint b) {
    if (a[0] ^ b[0]) return a[0] < 0;
    if (len() != b.len()) return len() < b.len();
    for (int i = len(); i > 0; i--) {
        if (a[i] != b.a[i]) return a[i] < b.a[i];
    }
    return false;
}
/************************常用****************************/

// 加法 同号
bint add(bint& a, bint& b) {
    if (a.len() < b.len()) return add(b, a);
    bint res;
    res.pb(a[0]);
    ll sum = 0;
    int n = a.len(), m = b.len();
    for (int i = 1; i <= n; ++i, sum /= w) {
        sum += a[i];
        if (i <= m) sum += b[i];
        res.pb(sum % w);
    }
    if (sum) res.pb(sum);
    return res;
}
// 减法  abs(a) >= abs(b)
bint sub(bint& a, bint& b) {
    bint res;
    res.pb(a[0]);
    ll last = 0;
    int n = a.len(), m = b.len();
    for (int i = 1; i <= n; ++i, last = (last < 0)) {
        last = a[i] - last;
        if (i <= m) last -= b[i];
        res.pb((last + w) % w);
    }
    while (res.len() > 1 && res.a.back() == 0) res.a.pop_back();
    return res;
}

// 乘法
bint mul(bint& a, bint& b) {
    bint res;
    res.pb(a[0] * b[0]);
    int n = a.len(), m = b.len();
    for (int i = 1; i <= n + m; ++i) res.pb(0);
    for (int i = 1; i <= n; ++i) {
        ll up = 0;
        for (int j = 1; j <= m; ++j) {
            ll tmp = a[i] * b[j] + res[i + j - 1] + up;
            res[i + j - 1] = tmp % w;
            up = tmp / w;
        }
        if (up) res[i + m] = up;
    }
    while (res.len() > 1 && res.a.back() == 0) res.a.pop_back();
    return res;
}

// 除法
bint div(bint& a, ll b, ll& r) {
    bint res;
    res.pb(a[0] * b >= 0);
    r = 0;
    for (int i = a.len(); i >= 1; --i) res.pb(0);
    for (int i = a.len(); i >= 1; --i) {
        r = r * w + a[i];
        if (r >= b) res[i] = r / b, r %= b;
    }
    while (res.len() > 1 && res.a.back() == 0) res.a.pop_back();
    return res;
}

/*****************************各种运算操作******************************/
// +
bint bint::operator+ (bint b) {
    return add(*this, b);
}
// -
bint bint::operator- (bint b) {
    return sub(*this, b);
}
// *
bint bint::operator* (int b) {
    bint res;
    res.pb(a[0] * b < 0 ? -1 : 1);
    int n = len();
    ll nxt = 0;
    for (int i = 1; i <= n; ++i) {
        ll tmp = a[i] * b + nxt;
        res.pb(tmp % w);
        nxt = tmp / w;
    }
    while (nxt) {
        res.pb(nxt % w);
        nxt /= w;
    }
    return res;
}
// *
bint bint::operator* (bint b) {
    return mul(*this, b);
}
// /
bint bint::operator/ (int b) {
    ll r;
    return div(*this, b, r);
}

// % 
bint bint::operator% (int b) {
    ll r = 0;
    for (int i = len(); i > 0; --i) {
        r = r * w + a[i];
        if (r >= b) r %= b;
    }
    return bint(r);
}

int main() {
    bint a, b;
    string s1, s2;
    cin >> s1 >> s2;
    a = s1, b = s2;
    bint res = a * b;
    res.print();
    return 0;
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

