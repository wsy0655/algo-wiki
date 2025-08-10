## 禁止在 int 乘 int 时不开 long long

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


## GDP

略

___

## 烤香肠

略

___

## 音乐


略

___

## 基因

定义 `string t = s`，使用 `reverse(t.begin(), t.end())` 翻转字符串 $t$。

接下来按照题目的配对规则计算稳定性即可，注意数据范围稳定性总和有可能爆 `int`。


> 稳定性极限值为 $1+2+3+\ldots+10^5\approx 10^{10}>2\times 10^9$


时间复杂度：$O(n)$。

```cpp
void solve()
{
    int n;
    cin >> n;
    string s;
    cin >> s;
    string t = s;
    reverse(t.begin(), t.end());
    int ans = 0;
    for (int i = 0; i < n; i++)
    {
        if (s[i] != 'A' && s[i] != 'T' && s[i] != 'C' && s[i] != 'G')
        {
            cout << "0\n";
            return ;
        }
        if (t[i] != 'A' && t[i] != 'T' && t[i] != 'C' && t[i] != 'G')
        {
            cout << "0\n";
            return ;
        }
        if (s[i] == 'A' && t[i] == 'T' || s[i] == 'T' && t[i] == 'A' || s[i] == 'C' && t[i] == 'G' || s[i] == 'G' && t[i] == 'C')
        {
            ans += i + 1;
        }
    }
    cout << ans << "\n";
}
```

___


## 牛奶

略

___


## 数组

略

___


## 土块

知识点：枚举和模拟


题意解析

题目给了两张 **$ n \times 4 $** 的 0/1 矩阵：

- 矩阵 $ a $：标准答案，每行表示某道题每个选项的正误（1 表示该选项正确）。
- 矩阵 $ b $：小 F 实际答题情况，每行表示他在该作答位置选择了哪些选项（1 表示选了）。

但是小 F 答题顺序可能错位：  
如果第一道作答题实际是题号 $ x $，那么第 $ i $ 道作答题对应的实际题号为：$(x + i) \bmod n$

其中 $ i $ 从 0 开始。

评分规则：

1. 至少一个错误选项 **或** 未选择任何选项 → **0 分**
2. 全部正确选项且无错 → **6 分**
3. 部分正确选项且无错 → **3 分**

要求：对所有 $ x \in [0, n-1] $，求小 F 的总分。

---

思路分析

- 枚举 $ x $（第一道作答题的实际题号）。
- 逐题判断得分：
     - 遍历该作答行的 4 个选项，统计：
         - **是否作答**（`ok3`）
         - **是否漏选正确选项**（`ok1`）
         - **是否选错错误选项**（`ok2`）
     - 根据规则加分：
         - 若没作答 → 0 分
         - 若选错 → 0 分
         - 若全选对 → 6 分
         - 若部分正确 → 3 分
- 输出每个 $ x $ 的总分。

时间复杂度：

- 外层枚举 $ x $ → $ O(n) $
- 内层遍历 $ n $ 道题，每题 4 个选项 → $ O(n \times n \times 4) $
- $ n \leq 1000 $，最多 $ 4\times10^6 $ 次判断，可行。

---


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1e3 + 5;
int n, a[N][N], b[N][N];
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 0; i < n; i++)
        for (int j = 1; j <= 4; j++)
            cin >> a[i][j];
    for (int i = 0; i < n; i++)
        for (int j = 1; j <= 4; j++)
            cin >> b[i][j];
    for (int x = 0; x < n; x++) // 枚举作答的第一道题的题号 x
    {
        int sum = 0; // 计算总分
        for (int i = 0; i < n; i++)
        {
            int now = (i + x) % n; // 和第 now 题的答案对照，而不是第 i 题
            int ok1 = 0, ok2 = 0, ok3 = 0; 
            for (int j = 1; j <= 4; j++)
            {
                if (b[i][j])
                    ok3 = 1; // 作答了题目
                if (a[now][j] && !b[i][j]) // 这个是答案，但是我们没选
                    ok1 = 1; // 标记漏选
                if (!a[now][j] && b[i][j]) // 不是答案，但我们选了
                    ok2 = 1; // 标记选错
            }
            if (!ok3)
                sum += 0; // 不答题加 0 分
            else if (ok2) // 选了错误的
                sum += 0;
            else if (!ok1) // 没有漏选的
                sum += 6;
            else 
                sum += 3;
        }
        cout << sum << " ";
    }
    return 0;
}
```


___


## 优秀正整数


知识点：枚举，数学。

**暴力代码**

准备工作：

- 判断质数的函数 `bool prime(long long x)`。
- 数位拆分求数位和的函数 `int f(long long x)`。
- 判断是不是完全平方数的函数 `bool check(long long x)`。


接下来枚举 $L\sim R$ 的所有整数，一一检验是否满足题目要求即可，注意取余细节。

时间复杂度：$O(R-L)$，会 TLE。

```cpp
long long ans = 1;
bool ok = 0;
for (int i = L; i <= R; i++)
{
    if (check(i))
    {
        int sum = f(i);
        if (prime(sum))
        {
            ok = 1; // 标记存在优秀的正整数
            ans = ans * i % mod;
        }
    }
}
if (!ok) ans = 0; // 不存在将答案修改为 0
```

**优化做法**

$L\sim R$ 内的完全平方数大约是 $\sqrt{R-L}$ 个，因此我们可以直接枚举平方根 $i$，若 $i\times i\in [L,R]$。就进行下一步。

这样使得复杂度降低为 $O(\sqrt{R-L})$，可以通过。

```cpp
long long l = sqrt(L), r = sqrt(R), ans = 1;
bool ok = 0;
for (long long i = l; i <= r; i++)
{
    if (i * i < L) continue;
    long long sum = f(i * i);
    if (prime(sum))
    {
        ok = 1;
        // 每两个数相乘就取余一次，避免三个相乘爆掉
        ans = ans * i % mod * i % mod;
    }
}
if (!ok) ans = 0;
cout << ans;
```


___


## 走访


知识点：枚举，前缀和。


注意到传送的本质就是去掉一个长度为 $k$ 的区间，由于我们要求最小的总时间，因此必然是去掉一个区间和最大的区间。

因此使用枚举算法，枚举出所有长度为 $k$ 的区间，使用前缀和快速求出区间和，并找到最大的区间。记作 `ans`。


那么答案就是 $a_1+a_2+\ldots+a_n-ans$。

时间复杂度：$O(n)$。注意 `long long`。


____


## 诚实者


知识点：二进制枚举或 DFS。




由于 $n\leq 15$，因此可以暴力枚举哪些人是诚实的，然后检查诚实的人之间的证词是否会产生矛盾即可。



**二进制枚举的实现方法**

具体实现如下：

- 输入时，使用 `vector<pair<int, int>> b[15]` 存储每个人的所有证词。
    - 在输入时执行 `b[i].push_back({x, y})`

- 枚举所有可能的诚实者状态 `for (int mask = 0; mask < (1 << n); mask++)`。
- 定义 `vector<bool> vis(n, false)` 先给诚实的人打上标记。
    - 当 `for (int i = 0; i < n; i++)` 枚举所有人时，如果 `(mask >> i) & 1` 为真，则说明 `i` 是诚实的人。
    - 则 `vis[i] = true`
- 接下来在去检验诚实的人说话是否矛盾。
    - `for (int i = 0; i < n; i++)` 枚举所有人。
    - 如果 `vis[i]` 为真，则 `for (auto it : b[i])` 枚举第 $i$ 个人的证词。
    - 记 `int x = it.first` 为 `i` 的朋友编号，`int y = it.second` 为 `i` 的证词内容。
    - 如果 `vis[x]` 为真，但 `y` 为假，则矛盾。
    - 如果 `vis[x]` 为假，但 `y` 为真，则矛盾。
    - 给矛盾打上标记。
- 若状态 `mask` 不矛盾，则更新答案即可。答案就是状态 `mask` 二进制下 $1$ 的个数。



[不懂 vector 点我](https://wsy0655.github.io/algo-wiki/STL%E5%AE%B9%E5%99%A8/vector/)


```cpp
#include <bits/stdc++.h>
using namespace std;
vector<pair<int, int>> b[15];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        int a;
        cin >> a;
        for (int j = 0; j < a; j++)
        {
            int x, y;
            cin >> x >> y;
            x--; // 思考一下为何减去 1
            b[i].push_back({x, y}); // 第 i 个人的证言
        }
    }
    int ans = 0;
    for (int mask = 0; mask < (1 << n); mask++)
    {
        vector<bool> vis(n); // 给诚实的人打标记
        for (int i = 0; i < n; i++)
            if (mask >> i & 1)
                vis[i] = 1; // 第 i 个人是诚实的
        bool ok = 1;
        for (int i = 0; i < n; i++)
        {
            if (vis[i])
            {
                for (auto it : b[i])
                {
                    int x = it.first, y = it.second;
                    if (y && !vis[x]) // 认为 x 诚实但记录不诚实
                        ok = 0;
                    if (!y && vis[x]) // x 不诚实，但记录是诚实的
                        ok = 0; 
                }
            }
        }
        int num = __builtin_popcount(mask);
        if (ok) ans = max(ans, num);
    }
    cout << ans;
    return 0;
}
```

___



**DFS的实现方法**


```cpp
#include <bits/stdc++.h>
using namespace std;
vector<pair<int, int>> b[15];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        int a;
        cin >> a;
        for (int j = 1; j <= a; j++)
        {
            int x, y;
            cin >> x >> y;
            b[i].push_back({x, y}); // 第 i 个人的证言
        }
    }
    vector<bool> vis(n + 1);
    int ans = 0;
    auto dfs = [&](int idx, auto &&self) -> void 
    {
        if (idx == n + 1)
        {
            bool ok = 1;
            int sum = 0;
            for (int i = 1; i <= n; i++)
            {
                if (vis[i]) // 如果 i 诚实就看它的证词是否矛盾
                {
                    sum++; // 诚实的人数量加 1
                    for (auto it : b[i])
                    {
                        int x = it.first, y = it.second;
                        if (vis[x] && !y) ok = 0;
                        if (!vis[x] && y) ok = 0;
                    }
                }
            }
            if (ok) ans = max(ans, sum);
            return ;
        }
        vis[idx] = 1; // 认为这个人诚实
        self(idx + 1, self);
        vis[idx] = 0; // 认为这个人不诚实
        self(idx + 1, self);
    };
    dfs(1, dfs);
    cout << ans;
    return 0;
}
```

____



## 西西福王

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


## 古树


知识点：数学，栈，模拟。

主要是用等差数列求和公式优化操作 $1$。操作 $2$ 注意一些实现细节，可能栈顶取出来若干个，并没有完全把栈顶取走。需要更新栈顶的值，建议数组模拟栈实现。


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N = 5e5 + 5;
struct node
{
    int l, r, len;
};
node stk[N];
int t, top;
int f(int l, int r, int len) // 等差数列求和公式
{
    return (l + r) * len / 2;
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> t;
    while (t--)
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int l, r;
            cin >> l >> r;
            stk[++top] = {l, r, r - l + 1};
        }
        else
        {
            int k;
            cin >> k;
            int sum = 0;
            while (k >= stk[top].len)
            {
                sum += f(stk[top].l, stk[top].r, stk[top].len);
                k -= stk[top].len;
                top--;
            }
            if (k)
            {
                int l = stk[top].r - k + 1, r = stk[top].r, len = k;
                sum += f(l, r, len);
                stk[top].r -= k;
                stk[top].len -= k;
            }
            cout << sum << "\n";
        }
    }
    return 0;
}
```
