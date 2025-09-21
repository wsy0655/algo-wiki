## T1

方法一：循环枚举

由于 $a\leq 100$，因此 $x$ 必然不会太大。因此枚举这个 $x$ 即可。


```cpp
for (int x = 0; ; x++)
{
    int y = 10 * sqrt(x);
    if (y >= a)
    {
        cout << x;
        break;
    }
}
```


方法二：数学算法

$$\begin{aligned}
&\left \lfloor 10 \sqrt x \right  \rfloor \geq a \\
\Rightarrow &(\left \lfloor 10 \sqrt x \right  \rfloor)^2 \geq a^2\\
\Rightarrow & \left \lfloor 100 \cdot x \right  \rfloor\geq a^2\\
\Rightarrow & 100 \cdot x  \geq a^2\\
\Rightarrow & x \geq \frac{a^2}{100}
\end{aligned}$$


由于 $\dfrac{a^2}{100}$ 可能为小数，因此 $x$ 至少取 $\lceil \dfrac{a^2}{100} \rceil$。


使用 `ceil` 函数即可。注意确保值为 `double` 类型后在用 `ceil` 向上取整。


___



## T2


基础知识

`int` 类型的范围为 $[−2^{31},2^{31}−1]$，也就是 $[−2147483648,2147483647]$。一般认为 `int` 类型可以存储约 $[-2\times 10^9,2\times 10^9]$ 大小的值。

`long long` 类型的范围为 $[−2^{63},2^{63}−1]$，也就是 $[−9223372036854775808,9223372036854775807]$。一般认为 `long long` 类型可以存储约 $[-9\times 10^{18},9\times 10^{18}]$ 大小的值。

解法分析

在 `int` 乘 `int` 时，超出 `int` 类型是非常容易的，也是在 OI 中常见的失分点，但 `int` 乘 `int` 一定不会超过 `long long` 类型可以表示的范围。


由于 $0 \le x_l \le x_u < 2^{31}$，$0 \le y_l \le y_u < 2^{31}$。

因此有 $x_l \times y_l \le x\times y\le x_u \times y_u$。

因此我们可以计算 $x_u\times y_u$，将结果与 `INT_MAX` 进行比较即可。


```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int xl, xu, yl, yu;
    cin >> xl >> xu >> yl >> yu;
    if (1ll * xu * yu <= INT_MAX)
        cout << "int";
    else
        cout << "long long int";
    return 0;
}
```


___


## T3


本题考察对循环结构的运用。

可以考虑到，一个最简单的做法是，需要枚举 $1\sim N^N$ 中的每个数，并对其做第二和第三个判断。在判断后统计个数即可。

- 判断 $(x\bmod K)$ 是 $N$ 的倍数：`(x % k) % n == 0`。
- 判断 $x$ 的个位是 $N$：`x % 10 == n`。

为了防止重复的运算，我们可以先将 $N^N$ 计算出来。之后，按照该思路编写的核心代码如下：


```cpp
int n, k;
cin >> n >> k;
int nn = 1; // nn 代表 n 的 n 次方
for (int i = 1; i <= n; ++i) {
	nn *= n;
}
int cnt = 0; // 记录符合条件的 x 的数量
for (int x = 1; x <= nn; x++) {
	if (x % 10 == n && (x % k) % n == 0) {
		++cnt;
	}
}
cout << cnt << endl;
```


___

然而当 $N=9$ 时，需要循环 $9^9 \approx 3.8\times 10^8$ 容易 TLE。


此时可以注意到第三条判断，要求每个 $x$ 的个位数都是 $N$。

可以注意到，个位数是 $N$ 的正整数是严格地每 $10$ 个出现一次的。因此，我们可以将循环的初始值改为 $N$，步长改为 $10$，直接跳过所有的个位数不是 $N$ 的情况。


```cpp
for (int x = n; x <= nn; x += 10) {
	if ((x % k) % n == 0) {
		++cnt;
	}
}
```

此时循环次数降低到 $\dfrac{9^9}{10} \approx 3.8 \times 10^7$ 次可以通过。


___


## T4


本题考察对循环结构的运用和对题目信息的掌握。

通过题目对异或的定义，我们会发现，如果一个非负整数和自身异或，那么二者的每一位都是相同的，二者最终得到的答案会是 $0000⋯000=0$。

- 异或性质一：$a\oplus a=0$。
- 异或性质二：$a\oplus 0=a$。

所以可以发现，$(a_1\oplus a_2\oplus a_3\oplus \cdots \oplus a_n)\oplus (a_1\oplus a_2\oplus a_3\oplus \cdots \oplus a_n)=0$。

而显然，对于两个非负整数，一般不可以通过异或得到负数结果，因此 $0$ 是最优的结果。

因此，当 $x$ 取 $(a_1\oplus a_2\oplus a_3\oplus \cdots \oplus a_n)$ 时，答案最优为 $0$。输出 $(a_1\oplus a_2\oplus a_3\oplus \cdots \oplus a_n)$ 和 $0$ 即可。

代码实现上，只需要初始时令 $x=0$，读入 $a_i$ 的过程中让 

$$
x\leftarrow x\oplus a_i
$$ 

注意数据范围需要使用 `long long`。


```cpp
int n;
cin >> n;
long long x = 0;
for (int i = 1; i <= n; i++) 
{
	long long a;
	cin >> a;
	x = x ^ a;
}
```


___


## T5

本题考察对二维循环结构的运用。

对于「这个最大值之前有多少个非 $0$ 值比它小」，略微琢磨便可发现，在最大值唯一的情况下，「比最大值小」是一定成立的。因此，题目的第二个任务可以转化为「求这个最大值之前有多少个非 $0$ 值」。

对于第一个任务，可以使用「擂台法」轻松解决。擂台记录当前最大值和最大值的位置。读入元素时和最大值比较，如果更大则更新擂台。


```cpp
for (int i = 1; i <= n; ++i) 
{
    int max_val = -1, max_pos = -1;
    for (int j = 1; j <= m; ++j) 
    {
        int x;
        cin >> x;
        if (x > max_val) 
        {
            max_val = x;
            max_pos = j;
        }
    }
}
```


对于第二个任务，可以考虑到，我们在读入元素的同时计数非 $0$ 元素个数即可。在擂台中额外记录此时的非 $0$ 元素个数，在「擂台法」的同时更新即可。


```cpp
for (int i = 1; i <= n; ++i) 
{
    int max_val = -1, max_pos = -1, max_cnt = -1;
    int cnt = 0; 
    for (int j = 1; j <= m; ++j) 
    {
        int x;
        cin >> x;
        if (x != 0) 
            ++cnt;
        if (x > max_val) 
        {
            max_val = x;
            max_pos = j;
            max_cnt = cnt; 
        }
    }
}
```

最后输出时，需要注意最大值自身是否有被统计到「个数」中。


___


## T6

一道暴力枚举题。

暴力枚举 $A$ 和 $B$ 的所有可能数字，设 $f_i$ 表示数字 $i$ 所用火柴数，若 $f_A+f_B+f_{A+B}=n-4$，答案加一，这里减 $4$ 是减去符号所用火柴。


求某个数字使用的火柴数量可以借助数位拆分。

```cpp
int a[]={6, 2, 5, 5, 4, 5, 6, 3, 7, 6};
// a[i] 表示数字 i 用多少根火柴
int f(int x)
{
    int res = 0;
    while(x)
    {
        res += a[x % 10];
        x /= 10;
    }
    return res;
}
```

接下来就考虑枚举 $A,B$ 即可，由于 $n\leq 24$，因此 $A$ 不会太大，事实上 $A$ 枚举到 $1000$ 就可以了。

```cpp
cin >> n;
for (int i = 0; i <= 1000; ++i)
    for (int j = 0; j <= 1000; ++j)
        if (f(i) + f(j) + f(i + j) == n - 4)
            ans++;
```


___



## T7

**✅ 问题描述**

给定一个一元 $n$ 次多项式：

$$
f(x) = a_n x^n + a_{n-1} x^{n-1} + \cdots + a_1 x + a_0\quad (a_n \ne 0)
$$

按照如下格式要求输出：

- 从高次到低次输出（降幂排列）
- 系数为 0 的项不输出
- 第一项如果系数为正不加 `+`，为负加 `-`
- 其他项：
    - 系数为正加 `+`，为负加 `-`
    - 系数为 $\pm1$ 且指数 $>0$ 时不输出 `1`
- 指数：
    - 若为 $0$，只输出常数项（不带 $x$）
    - 若为 $1$，输出 `x`
    - 若为 $k\ge2$，输出 `x^k`



**✅ 实现要点**

1. 用 `i == n` 判断首项：
      - 如果是首项，则根据符号决定是否输出 `-`，但不输出 `+`
      - 如果不是首项，则根据系数符号输出 `+` 或 `-`
2. 对于每一项：
      - 如果系数为 $\pm1$ 且指数 $>0$，不输出 `1`
      - 系数为 0 的项直接跳过
3. 根据当前项的指数决定变量格式：
      - $i = 0$：只输出系数
      - $i = 1$：输出 `x`
      - $i \ge 2$：输出 `x^i`



```cpp
int main()
{
    int n;
    cin >> n;
    int a[105];
    for (int i = n; i >= 0; --i)
        cin >> a[i];
    for (int i = n; i >= 0; --i)
    {
        if (a[i] == 0)
            continue; // 系数为 0 直接跳过
        // 1. 处理第一项（首项）
        if (i == n)
        {
            if (a[i] < 0)
                cout << "-";
            if (abs(a[i]) != 1 || i == 0)
                cout << abs(a[i]);
            if (i >= 2)
                cout << "x^" << i;
            else if (i == 1)
                cout << "x";
        }
        else
        {
            // 2. 非首项
            if (a[i] > 0)
                cout << "+";
            else
                cout << "-";
            if (abs(a[i]) != 1 || i == 0)
                cout << abs(a[i]);
            if (i >= 2)
                cout << "x^" << i;
            else if (i == 1)
                cout << "x";
        }
    }
    return 0;
}
```