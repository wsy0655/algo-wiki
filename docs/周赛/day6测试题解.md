## T1

题意简述

给定三个数，判断是否 $A+B=C$，或 $A−B=C$。


需要注意虽然 $a,b,c$ 都在 `int` 范围内，但是 $a$ 与 $b$ 的和/差会超过 `int` 存储的范围，所以应该用 `long long` 存储相关数据。

这是因为 `int` 可以存储的范围大约是 $-2\times 10^9 \times 2\times 10^9$，而本题中 $a,b,c$ 的范围也是 $-2\times 10^9 \times 2\times 10^9$。

那么 $a+b,a-b$ 有可能达到 $4\times 10^9$ 或 $-4\times 10^9$，而 `int` 无法存储。因此需要 `long long`。


___


## T2


根据题意可知 `a[p][q]` 是访问二维数组 $a$ 的第 $p\times m+q+1$ 个元素，而二维数组一共含有 $n\times m$ 个元素。


> 二维数组的行和列的索引均从 $0$ 开始，因此 $a[p][q]$ 实际是第 $p+1$ 行第 $q+1$ 列，因此是二维数组中第 $p\times m+q+1$ 个元素。

因此比较二者大小关系即可。

即当 $p\times m+q+1>n\times m$ 时，$a[p][q]$ 访问越界。反之不越界。

注意 `long long`。


> 这个题还是很有意义的，告诉了大家数组越界的原因分析。有的时候虽然不越界，但是也访问到了一些没有用的位置从而使得结果计算错误。



___

## T3


解析

直接使用 `while` 循环模拟上面的迭代式即可。使用一个变量 `pre` 表示上次迭代后 $x$ 的值。只要本次迭代的结果不等于 `pre` 就一直进行迭代。

```cpp
int pre = -1;
while (pre != x) 
{
    cout << x << '\n';
    pre = x;          // 本次迭代结束后，x 原来的值就变成了上次迭代的结果。
    x = (x + a) / a;
}
```

___



## T4

用数组 `S` 存储下 H 校所有同学的 C 考试成绩，`S[i]` 表示编号为 $i$ 的同学的成绩。

接下来，读取所有的获奖选手的编号 $W_i$，如果 $W_i$ 不满足 $1\leq W_i\leq N$，就直接跳过它。

否则，判断编号为 $W_i$ 的同学的 C 考试成绩 $S[W_i]$ 是否满足 $S[W_i]<200$，如果满足，就让答案增加 $1$。

最后输出答案即可。


____


## T5


直接枚举 $1\sim N$ 的所有整数，然后一一检验显然会 TLE。我们需要寻求一个高效的方法。


由于限定了长度为偶数，且前一半与后一半相同字，因此一旦前一半确定了，后一半也就随之确定了。

例如：$123123$ 可以看作是三位数 $123$ 拼接 $123$ 以后。


因此虽然 $N\leq 10^{12}$，但对半分以后，前一半只会 $\leq 10^6$。


因此枚举前一半，然后得到后一半就可以了。


**具体实现**

可以使用字符串函数 `to_string()`、 `stoll` 将数字转换为字符串，最后转换为数字。


时间复杂度：$O(\sqrt{N}\log{N})$。

```cpp
for (int i = 1; ; i++)
{
    string s = to_string(i); // 至少开启 C++11 方可使用
    long long x = stoll(s + s);
    if (x <= n)
        ans++;
    else
        break;
}
```

也可以用数位拆分求出 $i$ 的位数，记作 $\texttt{len}$，则新数字为：


$$
x=i\times 10^{\texttt{len}}+i
$$

例如 $123123=123\times 10^3+123$。


```cpp
for (int i = 1; ; i++)
{
    int len = 0;
    int j = i;
    while (j)
    {
        len++;
        j /= 10;
    }
    // len 较小，直接 pow 函数即可，不必担心 double 带来的误差。
    long long x = i * pow(10, len) + i;
    if (x <= n)
        ans++;
    else
        break;
}
cout << ans;
```



___


## T6


由于集合数 $\leq 10$，因此二进制枚举所有集合的情况也不过 $2^{10}$ 种情况。

```cpp
for (int mask = 0; mask < (1 << m); mask++)
{

}
```

然后一一检验当前状态 `mask` 对应的状态下的集合是否满足条件。


首先验证状态 `mask` 对应选了哪几个集合。


```cpp
for (int i = 0; i < m; i++)
{
    if (mask >> i & 1)
    {
        
    }
}
```

由于最终要验证 $1\sim n$ 的每一个数字是否出现，因此需要一个数组打标记。

此时选择了集合 $i$ 以后，遍历这个集合的所有元素打标记即可。


```cpp
for (int i = 0; i < m; i++)
{
    if (mask >> i & 1)
    {
        for (int j = 0; j < c[i]; j++)
        {
            vis[a[i][j]] = 1;
        }
    }
}
```

最后检验 $1\sim n$ 的每个数字是否被标记。


```cpp
bool ok = 1;
for (int i = 1; i <= n; i++)
    if (!vis[i])
        ok = 0;
if (ok) ans++;
```

具体实现还要注意哪些变量，哪些数组在什么位置重置。



```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 10 + 5;
int n, m, c[N], a[N][N];
bool vis[N];
int main()
{
    cin >> n >> m;
    for (int i = 0; i < m; i++)
    {
        cin >> c[i];
        for (int j = 0; j < c[i]; j++)
        {
            cin >> a[i][j];
        }
    }
    int ans = 0;
    for (int mask = 0; mask < (1 << m); mask++)
    {
        // 重置标记数组
        for (int i = 1; i <= n; i++) vis[i] = 0;
        // 检验选了哪些集合，并遍历集合的元素打上标记
        for (int i = 0; i < m; i++)
        {
            if (mask >> i & 1)
            {
                for (int j = 0; j < c[i]; j++)
                {
                    vis[a[i][j]] = 1;
                }
            }
        }
        // 检验是否 1 到 n 都被标记
        bool ok = 1;
        for (int i = 1; i <= n; i++)
            if (!vis[i])
                ok = 0;
        if (ok) ans++;
    }
    cout << ans;
    return 0;
}
```


___


## T7


类似于吃奶酪一题，全排列所有的城镇顺序，求出总移动距离最后除以 $n!$ 即可。

注意避免误差过大，至少保留 $6$ 位小数以后输出会好一些。

___
