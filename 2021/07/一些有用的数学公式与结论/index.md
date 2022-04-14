# 一些有用的数学公式与结论


# 数学小总结

## 质数

1. 算术基本定理：$N = {P_1}^{k_1} \times{P_2}^{k_2} \times {P_3}^{k_3} \cdots \times {P_n}^{k_n} \ \ , \ \ P_1 < P_2 < P_3 < \cdots < P_n$
2. 一个数N约数个数：$(k_1 + 1) \times (k_2 + 1) \times (k_3 + 1) \times \cdots \times (k_n + 1)$，k是算术基本定理中的k
3. 一个数N的所有正因数的和：$({P_1} ^ {0} + {P_1} ^ {1} + {P_1} ^ {2} \cdots + {P_1} ^ {k_1})\times ({P_2} ^ {0} + {P_2} ^ {1} + {P_2} ^ {2} \cdots + {P_2} ^ {k_2}) \times \cdots \times ({P_n} ^ {0} + {P_n} ^ {1} + {P_n} ^ {2} \cdots + {P_n} ^ {k_n})$
4. 费马小定理：$a^{p - 1} \equiv 1 \pmod p$，p是素数

## 中位数

对于一个数列，$\sum \lvert x_i - y \rvert$在y取数列的中位数时最小

## gcd

1. $gcd(a, b) = gcd(b, a \bmod b)$
2. $gcd(a, b) = gcd(a, b - a)$
3. 由算术基本定理：
   1. 假设$N = {P_1}^{a_1} \times{P_2}^{a_2} \times {P_3}^{a_3} \cdots \times {P_n}^{a_n}$
   2. 而$M = {P_1}^{b_1} \times{P_2}^{b_2} \times {P_3}^{b_3} \cdots \times {P_n}^{b_n}$
   3. 则$gcd(N, M) = P_1^{min(a_1, b_1)}\times P_2^{min(a_2, b_2)}\times P_3^{min(a_3, b_3)}\times \dots \times P_n^{min(a_n, b_n)}$
   4. $lcm(N, M) = P_1^{max(a_1, b_1)}\times P_2^{max(a_2, b_2)}\times P_3^{max(a_3, b_3)}\times \dots \times P_n^{max(a_n, b_n)}$
4. 斐波那契数列$f(1) = 1, f(2) = 1, f(3) = 2\dots$，则$gcd(f(a), f(b)) = f(gcd(a, b))$
5. 由第2条性质可推广：$gcd(a_1, a_2, a_3, \dots a_n) = gcd(a_1, a_2 - a_1, a_3 - a_2\dots a_n - a_{n- 1})$
6. $gcd$具区间单调性：对于区间$l_3 \le l_2 \le l_1 \le r_1 \le r_2 \le r_3$ $gcd(l_3, r_3)\le gcd(l_2, r_2) \le gcd(l_1, r_1)$
7. $gcd$具有区间反向包含性质：对于$gcd(l_1, r_1)$，$gcd(l_2, r_2)$，$l_1\le l_2 \le r_2 \le r_1$则$gcd(l_1, r_1)$一定是$gcd(l_2, r_2)$的因子。

## 卡特兰

1. 公式一：$f(n) = \sum_{i = 0}^{n - 1}{f(i) \times f(n - i - 1)}$
2. 公式二：$f(n) = \frac{f(n - 1) \times (4n - 2)}{n + 1}$
3. 公式三：$f(n) = \frac{C_{2n}^{n}}{n + 1}$
4. 公式四：$f(n) = C_{2n}^{n} - C_{2n}^{n - 1}$
5. 变形：给你$n$个1和$m$个0组合$n \ge m$，求一个组合满足前k个中1的个数不少于0的个数的方案数：$C_{n + m}^{n} - C_{n + m}^{n - 1}$

## 组合数

1. 通项公式：$C_{n}^{m} = \frac{n!}{m!(n - m)!} = \frac{n\times(n - 1)\times \dots \times (n - m + 1)}{m!}$ 
2. 递推公式：$C_{n}^{m} = C_{n - 1}^{m - 1} + C_{n - 1}^{m} = \frac{n - m + 1}{m}\times C_{n}^{m - 1}$
3. 有重复元素的全排列：有k个元素，取其中第i个元素有$n_i$个，全排列的个数：$\frac{n!}{n_1!n_2!n_3!\dots n_k!}$，其中$n = \sum{n_i}$
4. 可重复选择的组合：有n个不同元素，每个元素可以选多次，一共选k个元素，方案数：$C_{k + n - 1}^{n - 1}$
5. 性质1：$C_{n + m + 1}^{m} = \sum_{i = 0}^{m}{C_{n + i}^{i}}$
6. 性质2：$C_{n}^{m}\times C_{m}^{i} = C_{n}^{i}\times C_{n - i}^{m - i}$
7. 性质3：$\sum_{i = 0}^{n}{C_{n}^{i}} = 2 ^ n$
8. 性质4：$\sum_{i = 0}^{n}{C_{n}^{i}\times x^i} = (x + 1)^n$
9. 性质5：$C_{n}^{0} - C_{n}^{1} + C_{n}^{2} - \dots \pm C_{n}^{n} = 0$
10. 性质6：$C_{n}^{0} + C_{n}^{2} + C_{n}^{4}+\dots = C_{n}^{1}+C_{n}^{3}+C_{n}^{5}+\dots = 2^{n - 1}$
11. 性质7：$C_{n + m}^{i} = C_{n}^{0} \times C_{m}^{i} + C_{n}^{1} \times C_{m}^{i - 1} \times \dots \times C_{n}^{i}\times C_{m}^{0}$
12. 性质8：$m \times C_{n}^{m} = n \times C_{n - 1}^{m - 1}$
13. 性质9：$\sum_{i = 1}^{n}{C_{n}^{i}\times i}= n \times 2^{n - 1}$
14. 性质10：$\sum_{i = 1}^{n}{C_{n}^{i}} \times i^2 = n \times (n + 1) \times 2^{n - 2}$ 
15. 性质11：$\sum_{i = 0}^{n}{(C_{n}^{i})^2} = C_{2n}^{n}$

## 斯特林数

**第一类斯特林数**

记作 ${n \brack m}$，写作 $s(n, m)$

> 将n个不同的数，分成m组（每组都不能为空），每组都是一个环（圆排列），求不同方案数

- 递推式

$$
{n \brack m} = {n - 1\brack m - 1} + (n - 1) \times {n - 1 \brack m}
$$

- 通项公式：无
- 性质1 ：$s(0, 0) = 1$
- 性质2 ：$s(n, 0) = 0$
- 性质3 ：$s(n, n) = 1$
- 性质4 ：$s(n, 1) = (n - 1)!$
- 性质5 ：$s(n, n - 1) = C_n^2$
- 性质6 ：$s(n, 2) = (n - 1)! \times \sum_{i=1}^{n-1}1/i$
- 性质7 ：$\sum_{i=0}^n s(n, i) = n!$
- 性质8 ：$s(n,n-2) = 2 C_n^3 + 3 C_n^4$
- 性质9 ：$s(n, m) = \sum_{i = 1}^n C_{n-1}^{i - 1}(i - 1)!s(n - i, m - 1)$

**第二类斯特林数**

记作${n \brace m}$，写作 $S(n, m)$

> 将n个不同的数，分成m组（每组都不能为空），求不同方案数

- 递推式

$$
{n \brace m} = {n - 1 \brace m - 1} +  m \times {n - 1\brace m}
$$

- 通项公式：

$$
{n \brace m} = \frac{1}{m!}\sum_{i=0}^m (-1)^i C_m^i (m - i)^n
$$

- 卷积形式公式：(利用该公式，可以使用FFT求出所有${n \brace 0},{n \brace 1}...{n \brace n}$)

$$
{n \brace m} = \sum_{i=0}^m\frac{(-1)^i(m-i)^n}{i!(m-i)!}
$$

- 性质1 ：$S(0, 0) = 1$
- 性质2 ：$S(n, 0) = 0$
- 性质3 ：$S(n, 1) = 1$
- 性质4 ：$S(n, n) = 1$
- 性质5 ：$S(n, 2) = 2^{n - 1} - 1$
- 性质6 ：$S(n, n - 1) = C_n^2$
- 性质7 ：$S(n,n-2)=C_n^3 + 3 C_n^4$
- 性质8 ：$S(n,3) = \frac{1}{2} (3^{n-1} + 1) - 2^{n - 1}$
- 性质9 ：$S(n,n-3) = C_n^4 + 10 C_n^5 + 15 C_n^6$
- 性质10 ：$S(n, m) = \sum_{i = 1}^nS(n - i, k - 1) C_{n - 1}^{i - 1}$
- 性质11 ：$x^n = \sum_{i = 0}^xS(n, i) \times C_x^i \times i!$

**两类斯特林的关系**
$$
\sum_{i=0}^n {n \brack i} {i \brace m} = \sum_{i=0}^n {n \brace i} {i \brack m}
$$

## 欧拉函数

1. 性质1：若p为质数则$\phi(p) = p - 1$
2. 性质2：若p为质数则$\phi(p^a) = (p - 1) \times p^{a - 1}$
3. 性质3：当$n$，$m$互质时，则有$\phi(nm) = \phi(n) \times \phi(m)$
4. 性质4：若$a$，$m$互质时，则有$a^{\phi(m)} \equiv 1 \pmod m$
5. 性质5：若$p$为质数，且$i \bmod p = 0$，则$\phi(i \times p) = p \times \phi(i)$
6. 性质6：若$p$为质数，且$i \bmod p \ne 0$，则$\phi(i \times p) = \phi(i) \times (p - 1)$
7. 性质7：若$n$为奇数，则$\phi(2n) = \phi(n)$
8. 性质8：若$n$、$p$互质，则$p-n$也与$p$互质，因此小于$p$且与$p$互质的数之和为$\phi(n) \times n / 2$
9. 性质9：若$n > 1$，则小于$n$且与其互质的所有正整数之和为$\phi(n) \times n / 2$

## 欧拉降幂公式

- 一般将第一行的情况会当做第三行处理

$$
a^b \equiv
\begin{cases}
a^{b \bmod \phi (p)} & gcd(a, p) = 1 \newline
a^{b} & gcd(a, p) \ne 1 , b< \phi (p) \newline 
a^{b \bmod \phi (p) + \phi (p)} & gcd(a, p) \ne 1 , b \geqslant \phi (p)
\end{cases}
\pmod p
$$



## 求和公式的变换

- 无关系数提前：$\sum_{i=0}^{n} p \ f(i) = p\sum_{i=0}^{n} f(i)$
- 左右置换：$\sum_{i=0}^n \sum_{j=0}^m f(i) \ast g(j) = \sum_{j=0}^m \sum_{i=0}^n f(i) \ast g(j)$
- 矩阵的下三角：$\sum_{i=0}^n \sum_{j=0}^{i} f(i) \ast g(j) = \sum_{j=0}^n \sum_{i=j}^n f(i) \ast g(j)$
- 因数和倍数枚举的变换：$\sum_{i = 1}^n \sum_{d\vert i} f(i) \ast g(d) \ast h(\frac{i}{d})= \sum_{d=1}^n \sum_{i=1}^{\lfloor \frac{n}{d} \rfloor} f(i \times d) \ast g(d) \ast h(i)$

## 莫比乌斯反演公式

莫比乌斯函数，p为质数，q为整数
$$
\mu (n) =
\begin{cases}
(-1)^k & n = p_1\ p_2\ p_3 \ldots p_k \newline
0  & n = p^2 q
\end{cases}
$$



1. 性质1：$\sum_{d \vert n} \mu(d) = [n == 1]$
2. 性质2：$\sum_{d \vert n} \frac{\mu(d)}{d} = \frac{\phi(n)}{n}$

**莫比乌斯反演公式**

- 一般形式（因子关系）

$$
\begin{aligned}
f(n) &= \sum_{d \vert n} \ g(d) \newline
g(n) &= \sum_{d \vert n} \mu(d) \ f(\frac{n}{d})
\end{aligned}
$$

- 其他形式（倍数关系）

$$
\begin{aligned}
f(n) &= \sum_{n \vert d} \ g(d) \newline
g(n) &= \sum_{n \vert d} \mu(\frac{d}{n}) \ f(d)
\end{aligned}
$$



## 二项式反演公式

- 通常用来解决容斥问题
- 函数$f$一般和函数$g$互为补集
- 一般形式

$$
\begin{aligned}
f(n) &= \sum_{i=0}^{n} \  (-1)^i \ C_n^i \ g(i) \newline
g(n) &= \sum_{i=0}^{n} \  (-1)^i \ C_n^i \ f(i)
\end{aligned}
$$

- 常见形式

$$
\begin{aligned}
f(n) &= \sum_{i=0}^{n} \ C_n^i \ g(i) \newline
g(n) &= \sum_{i=0}^{n} \ (-1)^{n-i} \ C_n^i \ f(i)
\end{aligned}
$$

- 其他形式

$$
\begin{aligned}
f(n) &= \sum_{i=n}^{m} \ C_i^{n} \ g(i) \newline
g(n) &= \sum_{i=n}^{m} \ (-1)^{i-n} \ C_i^n \ f(i)
\end{aligned}
$$



## 斯特林反演公式

- 一般形式

$$
\begin{aligned}
f(n) &= \sum_{i = 0}^{n} \ {n \brace i} \ g(i) \newline
g(n) &= \sum_{i = 0}^{n} \ (-1)^{n - i} \ {n \brack i} \ f(i)
\end{aligned}
$$



## 阶

1. 性质1：$a, a^2, \dots,a^{\delta_m(a)}$模m两两不同余
2. 性质2：若 $a^n \equiv 1 \pmod m$，则$\delta_m(a) \vert n$
3. 性质3：对于正整数$m$，整数$a, b$，若有$gcd(a, m) = gcd(b, m) = 1$,则 $\delta_m(ab) = \delta_m(a)\delta_m(b)$ 的充要条件是 $gcd(\delta_m(a), \delta(b)) = 1$
4. 性质4：对于非负整数k，正整数m，整数a，若有$gcd(a, m) = 1$, 则$\delta_m(a^k) = \frac{\delta_m(a)}{gcd(\delta_m(a), k)}$

## 原根

定义：对于正整数$m$，存在整数$a$且$gcd(a, m) = 1$，且$\delta_m(a) = \phi(m)$，则说a为m的原根

原根判定定理：设$m\ge 3$ , $gcd(a, m) = 1$，则$a$是模$m$的原根的充要条件是，对于$\phi(m)$的每一个质因子$p$，都有$a^{\frac{\phi(m)}{p}} \not \equiv 1 \pmod m$，这从原根的定义和上述阶的性质2来想就很好理解

若$m$有原根，则其原根的个数为$\phi(\phi(m))$

原根存在定理：对于一个数$m$有原根，当且仅当 $m = 2, 4, p^{x},2p^x$，其中$p$为奇素数，x为正整数

若$m$有原根$g$则其他原根可以写$g^x \pmod m                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                $，其中x是不大于$\phi(m)$的数且与$\phi(m)$互质的数（可以用原根判定定理来理解）

## 多项式乘法/卷积

常见乘法，直接套FFT/NTT就好了
$$
h(i) = \sum_{j = 0}^{i} f(i) \times g(i - j)
$$
需要对其中一个多项式进行翻转后再套FFT/NTT（n一般为f函数的最高项式x的幂）
$$
\begin{aligned}
h(i) &= \sum_{j = i}^n f(j) \times g(j - i) \newline
&= \sum_{j = 0}^{n - i} f(i + j) \times g(j) \newline
&\text{令} \ rev(i) = f(n - i) \newline
H(i) &=  \sum_{j = 0}^{i} rev(i - j) \times g(j) \newline
h(i) &= H(n - i)
\end{aligned}
$$
分治 + FFT（注意不是分治FFT）
$$
\prod_{i = 0} \sum (a_i + b_i \times x)
$$
卷积满足交换律、结合律、加法分配律
$$
\begin{aligned}
f * g &= g * f \newline
(f * g) * h &= f * (g * h) \newline
f * (g + h) & = f * g + f * h
\end{aligned}
$$


分治FFT
$$
f(i) = \sum_{j = 0}^{j - 1} f(j) \times g(i - j)
$$

NTT一些模数

```cpp
//（g 是mod(r*2^k+1)的原根）
// 素数  r  k  g
469762049   7   26  3
998244353   119 23  3
1004535809  479 21  3
75161927681 35  31  3
206158430209    3   36  22
2061584302081   15  37  7
39582418599937  9   42  5
263882790666241 15  44  7
1231453023109121    35  45  3
31525197391593473   7   52  3
180143985094819841  5   55  6
1945555039024054273 27  56  5
4179340454199820289 29  57  3
```

## 拉格朗日插值公式

$$
f(x) = \sum_{i = 1}^n y_i \prod_{j \ne i} \frac{x - x_j}{x_i - x_j}
$$



## 一些求方案数的问题

1. 将n个**相同**的小球放入m个**相同**的盒子，盒子**不能**为空：
   - 理解1：$f(n, m) = f(n - 1, m - 1) + f(n - m, m)$, 有一个位置只有一个球的方案 + 先所有位置都预先放一个球的方案
   - 理解2：本质就是背包问题，用m个数组成的和为n的方案数，$dp(n, m) = \sum_{i=1}^ndp(n - i, m - 1)$
2. 

## 概率 

1. 假设有一个整数随机变量X，则有$p(X = k) = p(X \ge k) - p(X > k)$

## 期望

1. 期望的线性：$E(X + Y) = E(X) + E(Y)$
2. 同理，如果$X_1 + X_2 = X$，则$E(X) = E(X_1) + E(X_2)$
3. 假如X是一个随机正整数变量，则$E(X) = \sum_{i = 1}^{\infty}{P(X \ge i)}$


