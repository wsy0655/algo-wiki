## T1

知识点：分支语句，转义字符。


题目的难点主要在于输出，由于 `"` 和 `\` 都是转义字符，所以需要用 `\"` 和 `\\` 来表示。

```cpp
if (a1 != a2)
{
    if (a1 > a2)
    {
        cout << "\"\\n\"";
    }
    else
    {
        cout << "\"\\t\"";
    }
}
else
{
    if (p1 < p2)
    {
        cout << "\"\\n\"";
    }
    else
    {
        cout << "\"\\t\"";
    }
}
```

___

## T2

知识点：数位拆分


- `x % 10` 可以得到 `x` 的个位数字。
- `x / 10` 可以得到 `x` 除以 10 的商，即去掉个位数字后的结果。

一边数位拆分，利用 `if` 语句统计 零 的个数即可。


注意使用 `long long`。


```cpp
while (x)
{
    if (x % 10 == 2) sum++;
    if (x % 10 == 6) sum++;
    if (x % 10 == 8) sum += 2;
    if (x % 10 == 9) sum++;
    if (x % 10 == 0) sum++;
    x /= 10;
}
```

___


## T3

知识点：循环，数学化简。

$n!=n\times (n-1)\times (n-2)\times ...\times 1$

$n!!=n\times (n-2)\times (n-4)\times ...$


因此可以约分，约分后分母为 $1$，分子为 $(n-1)\times (n-3)\times (n-5)\times ...\times 1$


我们只需要用一个循环计算上式，最后乘上 $2$ 即可。


注意到提示里说答案小于 $2^{64}$。而 `long long` 最大可以表示的数字是 $2^{63}-1$。因此本题需要使用 `unsigned long long`。

> 什么是 `unsigned long long` ？
> `unsigned long long` 是一个无符号的 $64$ 位整数类型，它可以表示范围从 $0$ 到 $2^{64}-1$ 的数字。无法表示负数。

**注意本题是多组数据**


```cpp
int t;
cin >> t;
while (t--)
{
    unsigned long long sum = 2;
    int n;
    cin >> n;
    for (int i = n - 1; i >= 1; i -= 2)
    {
        sum = sum * i;
    }
    cout << sum << "\n"; 
}
```


___


## T4

知识点：结构体排序

定义结构体

```cpp
struct node
{
    string s;
    int p;
}
```

定义排序规则：

```cpp
bool cmp(node x, node y)
{
    if (x.s != y.s)
        return x.s < y.s;
    else 
        return x.p > y.p;
}
```


然后 `sort` 即可，时间复杂度：$O(n\log{n})$。


___


## T5

知识点：结构体排序，贪心思维。

椅子高度是摆设，相当于手的长度是 $b+a$。

由于每个苹果要花费一定的力气，为了获取尽量多的苹果，应当优先摘力气花费少的苹果。

因此使用结构体排序按照力气 $y_i$ 从小到大排序所有苹果。


接下来从前向后遍历所有苹果。

- 首先判断高度是否足够，即 $a+b\geq x_i$。
    - 如果 $s\geq y_i$，则将苹果摘下，并更新 $s$ 的值。
          - $s \leftarrow s-y_i$
          - $cnt\leftarrow cnt+1$

时间复杂度：$O(n\log{n})$。


___

## T6

知识点：数组应用

**准备工作**

`int n, m, r, f1[105], p1[105], f2[105], p2[105]; bool vis[105];`

- 其中 `vis` 数组用来标记聪聪老师出了哪些牌。

**解题思路**

每次翁老师打了第 $p$ 张牌以后，第 $p$ 张牌的花色是 `f1[p]` 点数是 `p1[p]`。

聪聪老师需要在剩余的牌当中选择一张花色相同，且点数恰好大于 `p1[p]` 的牌。

因此：

- 遍历聪聪老师的所有牌，`for (int j = 1; j <= n; j++)`
- 当满足 `vis[j] == 1` 说明第 $j$ 张牌已经使用，可以直接跳过。
- 否则，先判断 `f2[j]` 是否等于 `f1[p]`，如果不等于则跳过。
    - 目的是保证花色相同。
- 其次判断 `p2[j]` 是否大于 `p1[p]`，如果不大于则跳过。
    - 目的是保证点数要大于 `p1[p]`。
- 接下来就是求出点数恰好大于 `p1[p]` 的牌的编号。
    - 就是一个求最小值并且记录编号的过程。
- 若存在这张牌，打上标记。
- 最后统计有多少张牌没有被标记，即为剩下的排。


```cpp
for (int i = 1; i <= n; i++)
{
    int p;
    cin >> p;
    // 打第 p 张牌
    int pos = 1e9;
    for (int j = 1; j <= n; j++)
    {
        if (vis[j]) continue; // 第 j 张牌已经用了
        if (f2[j] == f1[p]) // 先保证花色一样
        {
            if (p2[j] <= p1[p]) continue; // 点数小的跳过不考虑
            // 求出点数恰好大于的牌的编号
            if (pos == 1e9 || p2[j] < p2[pos])
                pos = j;
        }
    }
    // 这个编号存在，就打上标记。相当于聪聪老师用了第 pos 张牌
    if (pos != 1e9)
    {
        vis[pos] = 1;
    }
}
int ans = 0;
for (int i = 1; i <= n; i++)
    if (!vis[i])
        ans++;
cout << ans;
```

___



## T7

知识点：二维数组。


对于原来位置在 $(i,j)$ 的字符，接下来放大 $k$ 倍以后，核心就是找到 $(i,j)$ 这个字符填充了新矩阵的哪一片区域。


不难发现是填充了 $((i-1)\times k+1,(j-1)\times k+1)\sim (i\times k,j\times k)$ 这片区域。

那么枚举所有位置 $(i,j)$ 更新这一片区域的字符即可。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 1e3 + 5;
int n, m, k;
char a[N][N], b[N][N];
int main()
{
    cin >> n >> m >> k;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> a[i][j];
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            int x1 = (i - 1) * k + 1, y1 = (j - 1) * k + 1;
            int x2 = i * k, y2 = j * k;
            for (int x = x1; x <= x2; x++)
                for (int y = y1; y <= y2; y++)
                    b[x][y] = a[i][j];
        }
    }
    for (int i = 1; i <= n * k; i++)
    {
        for (int j = 1; j <= m * k; j++)
            cout << b[i][j];
        cout << "\n";
    }
    return 0;
}
```