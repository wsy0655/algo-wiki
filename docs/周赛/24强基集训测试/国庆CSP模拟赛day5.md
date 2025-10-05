## T1 迅捷打击（quick）

**知识点：最值，循环**

每次攻击都会使所有 Boss 的血量同时减少 $D$。因此，为了让所有 Boss 的血量都不大于 0，**瓶颈只取决于最大血量**。设

\[
\text{mx}=\max(h_1,h_2,\ldots,h_n)
\]

最少攻击次数为

\[
\left\lceil \frac{\text{mx}}{D} \right\rceil 
\]

---

**上下取整转换公式**

当 $a\ge 0, b>0$ 时，

\[
\left\lceil \frac{a}{b} \right\rceil=\left\lfloor \frac{a+b-1}{b} \right\rfloor
\]

**证明（分类讨论）**

* 若 $a\bmod b=0$，左边为 $\frac{a}{b}$，右边为 $\left\lfloor \frac{a}{b}+\frac{b-1}{b} \right\rfloor=\frac{a}{b}$，成立。
* 若 $a\bmod b>0$，左边为 $\lfloor \frac{a}{b}\rfloor+1$。又因 $\lfloor \tfrac{a}{b}\rfloor=\lfloor \tfrac{a-1}{b}\rfloor$，右边
  
  \[
  \left\lfloor \frac{a-1+b}{b}\right\rfloor
  =\left\lfloor \frac{a-1}{b}+1\right\rfloor
  =\lfloor \tfrac{a}{b}\rfloor+1,
  \]

  亦成立。

**实现注意**

* 尽量使用整数式：`(a + b - 1) / b`。
* **避免** 直接用 `ceil(1.0 * a / b)`：会发生浮点舍入误差，极端数据下可能错。
* C++ 的整除向 0 取整：当我们保证 $a\ge 0, b>0$ 时，`a / b` 等价于 $\left\lfloor \frac{a}{b} \right\rfloor$。


____



## T2 前缀串（string）

**知识点：字符串**

判定 “$s$ 是否为 $t$ 的前缀”：可用 `find()` 的**第一次出现位置**是否为 $0$。

```cpp
if (t.find(s) == 0) { /* s 是 t 的前缀 */ }
```

* `t.find(s)` 返回 `s` 在 `t` 中 **首次出现** 的下标；若不存在，返回 `npos`。
* 只有返回值等于 `0` 时，才说明 `s` 在 `t` 的开头，即 `s` 为 `t` 的前缀。

把所有以 $S$ 为前缀的字符串收集到数组 `b` 中，然后 `sort(b + 1, b + cnt + 1)`，`b[1]` 即字典序最小者。


____




## T3 空箱（box）

**知识点：思维，模拟**



**（1）30 分做法：暴力模拟 + 备份数组**

每次询问都在数组备份 `b` 上从头执行 $x$ 次翻转：

* 第 $i$ 次翻转区间 $[i,, n-i+1]$；
* C++ 用 `reverse(first, last)` 实现区间翻转（半开区间 $[first,last)$）。

```cpp
for (int i = 1; i <= n; i++) b[i] = a[i];      // 重置 b
for (int i = 1; i <= x; i++) 
{
    int L = i, R = n - i + 1;
    reverse(b + L, b + R + 1); 
}
cout << b[y] << " ";
```

**复杂度**：一次询问最多做 $x$ 次翻转，单次翻转均摊 $O(n)$，最坏 $O(qn^2)$。

**实现要点**

* **`reverse` 的区间是半开 `[first, last)`**。类似于 `sort` 的用法。

---

**（2）50 分做法：离线按 $x$ 升序处理**

核心思想：**把所有询问按 $x$ 从小到大排序**，进行“增量式”翻转，避免每次从头开始。


离线询问注意最后输出答案时要按照原先询问的顺序输出，因此还要记录询问编号。

照排序后的询问处理，每次都是在上一次的基础上在翻转几次，因此总翻转此时最多 $n$ 次。


这样的话时间复杂度是 $O(qn)$。

```cpp
#include <bits/stdc++.h>
using namespace std;
int a[100005], ans[100005];
struct node
{
    int x, y, i;
} t[100005];
bool cmp(node x, node y)
{
    return x.x < y.x;
}
int main()
{
    freopen("box.in", "r", stdin);
    freopen("box.out", "w", stdout); 
    int n, q;
    cin >> n >> q;
    for (int i = 1; i <= n; i++)
    {   
        cin >> a[i];
    }
    for (int i = 1; i <= q; i++)
    {
        cin >> t[i].x >> t[i].y;
        t[i].i = i;
    }
    sort(t + 1, t + q + 1, cmp);
    int last = 0; 
    // 看着是两个循环，但当 last 循环到 n 时就不会在循环了，整体复杂度：O(q * n)
    for (int i = 1; i <= q; i++)
    {
        for (int j = last + 1; j <= t[i].x; j++) // 在上一次的下一个继续操作
        {
            reverse(a + j, a + n - j + 2);
        }
        last = t[i].x; // 更新一下处理到第几次翻转
        ans[t[i].i] = a[t[i].y];
    }
    for (int i = 1; i <= q; i++)
    {
        cout << ans[i] << " ";
    }
    return 0;
}
```

---

**（3）100 分做法：$O(1)$ 查询**

关键观察：第 $i$ 次翻转仅影响区间 $[i,, n-i+1]$。

一个位置 $y$ 被交换一次就与其对称点 $n-y+1$ 互换；**翻转偶数次回原位，奇数次到对称点**。所以只需要知道位置 $y$ 的**被包含次数的奇偶性**。

对位置 $y$ 与询问次数 $x$ 的关系分三段讨论：

1. **左侧区域** $y < x$：位置 $y$ 会被第 $1\sim y$ 次翻转覆盖，共 **$y$ 次**。
      * 若 $y$ 为奇数，输出 `a[n - y + 1]`；否则输出 `a[y]`。
2. **右侧区域** $y > n - x + 1$：位置 $y$ 会被前 $n-y+1$ 次翻转覆盖，共 **$n-y+1$ 次**。
      * 若 $n-y+1$ 为奇数，输出 `a[n - y + 1]`；否则输出 `a[y]`。

3. **中间区域** $x \le y \le n - x + 1$：位置 $y$ 会被前 $x$ 次翻转覆盖，共 **$x$ 次**。
      * 若 $x$ 为奇数，输出 `a[n - y + 1]`；否则输出 `a[y]`。

**复杂度**：$O(n+q)$。

**实现代码**

```cpp
while (q--)
{
    int x, y;
    cin >> x >> y;
    if (y < x)
    {
        if (y & 1) cout << a[n - y + 1] << " ";
        else cout << a[y] << " ";
    }
    else if (y > n - x + 1)
    {
        if ((n - y + 1) & 1) cout << a[n - y + 1] << " ";
        else cout << a[y] << " ";
    }
    else
    {
        if (x & 1) cout << a[n - y + 1] << " ";
        else cout << a[y] << " ";
    }
}
```



___


## T4 

知识点：排序，贪心。

按照防守难度从高到低排序，翁老师优先将士兵派到防守难度高的地方使得聪聪老师要分配更多的士兵去防守。


注意题目是求当翁老师的数量严格大于聪聪老师的数量后，二者差值的最大值。


其实就是当聪聪老师的士兵数为 $0$ 就可以停止攻击了，再往下攻击，聪聪老师不会派士兵，翁老师继续消耗士兵则会降低二者的差值。


假设当前枚举到第 $i$ 个资源点，记 $\text{num}$ 为应当派出的士兵数，则：

- $n\gets n-\text{num}$
- $m\gets m-\text{num}\times a_i$。

注意 $n,m$ 要和 $0$ 取 $\max$ 防止为负。


**如何求解 $\text{num}$**


首先 $\text{num}\leq b_i$ 即不超过容纳上限。

其次 $\text{num}$ 应当取决于聪聪老师的士兵数，由于翁老师每派一名士兵，聪聪老师需要 $a_i$ 名士兵防守因此：

$$
\text{num}=\min(b_i,\lceil \dfrac{m}{a_i} \rceil)
$$

枚举完所有的资源点后，如果 $n>m$ 则输出 $n-m$，否则输出 `No`。

时间复杂度：$O(k\log k)$。


```cpp
sort(a + 1, a + k + 1, cmp); // a[i].x 表示防守难度，a[i].y 表示容纳士兵的上限
for (int i = 1; i <= k; i++)
{
    int num = min(a[i].y, (m + a[i].x - 1) / a[i].x);
    n = max(0, n - num);
    m = max(0, m - num * a[i].x);
    if (m == 0) break;
}
if (n > m)
    cout << n - m;
else
    cout << "No";
```


___


## T5 序列（seq）

知识点：二分，前缀和，前缀最小值。


**$30$ 分解法**

枚举所有长度大于等于 $k$ 的区间，利用前缀和查询区间和并求出平均值以后更新最大值即可。

时间复杂度：$O(n^2)$。


**$100$ 分解法**

平均值具备单调性，考虑二分答案，即二分平均值 $x$，判定是否存在一个长度大于等于 $k$ 的区间使得区间的平均值大于等于 $x$。

即判断是否存在一个区间 $[l,r]$ 满足：

- $r-l+1\geq k$
- $\dfrac{\sum\limits_{i=l}^r a_i}{r-l+1}\geq x$

对第二个式子变形可得到

$$\begin{aligned}
&\dfrac{\sum\limits_{i=l}^r a_i}{r-l+1}\geq x\\
\Rightarrow& \sum\limits_{i=l}^r a_i\geq x(r-l+1)\\
\Rightarrow& \sum\limits_{i=l}^r a_i-x(r-l+1)\geq 0 \\
\Rightarrow& \sum\limits_{i=l}^r (a_i- x)\geq 0
\end{aligned}$$


定义 $b_i=a_i-x$，则等同于找到一个长度至少为 $k$ 的区间使得区间和 $\geq 0$。

定义 $s_i=b_1+b_2+\cdots+b_i$，则问题等价与求解

$$\begin{cases}
r-(l-1)\geq k\\
s_r-s_{l-1}\geq 0
\end{cases}$$

定义 $l'=l-1$，则问题等价于求解

$$\begin{cases}
r-l'\geq k\\
s_r-s_{l'}\geq 0
\end{cases}$$


考虑枚举 $r$，维护 $s_{l'}$ 的最小值，如果存在一个 $l'$ 使得 $s_r-s_{l'}\geq 0$ 则存在，否则不存在。


**具体实现**

- 本题为实数二分答案，需要设置一个二分精度。
- 答案需要保留若干位小数避免和标准答案误差过大。
- 二分边界：`double l = 1, r = 1e6`。


```cpp
bool check(double x)
{
    for (int i = 1; i <= n; i++)
    {
        b[i] = b[i - 1] + a[i] - x;
    }
    double mnv = 1e9;
    for (int r = k; r <= n; r++)
    {
        mnv = min(mnv, b[r - k]); // 维护前缀最小值  
        if (b[r] - mnv >= 0)
            return true;
    }
    return false;
}
```


____



## T6 计数（count）


知识点：动态规划，动态规划优化


看到划分多少段不难想到 DP 来处理。

定义 $dp_i$ 表示前 $i$ 段划分的方案贡献之和。

转移自然是枚举上一个段的结尾 $j$，其中 $j\in[0,i-1]$，那么有

$$
dp_i=\sum\limits_{j=1}^i dp_{j-1} + \prod\limits_{j=1}^i a_j \cdot g[j-1]\cdot [a_i=a_j]
$$

其中 $g[j-1]$ 表示 $[1,j-1]$ 的划分方案数有多少种。区间 $[1,j-1]$ 的每一种划分方案都可以拼接上区间 $[i,j]$ 的乘积的贡献。


举例：以 $[1,1,2,2,1]$ 为例，当求解 $dp_5$ 时，有 $dp_5=dp_4+1\cdot g[4]$，其中 $g[4]$ 表示 $[1,1,2,2]$ 的划分方案数有 $4$ 种。分别是

- $[1],[1],[2],[2]$
- $[1,1],[2],[2]$
- $[1,1],[2,2]$
- $[1],[1],[2,2]$

这四种划分方案的每一个后面都可以让 $[5,5]$ 这个区间贡献。

___

**区间乘积如何解决？**

- 方法一：我会逆元！直接维护前缀积和前缀积的逆元即可。
- 方法二：注意到值域最多只有 $40$，因此可以维护区间每一个数字的出现次数使用乘法解决。
    - 例如区间 $[1,5]$ 有 $3$ 个 $1$ 和 $2$ 个 $2$，那么区间乘积可以用 $1^3\times 2^2$ 表示。

___

这里用逆元的方法来解决区间乘积。


**$30$ 分代码**

```cpp
g[0] = 1;
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= i; j++)
    {
        if (a[i] == a[j])
        {
            f[i] += f[j - 1] + (pre[i] / pre[j - 1]) * g[j - 1];
            g[i] += g[j - 1];
        }
    }
}
```


____

**转移如何优化？**

定义 $pre[i]=a_1\times a_2\times \cdots \times a_i \bmod p$，定义 $inv[i]=a_1^{-1}\times a_2^{-1}\times \cdots \times a_i^{-1}\bmod p$。

其中 $p=998244353$。

$$\begin{aligned}
f_i&=\sum\limits_{j=1}^{i} (f_j+\text{pre}[i]\cdot \text{inv}[j-1]\cdot g[j-1]) \cdot [a_i=a_j]\\
f_i&=\sum\limits_{j=1}^{i} f_j \cdot [a_i=a_j]+ \text{pre}[i]\cdot \sum\limits_{j=1}^{i} inv[j-1]\cdot g[j-1] \cdot [a_i=a_j]
\end{aligned}$$


注意到转移的本质就是数值相同情况下 DP 值和划分方案数与逆元乘积的前缀和，开两个大小为 $40$ 的桶分别维护即可。


**$100$ 分代码**

```cpp
g[0] = 1;
vector<Z> sumf(41, 0), sumg(41, 0), sum(41, 0);
for (int i = 1; i <= n; i++)
{
    sumf[a[i]] += f[i - 1];
    sumg[a[i]] += g[i - 1];
    // power(pre[i - 1], P - 2) 使用费马小定理用快速幂实现了前缀积的逆元
    sum[a[i]] += power(pre[i - 1], P - 2) * g[i - 1];

    f[i] = sumf[a[i]] + pre[i] * sum[a[i]];
    g[i] = sumg[a[i]];
}
``` 


- $sumf[a[i]]$ 维护数值为 $a[i]$ 的所有 DP 值的和。
- $sumg[a[i]]$ 维护数值为 $a[i]$ 的所有划分方案数 $g$ 之和。
- $sum[a[i]]$ 维护数值为 $a[i]$ 的前缀积的逆元乘以划分方案数的和。


有了这些就可以 $O(1)$ 更新也就是

$$\begin{aligned}
f_i&=\sum\limits_{j=1}^{i} f_j \cdot [a_i=a_j]+ \text{pre}[i]\cdot \sum\limits_{j=1}^{i} inv[j-1]\cdot g[j-1] \cdot [a_i=a_j]\\
&=sumf[a[i]]+pre[i] \cdot sum[a[i]]
\end{aligned}$$






