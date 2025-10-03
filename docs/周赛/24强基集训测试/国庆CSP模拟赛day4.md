## T1 镜子（mirror）

知识点：数学，贪心。

假设有三个镜子，位置分别为 $x,y,z$ 如果按照 $x,y,z$ 的使用顺序可以发现最终的位置就是

$$
2z-(2y-(2x-a))
$$

化简后可以得到 $2(z-y+x)-a$。

因此如果有奇数个镜子，最终就是 奇加偶减交替下去**减去**固定的 $a$。如果有偶数个镜子，最终就是 奇加偶减交替下去**加上**固定的 $a$。


为了使得答案尽量大，显然应该把大的数字参与加，小的数字参与减。根据 $n$ 是奇还是偶来决定最后减还是加 $a$ 即可。

时间复杂度：$O(n\log{n})$。


**具体实现**

- 从小到大排序所有镜子的位置 $a[1\ldots n]$。
- 初始化 $ans\gets 0$。
- 遍历 $a[1\ldots \lfloor\frac{n}{2}\rfloor]$。令 $ans\gets ans-a[i]$。
- 遍历 $a[\lfloor\frac{n}{2}\rfloor+1\ldots n]$。令 $ans\gets ans+a[i]$。
- 根据 $n$ 的奇偶性来决定最终减去还是加上 $a$。


___


## T2 蛋糕（cake）



知识点：二分，前缀和。


每块蛋糕至多分 $\log{x}$ 次。且最后每一段的大小都是相等的。因此可以预处理每个蛋糕最后分成了多少块，以及每块是多少。分别记作 $sum,x$。

```cpp
int x;
cin >> x;
int sum = 1;
while (x % 2 == 0)
{
    sum *= 2;
    x /= 2;
}
a[i] = {sum, x};
```

对个数维护前缀和 `pre[i] = pre[i - 1] + sum`。


对于每次查询，使用 `lower_bound()` 找到刚好大于等于 $x$ 的位置，然后输出结果即可。

时间复杂度：$O(n\log{x}+q\log{n})$。


___



## T3 序列（seq）

知识点：位运算，前缀和。


本题的核心思想是按位处理，首先定义 `sum[i][j]` 预处理二进制第 $j$ 位的前缀和。

```cpp
for (int i = 1; i <= n; i++) 
{
    for (int j = 0; j < 30; j++)
    {
        if (a[i] >> j & 1)
        {
            sum[i][j] = sum[i - 1][j] + (1 << j);
        }
        else
        {
            sum[i][j] = sum[i - 1][j];
        }
    }
}
```

这样假如没有操作一的话，可以直接对每一位用前缀和查询一次区间和得到最终的答案。


如果有操作一的话，则需要考虑操作一的影响。注意到操作一是对全局赋值 $x$，且或运算不会出现二进制位上 $1$ 变为 $0$ 这种情况。因此维护一个 `bool have[30]` 的数组记录每一个二进制位下是否有 $1$。

针对操作一的数字 $x$，遍历 $x$ 二进制下有 $1$ 的每一位 $j$，标记 `have[j] = true`。

那么在操作二求答案时，如果 `have[j]` 为 `true`，则答案直接累加 $2^j\times (r-l+1)$，否则再用前缀和查询一次区间和。


时间复杂度：$O(n\log{a_i}+q\log{a_i})$。

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 2e5 + 5;
int n, q, sum[N][30], a[N];
bool have[30];
signed main()
{
    freopen("seq.in", "r", stdin);
    freopen("seq.out", "w", stdout);
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> q;
    for (int i = 1; i <= n; i++) 
    {
        cin >> a[i];
        for (int j = 0; j < 30; j++)
            if (a[i] >> j & 1)
                sum[i][j] = sum[i - 1][j] + (1 << j);
            else
                sum[i][j] = sum[i - 1][j];
    }
    while (q--)
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int x;
            cin >> x;
            for (int i = 0; i < 30; i++)
                if (x >> i & 1)
                    have[i] = true;
        }
        else
        {
            
            int l, r;
            cin >> l >> r;
            int ans = 0;
            for (int i = 0; i < 30; i++)
                if (have[i])
                    ans += (1 << i) * (r - l + 1);
                else 
                    ans += sum[r][i] - sum[l - 1][i];
            cout << ans << '\n';
        }
    }
    return 0;
}
```

___


## T4 机器人（robot）



___

