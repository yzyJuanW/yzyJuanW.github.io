# 一些有用的数学公式与结论


# 数学公式小总结

## 质数

1. 算术基本定理：$N = {P_1}^{k_1} \times{P_2}^{k_2} \times {P_3}^{k_3} \cdots \times {P_n}^{k_n} \ \ , \ \ P_1 < P_2 < P_3 < \cdots < P_n$
2. 一个数N约数个数：$(k_1 + 1) \times (k_2 + 1) \times (k_3 + 1) \times \cdots \times (k_n + 1)$，k是算术基本定理中的k
3. 一个数N的所有正因数的和：$({P_1} ^ {0} + {P_1} ^ {1} + {P_1} ^ {2} \cdots + {P_1} ^ {k_1})\times ({P_2} ^ {0} + {P_2} ^ {1} + {P_2} ^ {2} \cdots + {P_2} ^ {k_2}) \times \cdots \times ({P_n} ^ {0} + {P_n} ^ {1} + {P_n} ^ {2} \cdots + {P_n} ^ {k_n})$
4. 费马小定理：$a^{p - 1} \equiv 1 \pmod p$，p是素数

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

$$
a^b \equiv
\begin{cases}
a^{b \bmod \phi (m)} & gcd(a, m) = 1 \\ 
a^{b} & gcd(a, m) \ne 1 , b< \phi (m) \\ 
a^{b \bmod \phi (m) + \phi (m)} & gcd(a, m) \ne 1 , b \geqslant \phi (m)
\end{cases}
\pmod m
$$

$$
a^b \equiv
\begin{cases}
a^{b \bmod \phi (m)} & gcd(a, m) = 1
\end{cases}
\pmod m
$$

$$
a^b \equiv
\begin{cases}
a^{b} & gcd(a, m) \ne 1 , b< \phi (m) \\ 
\end{cases}
\pmod m
$$


$$
a^b \equiv
\begin{cases}
a^{b \bmod \phi (m) + \phi (m)} gcd(a, m) \ne 1 , b \geqslant \phi (m)
\end{cases}
\pmod m
$$

$$
a^{b \bmod \phi (m)}  \ gcd(a, m) = 1  \pmod m
$$

$$
\begin{aligned}
&a^b \equiv a^{b \bmod \phi (p)} & \text{when} && gcd(a, p) = 1
\end{aligned}
$$

$$
\begin{aligned}
& a^b \equiv a^b & gcd(a, p) \ne 1 , b < \phi(p)
\end{aligned}
$$

$$
\begin{aligned}
& a^b \equiv a^{b \bmod \phi (p) + \phi (p)} & & gcd(a, p) \ne 1, b \ge \phi(p)
\end{aligned}
$$



## 概率 

1. 假设有一个整数随机变量X，则有$p(X = k) = p(X \ge k) - p(X > k)$

## 期望

1. 期望的线性：$E(X + Y) = E(X) + E(Y)$
2. 同理，如果$X_1 + X_2 = X$，则$E(X) = E(X_1) + E(X_2)$
3. 假如X是一个随机正整数变量，则$E(X) = \sum_{i = 1}^{\infty}{P(X \ge i)}$


