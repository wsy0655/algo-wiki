## T1

$$ \begin{aligned}
&\sum\limits_{a=1}^{A}\ \sum\limits_{b=1}^{B}\ \sum\limits_{c=1}^{C}\ a\cdot b\cdot c \\
=&\sum\limits_{a=1}^{A} a\cdot \sum\limits_{b=1}^{B} b\cdot \sum\limits_{c=1}^{C} c\\
=&\frac{(1+A)\cdot A}{2}\times \frac{(1+B)\cdot B}{2}\times \frac{(1+C)\cdot C}{2}
\end{aligned}$$

注意取余细节，由于 $A,B,C\leq 10^9$，因此 $\frac{(1+A)\cdot A}{2}\leq 10^{18}$。

注意：除法不支持边计算边取余。

结果可以使用 `long long` 完整算出后，在进行三次分布乘法取余。


```cpp
long long a, b, c;
cin >> a >> b >> c;
long long sum1 = (1 + a) * a / 2;
long long sum2 = (1 + b) * b / 2;
long long sum3 = (1 + c) * c / 2;
long long ans = sum1 * sum2 % mod * sum3 % mod;
```

时间复杂度：$O(1)$


___


## T2


字符串模拟题，主要考察实现细节。

首先可以把以 `.` 作为分隔符，把每个字符串单独提取出来存储。

- 题目忽略大小写，可以统一全部小写或大写处理。

```cpp
string s;
cin >> s;
for (char &c : s) c = tolower(c);
vec<string> a;
string t;
for (char c : s)
{
    if (c == '.')
    {
        a.push_back(t);
        t = "";
    }
    else
    {
        t += c;
    }
}
a.push_back(t); // 最后一段单独的也要存储一次
```

此时若 `a.size() < 3` 程序可以直接结束。


接下来就是求解答案的过程。

遍历所有字符串，`for (int i = 2; i < a.size(); i++)`，这里选择从 $i=2$ 开始，这样就直接保证至少有 $3$ 个字符串了，分别是 `a[0], a[1], a[2]`。


若满足 `a[i - 1] == "edu"`，那么就检查 `a[i]` 是否存在一个长度为 $2$ 的前缀是 `cn` 即可。

在过程中维护从头到尾有多少个字符即可。具体可以看代码

```cpp
if (a.size() < 3)
{
    return 0;
}
int pre = a[0].size() + a[1].size(); // 初始设为前两个字符串的长度之和
for (int i = 2; i < a.size(); i++)
{
    if (a[i - 1] == "edu" && a[i].substr(0, 2) == "cn")
    {
        // i 即为字符 . 的个数
        cout << pre + 2 + i << " ";
    }
    pre += a[i].size(); // 遍历完以后累加 a[i] 的长度
}
```

时间复杂度：$O(n)$，其中 $n$ 为输入的字符串总长度。

___

## T3

贪心结合二分答案。

由于魔法的使用是独立的，且一个魔法只用一次。且不难看出魔法的作用就是让路程多加上 $a_i$，即累加魔法值。

- 例如用了 $3,5$ 这两个魔法，路程就变为了 $L+3+5=14$。

由于希望使用的魔法数量尽量少，因此优先使用 $a_i$ 较大的魔法。

- 因此按照 $a_i$ 从大到小排序。

答案问题具备单调性，若使用 $x$ 个魔法可以使得登山时间大于 $t$，则用更多的魔法都可以办到。因此二分魔法个数 $x$。

- 实现一个 `check(x, t)` 的检查函数，检验是否使用前 $x$ 个魔法可以使得登山时间超过 $t$。

使用前 $x$ 个魔法以后总的路程就是 $a_1+a_2+\ldots+a_x+L$。

- 不难看出要用前缀和维护 $a_i$。


判断是否可行，不建议用除法。可以转变为乘法判断。

- 若满足 $sum[x] + L > v * t$，则说明登山时间必然超过 $t$。

时间复杂度：$O(n\log{n} +q\log{n})$


```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int N = 2e5 + 5;
ll n, L, v, a[N], sum[N];
bool check(ll x, ll t) // 检验使用了 x 个魔法以后的总时间是否大于 t
{
    return sum[x] + L > v * t;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> L >> v;
    for (int i = 1; i <= n; i++) cin >> a[i];
    // 从大到小排序 a 序列
    sort(a + 1, a + n + 1, greater<ll>());
    for (int i = 1; i <= n; i++)
        sum[i] = sum[i - 1] + a[i];
    int q;
    cin >> q;
    while (q--)
    {
        ll t;
        cin >> t;
        // 最少用 0 个魔法，最多用 n 个魔法
        ll l = 0, r = n, ans = -1;
        while (l <= r)
        {
            ll mid = l + r >> 1;
            if (check(mid, t))
            {
                ans = mid, r = mid - 1;
            }
            else
            {
                l = mid + 1;
            }
        }
        cout << ans << "\n";
    }
    return 0;
}
```

___

## T4


知识点：贪心，排序，前缀和，前缀最值。


注意到：一旦确定了 $A_{max},A_{min}$ 的值以后，我们要做的就是最大化 $S$ 本身就可以了。

也就是说，所以满足 $A_{min}\leq A_i\leq A_{max}$ 的艺术品 $i$ 都应该选择进来。（因为 $B_i\geq 1$），都选择进来可以最大化 $B_i$ 的和，即最大化 $S$。


因此首先使用结构体排序，按照 $A_i$ 这个维度升序排序。

```cpp
struct node
{
    long long a, b;
} v[N];
bool cmp(node a, node b)
{
    return a.a < b.a;
}
sort(v + 1, v + n + 1, cmp);
```

现在问题可以转化为：给定一个区间 $[i,j]$。最大化

$$
B_{i}+B_{i+1}+...+B_{j}-(A_j-A_i)
$$

定义前缀和 $sum[i]=B_1+B_2+...+B_i$。

则答案等同于求解

$$\begin{cases}
i\leq j\\
sum[j]-sum[i-1]-(A_j-A_i)
\end{cases}$$

那么枚举 $i,j$ 就可以得到 $50$ 分了。

考虑优化，首先对求解式子做变形得到 

$$
sum[j]-A_j-(sum[i-1]-A_i)
$$

枚举 $j$，维护 $sum[i-1]-A_i$ 的前缀最小值 $mn$ 即可。

时间复杂度：$O(n\log{n})$，来自于排序。

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int N = 5e5 + 5;
struct node
{
    ll a, b;
} v[N];
bool cmp(node a, node b)
{
    return a.a < b.a;
}
ll n, sum[N];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> v[i].a >> v[i].b;
    }
    sort(v + 1, v + n + 1, cmp);
    for (int i = 1; i <= n; i++)
    {
        sum[i] = sum[i - 1] + v[i].b;
    }
    ll mn = 9e18, mx = -9e18;
    for (int j = 1; j <= n; j++)
    {
        mn = min(mn, sum[j - 1] - v[j].a);
        mx = max(mx, sum[j] - v[j].a - mn);
    }
    cout << mx;
    return 0;
}
```





