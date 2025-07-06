

## 基本概念

- **GCD (Greatest Common Divisor)**: 最大公约数。
    - 含义：两个数 $a$ 和 $b$，求出能同时被 $a$ 和 $b$ 整除的最大数字
- **LCM (Least Common Multiple)**: 最小公倍数
    - 含义：两个数 $a$ 和 $b$，求出同时是 $a$ 和 $b$ 的最小倍数。

规定：$\gcd(a, 0) = \gcd(a, a) = a$

---

## 更相减损术

**基本原理**:

$$
\gcd(a, b) = \gcd(a, b - a) \quad (a \leq b)
$$

### 证明思路

- 设 $\gcd(a, b) = d$，则 $a = m\cdot d$, $b = n\cdot d$，其中 $\gcd(m, n) = 1$

则:

$$
\gcd(a, b - a) = \gcd(m\cdot d, n\cdot d - m\cdot d) = \gcd(m, n - m) \times d
$$



- 反证 $\gcd(m, n - m) = 1$：
     - 假设 $\gcd(m, n - m) = p > 1$
     - 则 $m = k_1\cdot  p$，$n - m = k_2\cdot  p$，$n = (k_1 + k_2)\cdot p$
     - 则 $\gcd(m, n) \geq p$，与 $\gcd(m, n) = 1$ 矛盾

**适用于多个数字：**

$$
\gcd(a, b, c, d, \dots) = \gcd(a, b-a, c-b, d-c, \dots)
$$

---

## 辗转相除法（欧几里得算法）

$$
\gcd(a, b) = \gcd(b, a \bmod b)
$$

**理解**

更相减损术是一次减去 $b$，而 $a \bmod b$ 是相当于多次减 $b$ 直到余数。

**代码实现**

```cpp
int gcd(int a, int b) 
{
    if (b == 0) return a;
    return gcd(b, a % b);
}
```

- **时间复杂度**: $O(\log \max(a,b))$
    - 可以证明 $a\bmod b\leq \lfloor\frac{a}{2}\rfloor$ 当且仅当 $b\leq a$。
- **C++ 自带函数**: `__gcd(a, b)`

---

## 最小公倍数 LCM

利用 GCD 进行计算：

$$
lcm(a, b) = \frac{a \times b}{\gcd(a, b)}
$$

```cpp
int lcm(int a, int b) 
{
    return a / gcd(a, b) * b;
}
```

---

## 多个数字的 GCD 和 LCM

**GCD**

```cpp
int d = 0;
for (int i = 1; i <= n; i++) d = gcd(d, a[i]);
```

**LCM**

```cpp
int m = 1;
for (int i = 1; i <= n; i++) m = lcm(m, a[i]);
```

* **初始化值**：

  * GCD 为 $0$，因为 \$\gcd(a, 0) = a\$
  * LCM 为 $1$，因为 \$\text{LCM}(1, a) = a\$

---


## 应用


- 给定一个序列 $a_1, a_2, \dots, a_n$，多组询问求 $\gcd(a_1+x,a_2+x,\ldots,a_n+x)$。


利用更相减损术可得：


$$\begin{aligned}
&\gcd(a_1+x,a_2+x,\ldots,a_n+x)\\
=& \gcd(a_1+x,a_2-a_1,a_3-a_2,\ldots,a_n-a_{n-1})
\end{aligned}$$


由于 $\gcd$ 具有结合律，因此可以预处理 $d=\gcd(a_2-a_1,a_3-a_2,\ldots,a_n-a_{n-1})$。


这样每次询问就直接计算 $\gcd(a_1+x,d)$ 即可。