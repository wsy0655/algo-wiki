## T1

本题给出的信息较多，将其一步一步分解。

我们先考虑 $b$ 名学生吃一餐的所需，分别是 $b×R$ 克米饭，$b×V$ 克蔬菜，$b×M$ 克肉；

接着，我们再考虑 $a$ 名老师吃一餐的所需，分别是 $a×2×R$ 克米饭，$a×3×V$ 克蔬菜，$a×3×M$ 克肉。

老师要吃两餐，上面的数值都需要乘以 $2$。即，为了满足 $a$ 名老师的需要，需要准备 $2×a×2×R$ 克米饭，$2×a×3×V$ 克蔬菜，$2×a×3×M$ 克肉。

将学生和老师需要的相加，即可知道最后的答案为：

- 米饭需要：$b×R+4×a×R$ 克；
- 蔬菜需要：$b×V+6×a×V$ 克；
- 肉需要：$b×M+6×a×M$ 克；


$$
\begin{array}{ll}
1  & \textbf{input } b, a, R, V, M \\
2  & rice_s \gets b \times R \\
3  & veg_s  \gets b \times V \\
4  & meat_s \gets b \times M \\
5  & rice_t \gets 2a \times (2R) \\
6  & veg_t  \gets 2a \times (3V) \\
7  & meat_t \gets 2a \times (3M) \\
8  & rice_{total} \gets rice_s + rice_t \\
9  & veg_{total}  \gets veg_s + veg_t \\
10 & meat_{total} \gets meat_s + meat_t \\
11 & \textbf{print } rice_{total}, veg_{total}, meat_{total} \\
\end{array}
$$


___


## T2


本题要求读入 $8$ 个气象观测站的风力监测数据。这是一项重复劳动，因此适合使用循环结构进行完成。

接下来的关键在于，如何根据这些条件，简单地去判断应当发布什么警告信号。这里给出一种做法。定义如下变量：

- `cnt41` 表示，风力大于等于 $41$ 千米/小时的气象站个数；
- `cnt63` 表示，风力大于等于 $63$ 千米/小时的气象站个数；
- `cnt118` 表示，风力大于等于 $118$ 千米/小时的气象站个数；




接下来我们可以根据这三个变量得到对应的结果分别进行判断：

- 有 $1$ 个气象站的持续风力达到或超过 $118$ 千米/小时，即 `cnt118 >= 1` 时，为 $10$ 号飓风信号；
- 有 $4$ 个气象站的持续风力达到或超过 $63$ 千米/小时，即 `cnt63 >= 4` 时，为 $8$ 号烈风信号；
- 有 $4$ 个气象站的持续风力达到或超过 $41$ 千米/小时，即 `cnt41 >= 4` 时，为 $3$ 号强风信号；
- 其他情况则是 $1$ 号戒备信号；

需要注意，由于多个条件可能是同时满足的，例如可能同时出现 $1$ 个气象站持续风力达到或超过 $118$ 千米/小时，而且其他气象站均观测到了超过 $63$ 千米/小时的风力的情况。这个时候需要输出符合要求的信号中，等级最高者。因此，需要使用 `if - else` 的分支结构，从最高等级的信号往较低等级的信号进行判断。




$$
\begin{array}{ll}
1  & cnt41 \gets 0,\quad cnt63 \gets 0,\quad cnt118 \gets 0 \\
2  & \textbf{for } i \gets 1 \textbf{ to } 8 \\
3  & \quad x \gets \textbf{input} \\
4  & \quad \textbf{if } x \ge 41\\
5 & \quad \quad cnt41 \gets cnt41 + 1 \\
6  & \quad \textbf{if } x \ge 63\\
7 & \quad \quad cnt63 \gets cnt63 + 1 \\
8  & \quad \textbf{if } x \ge 118\\
9 & \quad \quad cnt118 \gets cnt118 + 1 \\
10  & \textbf{if } cnt118 \ge 1: \textbf{print } 10 \\
11  & \textbf{else if } cnt63  \ge 4: \textbf{print } 8  \\
12 & \textbf{else if } cnt41  \ge 4: \textbf{print } 3  \\
13 & \textbf{else}:        \textbf{print } 1  \\
\end{array}
$$



___


## T3


知识点：循环，数位拆分

$$
\begin{array}{ll}
1  & \textbf{function } f(x) \\
2  & \quad count \gets 0 \\
3  & \quad \textbf{while } x > 0: \\
4  & \quad\quad count \gets count + 1; \\
5  & \quad\quad x \gets x / 10 \\
5  & \quad \textbf{return } count \\
6  & n   \gets \textbf{input} \\
7  & ans \gets 0 \\
8  & \textbf{while } n > 0: \\
9  & \quad ans \gets ans + f(n) \\
10 & \quad n   \gets n / 2 \\
11 & \textbf{print } ans \\
\end{array}
$$


___


## T4

知识点：枚举

这是一个区间枚举问题，题目的大意即为是否存在一个长度为 $k$ 的区间，使得区间的净流入量大于等于 $m$。

由于数据范围 $n\leq 5000$，因此可以直接枚举所有的区间来完成这一任务。

枚举的区间为：$[i,i+k-1]$，其中 $i$ 从 $1$ 开始到 $n-k+1$ 结束。


时间复杂度：$O(nk)$。



$$
\begin{array}{ll}
1  & n, k, m \gets \textbf{input} \\
2  & \textbf{for } i \gets 1 \textbf{ to } n: \\
3  & \quad a[i] \gets \textbf{input} \\
4  & \textbf{for } i \gets 1 \textbf{ to } n: \\
5 & \quad b[i] \gets \textbf{input} \\
6  & \textbf{for } i \gets 1 \textbf{ to } n: \\
7  & \quad c[i] \gets a[i] - b[i] \\
8  & \textbf{for } i \gets 1 \textbf{ to } n-k+1: \\
9  & \quad sum \gets 0 \\
10  & \quad \textbf{for } j \gets i \textbf{ to } i+k-1\\
11  & \quad \quad sum \gets sum + c[j] \\
12  & \quad \textbf{if } sum \ge m\\
13 & \quad \quad \textbf{print Yes} \\
14  & \quad \quad \textbf{return 0}\\
15  & \textbf{print No} \\
\end{array}
$$



___


## T5

知识点：判断质数、二维问题处理

本题考察的是如何**高效判断一个数是否为质数**，以及如何**避免重复计算**从而降低整体复杂度。

---

**质数判断函数**

我们需要一个判断质数的函数，时间复杂度为 $O(\sqrt{x})$：

```cpp
bool prime(int x) 
{
    if (x < 2) return false;
    for (int i = 2; i <= sqrt(x); i++) 
        if (x % i == 0) 
            return false;
    return true;
}
```

---

**朴素做法**

枚举每个位置 $(i, j)$，向上判断每个位置是否是质数：

```cpp
int cnt = 0;
for (int k = i; k >= 1; k--) 
{
    if (prime(a[k][j])) cnt++;
    else break;
}
```

这样会导致每个位置最多判断 $O(n)$ 次是否为质数，整体复杂度：

$$
O(n^2 \cdot m \cdot \sqrt{V})
$$

其中 $V=10^5$，数据范围太大，最终会 TLE。

---

**优化做法**

质数的判断有重复：每个格子会被其下方所有格子判断一次是否为质数。我们可以先预处理每个格子是否为质数：

这一步的时间复杂度为：$O(n\cdot m\cdot \sqrt{V})$，不会 TLE。

```cpp
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        if (prime(a[i][j]))
            vis[i][j] = 1;
        else
            vis[i][j] = 0;
```

这样每个格子只判断一次，后续查询直接 $O(1)$。

---

**枚举与统计**

对于每个位置 $(i,j)$，从当前位置向上统计连续的质数个数。

最终复杂度：

$$
O(n\cdot m\cdot \sqrt{V} + n^2 \cdot m)
$$

足以通过所有测试点。

---

**伪代码实现**

$$
\begin{array}{ll}
1  & \textbf{for } i \gets 1 \textbf{ to } n \\
2  & \quad \textbf{for } j \gets 1 \textbf{ to } m \\
3  & \quad \quad vis[i][j] \gets \texttt{prime(a[i][j])} \\
4  & ans \gets 0 \\
5  & \textbf{for } i \gets 1 \textbf{ to } n \\
6  & \quad \textbf{for } j \gets 1 \textbf{ to } m \\
7  & \quad\quad cnt \gets 0, k \gets i \\
8  & \quad\quad \textbf{while } k \ge 1 \textbf{ and } vis[k][j]: \\
9  & \quad\quad\quad cnt \gets cnt + 1; k \gets k - 1 \\
10 & \quad\quad \texttt{print cnt} \\
\end{array}
$$


> 学习质数筛法和递推可以使得本题复杂度降低为 $O(V+n\cdot m)$。

___



## T6


本题要求判断字符串 `s` 的哪些 **后缀 + 前缀** 组合可以构成字符串 `t`，并统计满足条件的方案数。

---

**关键观察：**

- 目标是找出所有满足 `suf + pre == t` 的组合，其中：
  - `suf` 是 `s` 的后缀，
  - `pre` 是 `s` 的前缀。
- 由于字符串 `t` 很短（$1 \leq |t| \leq 100$），我们可以 **暴力枚举** 前缀长度，构造所有可能的组合，效率是可以接受的。

---

**枚举方案：**

- 设 `n = s.size()`，`m = t.size()`。
- 枚举前缀长度 $i$，即 `i` 从 $1$ 到 $n-1$：
    - 令后缀长度 $j = m - i$，如果 $j < 1$，无法构造合法后缀，直接跳出循环。
    - 构造前缀：`pre = s.substr(0, i)`
    - 同理利用 `substr` 构造后缀：`suf`
    - 判断：若 `suf + pre == t` 成立，则方案数加 `1`


---

## T7


知识点：位运算、二进制理解

本题核心在于**定位并修改整数的某一位二进制位**，使得该位变为 `1`，且增量尽可能小。

---

**思路解析**

* 题目中给出的 $k$ 是从 **第 1 位开始计数**（从右往左），而在位运算中我们通常从第 $0$ 位开始，所以需先调整令 $k\leftarrow k-1$

* 如果当前第 $k$ 位已经是 `1`，即：

  ```cpp
  if ((n >> k) & 1)
  ```

那么无需操作，直接跳过。

---

**如何构造最小的 $x$**

我们希望找到一个最小的正整数 `x`，使得：

```cpp
((n + x) >> k) & 1 == 1
```

即加上 `x` 后，第 $k$ 位变为 `1`。

---

**构造原理**

我们可以通过加法进位机制让第 $k$ 位进位变为 `1`。假设当前 $n$ 的低 $k$ 位为：

$$
0,b_{k-2},b_{k-3},\ldots,b_0
$$

希望变为：

$$
1,0,0,\ldots,0
$$

也就是只保留第 $k$ 位是 `1`，低位全为 `0`。这个数的十进制就是 $2^k$。

所以需要的增量为：

$$
x = 2^k - \text{(n 的低 }k\text{ 位)}
$$

因此问题的关键在于计算 $n$ 的低 $k$ 位。

---

**如何提取 `n` 的低 $k$ 位？**

使用位运算中常见的技巧：

```cpp
v = (1ll << k) - 1;
int res = n & v;
```

`(1ll << k) - 1` 在二进制中恰好是 $k$ 个连续的 `1`，与 `n` 进行按位与操作，可以保留 `n` 的低 $k$ 位。

最终的计算式为：

```cpp
x = (1ll << k) - (n & v);
```

将 `x` 加到 `n` 上即可满足题目要求。


如对获取低 $k$ 位有疑问，建议自己画图理解。主要是理解与运算的逻辑（都为 $1$ 才为 $1$，因此 $2^k-1$ 这个数字恰好提供 $k$ 个 $1$ 可以把 $n$ 的低 $k$ 位保留下来）


___