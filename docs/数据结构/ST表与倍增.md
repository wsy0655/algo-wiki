## 引入

### 二进制拆分

众所周知，任何正整数都可以表示为若干个不同的 $2$ 的非负整数次幂之和。

- $5 = 1 + 4 = 2^0 + 2^2$
- $7 = 1 + 2 + 4 = 2^0 + 2^1 + 2^2$
- $20 = 4 + 16 = 2^2 + 2^4$

这就是 **二进制拆分**，其拆分个数为 $O(\log n)$，是高效算法设计的重要工具。

### 倍增的动机

某些问题的解可以由多个 $2^k$ 长度的子问题合并而来。

**例子：** 求 $a_1 \sim a_7$ 的区间最大值，可以拆分为：

$$
\text{max}(a_1\sim a_1),\quad \text{max}(a_2\sim a_3),\quad \text{max}(a_4\sim a_7)
$$

这些区间的长度分别为 $2^0$、$2^1$、$2^2$。

> 如何快速预处理所有长度为 $2^k$ 的区间最值？
>
> 倍增法。

## ST 表

ST 表（Sparse Table，稀疏表）是一种用于高效处理 **可重复贡献问题** 的数据结构。


**可重复贡献问题：**

如果某运算 $\operatorname{opt}$ 满足：

$$
x \operatorname{opt} x = x
$$

则它在区间查询中不会因为重复计算而出错，这类问题称为 **可重复贡献问题**。

例子：

- $\max(x, x) = x$ → 区间最大值（RMQ）可重复贡献
- $\min(x, x) = x$ → 区间最小值（RMQ）可重复贡献
- $\gcd(x, x) = x$ → 区间 GCD 可重复贡献
- $\sum(x, x) = 2x$ → 不可重复贡献（会重复加）

此外，$\operatorname{opt}$ 还必须满足 **结合律** 才能使用 ST 表。


### RMQ 问题

RMQ（Range Maximum/Minimum Query）表示区间最大/最小值查询问题。

**RMQ 是一个典型的可重复贡献问题**

例子：查询区间 $[1, 8]$ 的最大值，可以通过如下两个子区间合并而得：

$$
\max([1, 7],\ [2, 8])
$$

二者重叠，但不影响最终答案。

### ST 表的基本性质

ST 表用于在静态数组上高效回答区间最大值（最小值、GCD 等）查询，特点如下：

- 预处理复杂度：$O(n \log n)$
- 查询复杂度：$O(1)$
- 限制：不支持在线修改（仅支持静态数据）
- 核心思想：基于倍增 + 动态规划


### ST 表的实现


**状态设计：** $f_{i,j}$ 表示区间 $[i,\ i + 2^j - 1]$ 的最大值。

例如：

- $f_{i,0} = \max(a_i) = a_i$
- $f_{i,1} = \max(a_i, a_{i+1})$
- $f_{i,2} = \max(a_i, a_{i+1}, a_{i+2}, a_{i+3})$
- $f_{i,3} = \max(a_i \sim a_{i+7})$


**状态转移：** 

$$
f_{i,j} = \max(f_{i,j-1},\ f_{i + 2^{j-1},\ j-1})
$$

> 将长度为 $2^j$ 的区间 $[i,\ i + 2^j - 1]$ 拆分成两个长度为 $2^{j-1}$ 的子区间：
>
> 左半段：$[i,\ i + 2^{j-1} - 1]$
>
> 右半段：$[i + 2^{j-1},\ i + 2^j - 1]$


![](https://oi-wiki.org/ds/images/st-preprocess-lift.svg)


**代码示例：**

```cpp
for (int i = 1; i <= n; i++) f[i][0] = a[i];
for (int j = 1; (1 << j) <= n; j++)
{
    for (int i = 1; i + (1 << j) - 1 <= n; i++)
    {
        f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
    }
}
```

**区间查询**

对于每个查询区间 $[l,r]$，将其拆分为两个长度为 $2^s$ 的区间：

$$
[l,\, l+2^s-1] \quad \text{和} \quad [r-2^s+1,\, r]
$$

其中 $s= \left\lfloor \log_2(r - l + 1) \right\rfloor$。

> 换句话来说，$s$ 是满足最大的整数使得 $l+2^s-1\leq r$。


两个区间的最大值的较大者即为 $[l,r]$ 的最大值。即

$$
\max(a_l,\ldots,a_r)=\max(f[l][s],\ f[r-2^s+1][s])
$$

![](https://oi-wiki.org/ds/images/st-query.svg)


#### 如何求 $s = \lfloor \log_2(r-l+1) \rfloor$

有两种常用方法：

- 使用数学库函数 `log2()`，但存在一定的计算开销。且返回值为 `double` 注意误差。
- 预处理一个数组 `Log2[i]`，查询时 $O(1)$ 复杂度。


**预处理 `Log2[i]` 数组的递推推导**


对于整数 $i$，定义数组 $\texttt{Log2}$：

$$
\text{Log2}[i] = \left\lfloor \log_2 i \right\rfloor
$$

即 $i$ 的以 $2$ 为底的对数的整数部分。

**递推公式**

$$\begin{cases}
\text{Log2}[1] = 0
\\
\text{Log2}[i] = \text{Log2}[\lfloor \frac{i}{2} \rfloor] + 1, \quad i > 1
\end{cases}$$


> 递推推导说明
>
> 对数性质：$\log_2 i = \log_2 \left(2 \times \frac{i}{2}\right) = 1 + \log_2 \left(\frac{i}{2}\right)$


**代码示例**

```cpp
int query(int l, int r)
{
    int len = Log[r - l + 1];
    return max(f[l][len], f[r - (1 << len) + 1][len]);
}
Log2[1] = 0;
for (int i = 2; i <= n; i++) Log2[i] = Log2[i >> 1] + 1;
```


#### 完整实现


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 1e5 + 5;
int n, m, f[N][17], Log[N];
int query(int l, int r)
{
    int len = Log[r - l + 1];
    return max(f[l][len], f[r - (1 << len) + 1][len]);
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) 
    {
        cin >> f[i][0];
        if (i > 1) Log[i] = Log[i >> 1] + 1;
    }
    for (int j = 1; (1 << j) <= n; j++)
    {
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        {
            f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
        }
    }
    while (m--)
    {
        int l, r;
        cin >> l >> r;
        cout << query(l, r) << "\n";
    }
    return 0;
}
```

## ST 表维护其他信息

除 RMQ 以外，还有其它的「可重复贡献问题」。例如「区间按位与」、「区间按位或」、「区间 GCD」，ST 表都能高效地解决。


如果分析一下，「可重复贡献问题」一般都带有某种类似 RMQ 的成分。例如「区间按位与」就是每一位取最小值，而「区间 GCD」则是每一个质因数的指数取最小值。

## 总结

ST 表能较好的维护「可重复贡献」的区间信息（同时也应满足结合律），时间复杂度较低，代码量相对其他算法很小。但是，ST 表能维护的信息非常有限，不能较好地扩展，并且不支持修改操作。


## 练习

### 习题：区间 $\gcd$ 查询

ST 表可用于解决区间 $\gcd$ 查询问题，因为 $\gcd(x,x) = x$ 且满足结合律：

$$
\gcd(x,y,z) = \gcd(x, \gcd(y,z)).
$$

这是典型的可重复贡献问题。

```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 1e5 + 5;
int n, m, f[N][17], Log[N];
int query(int l, int r)
{
    if (l > r) swap(l, r);
    int len = Log[r - l + 1];
    return __gcd(f[l][len], f[r - (1 << len) + 1][len]);
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) 
    {
        cin >> f[i][0];
        if (i > 1) Log[i] = Log[i >> 1] + 1;
    }
    for (int j = 1; (1 << j) <= n; j++)
    {
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        {
            f[i][j] = __gcd(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
        }
    }
    while (m--)
    {
        int l, r;
        cin >> l >> r;
        cout << query(l, r) << "\n";
    }
    return 0;
}
```


## 基于序列的倍增

接下来主要讲解倍增的另一种应用：跳跃式倍增。

- 应用代表：**倍增 LCA**、**倍增走路/跳跃**、**DP 优化**
- 特点：通过 $f[i][k]$ 预处理「从 $i$ 开始跳 $2^k$ 步」的结果
- 典型场景：求第 $k$ 个祖先、倍增走路、区间覆盖

### 例题一：洗牌问题

**题意：** 给定 $n$ 张牌（编号 $1\sim n$），还有一个长度为 $n$ 的洗牌置换数组 $f$。

一次洗牌：把第 $i$ 张牌移动到位置 $f_i$，保证 $f$ 是 $1\sim n$ 的一个排列。

初始排列为 $[1, 2, 3, \dots, n]$。给定 $k$，问洗 $k$ 次后的排列。

- $1\leq n\leq 10^5$，$1\leq k\leq 10^9$

**朴素做法：暴力模拟**

枚举每张牌，从它出发执行 $k$ 次映射。

时间复杂度： $O(n \times k)$，无法通过本题。

```cpp
for (int i = 1; i <= n; i++) 
{
    int now = i;
    for (int t = 0; t < k; t++) 
    {
        now = f[now];
    }
    ans[now] = i;
}
```


**倍增加速**

**关键问题：** 如何快速执行 $k$ 次 **置换**？

**思路：** 将 $k$ 拆分为二进制的若干 $2^j$ 次操作。

例如，若 $k=13=1101_2$，可以分解为：

- 第一次：跳 $2^3=8$ 步
- 第二次：跳 $2^2=4$ 步
- 第三次：跳 $2^0=1$ 步

总共仍是跳 $13$ 步。因此预处理每次洗 $2$ 的幂次，即可快速完成 $k$ 次操作。

**倍增预处理**

- 状态设计：$f[i][j]$ 表示从位置 $i$ 开始，洗 $2^j$ 次后的位置。
    - 初始化：$f[i][0]$ 为初始序列。
- 状态转移：$f[i][j] = f[f[i][j-1]][j-1]$
    - 含义：先跳 $2^{j-1}$ 步到 $f[i][j-1]$，再从那继续跳 $2^{j-1}$ 步。

**具体实现**

- 对每张初始牌（位置 $i$）执行倍增跳跃
- 每次看 $k$ 的二进制中第 $j$ 位是否为 $1$
- 若是，则从当前位置跳 $2^j$ 次
- 时间复杂度： $O(n \log k)$

**代码实现**

```cpp
for (int i = 1; i <= n; i++) 
{
    int now = i;
    for (int j = 30; j >= 0; j--) 
    {
        if (k >> j & 1) now = f[now][j];
    }
    ans[now] = i;
}
```






