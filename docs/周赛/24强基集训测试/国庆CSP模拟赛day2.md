## T1 移动格子（move）

在每次操作后，剩余数字的位置会被重新编号，且新位置为原位置除以 $2$（整数除）。初始时位于偶数位置的数字在第一次操作后会保留（在后面操作中继续相同规则）。最终剩下的数字一定是经过多次删除后始终未被移除的数，即它在每次操作中都处于偶数位置。

**结论：**

若 $n$ 是 $2$ 的幂（$1,2,4,8,\ldots$）则最后剩下的数字是 $n$ 本身。否则最后剩下的数字是不超过 $n$ 的最大的 $2$ 的幂。


<details>

<summary>参考代码</summary>


```cpp
int ans = 1;
while (ans <= n)
    ans *= 2;
cout << ans;
```

</details>

___



## T2 数组（array）


注意到 $\min(x_a,x_b)=c$ 等价于一组较好处理的条件：

- $x_a\geq c$
- $x_b\geq c$

那么直接令 $x_a=c,x_b=c$ 是可以满足当前条件的。

但是会出现另外一个问题：假如 $x_a$ 已赋值为 $c_0$，又出现一组限制条件为 $x_a,x_b,c_1$ 且 $c_1<c_0$，那么 $x_a$ 如果继续赋值为 $c_1$ 就无法满足之前的条件了。

为了解决这个办法：在赋值时取 $\max$ 即可。因为题目保证这样的数组存在，因此按照这个构造即可。对于不存在的值，最后输出任意一个正整数（$1\sim 10^9$）即可通过。

时间复杂度：$O(n+m)$。


<details> 

<summary>参考代码</summary>

```cpp
while (m--)
{
    int a, b, c;
    cin >> a >> b >> c;
    x[a] = max(x[a], c);
    x[b] = max(x[b], c);
}
for (int i = 1; i <= n; i++)
{
    if (x[i] == 0) x[i] = 1;
    cout << x[i] << " ";
}
```

</details>



___


## T3 自学（self）


标签：二分，贪心。


考虑二分答案，二分每门课理解程度 $x$ 的最小值最大可能是多少。

一共有 $n\times m$ 个课时，如果只考虑行动 $1$，那么每门课的最终理解程度就是固定的 $a_i\times m$，因此我们可能在某些课程上了若干次满足条件以后，需要分配一些课时去自学其他课程来完成最终的目标。（甚至自学优于直接去上课）


这里的检查方式可以转变为：求出每门课程都用于至少拥有 $x$ 的理解程度下，一共需要多少课时记作 $\text{cnt}$，如果 $\text{cnt}\leq n\times m$ 则说明可以完成，否则无法完成。

接下来的上课是一个贪心的过程：

**当 $a_i>b_i$**，显然优先选择直接去上课而不是自学。


- 若 $a_i\times m\leq x$。
    - 则仅仅上课 $m$ 次还不够，还需要一定的自学。
    - 上课 $m$ 次以后，还剩余 $x-(a_i\times m)$ 的理解度需要自学。

因此还需要 $\lceil \dfrac{x-(a_i\times m)}{b_i}\rceil$ 次自学。


- 若 $a_i\times m>x$，则上一定的课程后就可以满足限制不需要自学。

因此需要 $\lceil \dfrac{x}{a_i}\rceil$ 次上课。

**当 $a_i\leq b_i$**，显然优先选择自学。需要：$\lceil \dfrac{x}{b_i}\rceil$ 次自学。


____

在整个过程中维护课时的次数 $\text{cnt}$ 最终与 $n\times m$ 比较即可。


时间复杂度：$O(n\log{v})$，其中 $v=10^{18}$。



<details> 

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 3e5 + 5;
int n, m, a[N], b[N];
bool check(int x)
{
    int cnt = n * m;
    for (int i = 1; i <= n; ++i)
    {
        if (a[i] > b[i]) // 优先上课，不够的靠自学补。注意每门课最多上 m 次
        {
            if (a[i] * m <= x)
            {
                cnt -= m; // 先上课 m 次
                int tmp = x - a[i] * m;  // 剩余理解程度靠自学完成
                cnt -= (tmp + b[i] - 1) / b[i]; // 除以 b[i] 向上取整
            }
            else // 无需自学，直接上课就够用
            {
                cnt -= (x + a[i] - 1) / a[i];
            }
        }
        else // 优先自学，自学次数没有限制，因此学到满足要求即可。
        {
            cnt -= (x + b[i] - 1) / b[i];
            
        }
        if (cnt < 0) return 0;
    }
    return 1;
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; ++i)
        cin >> a[i];
    for (int i = 1; i <= n; ++i)
        cin >> b[i];
    int l = 0, r = 1e18, ans = 0;
    while (l <= r)
    {
        int mid = l + r >> 1;
        if (check(mid))
            ans = mid, l = mid + 1;
        else
            r = mid - 1;
    }
    cout << ans;
    return 0;
}
```

</details>


____


## T4 正确的数列（seq）


知识点：单调栈


**$60$ 分做法**

暴力 DP，定义 $dp_i$ 为前 $i$ 个数字可以最少划分多少段。

转移：枚举上一段的结尾 $j$，其中 $j\in [0,i-1]$，若 $[j+1,i]$ 是正确的区间。则 $dp_i=\min(dp_i,dp_j+1)$。

使用 ST 表检查 $[j+1,i]$ 是否为正确的区间。最终时间复杂度为 $O(n^2)$。



<details> 

<summary>参考代码</summary>


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 3e5 + 5;
int n, a[N], f[N][19], g[N][19], Log2[N], dp[N];
void init()
{
    for (int i = 1; i <= n; i++) f[i][0] = g[i][0] = a[i];
    for (int i = 2; i <= n; i++) Log2[i] = Log2[i >> 1] + 1;
    for (int j = 1; (1 << j) <= n; j++)
    {
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        {
            f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
            g[i][j] = min(g[i][j - 1], g[i + (1 << (j - 1))][j - 1]);
        }
    }
}
int query(int l, int r, int op)
{
    int len = Log2[r - l + 1];
    if (op) return max(f[l][len], f[r - (1 << len) + 1][len]);
    else return min(g[l][len], g[r - (1 << len) + 1][len]);
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        dp[i] = 1e9;
    }
    init();
    for (int i = 1; i <= n; i++)
    {
        for (int j = 0; j < i; j++)
        {
            if (query(j + 1, i, 1) == a[i] && query(j + 1, i, 0) == a[j + 1])
            {
                dp[i] = min(dp[i], dp[j] + 1);
            }
        }
    }
    cout << dp[n];
    return 0;
}
```

</details>

____


**满分解法**

若以 $l$ 为左端点，则对应的右端点的位置 $r$ 应该满足：$r<\text{rmn}[l]$，其中 $\text{rmn}[l]$ 为 $l$ 右边第一个小于 $a[l]$ 的位置。（可以使用单调栈预处理）



例如：$a=[2,3,4,1,0]$，若以 $l=1$ 为左端点，则 $r$ 必须小于 $\text{rmn}[1]=4$。


形式化地，对于满足条件的区间 $[l,r]$，应符合 $r<\text{rmn}[l]$。

且还需要确保 $a_r$ 是 $[l,r]$ 的最大值，那么如何找到这个 $r$ 呢？


考虑到每个区间的右端点必须要比左端点的数大，可以求出 $\text{rmx}[i]$ 表示 $i$ 右边第一个大于 $a[i]$ 的位置。

为了满足题目中分割的区间尽可能少的要求，每个区间应尽可能的大。为了达成这个条件，在求取以 $l$ 为左端点区间的右端点 $r$ 时，可以从 $l$ 起跳。

- 初始化 $r=l$
- 当满足 $\text{rmx}[r]<\text{rmn}[l]$ 时说明跳过去合法，令 $r\gets \text{rmx}[r]$

如此一来，即可获得以 $l$ 为左端点的最长区间。在求取下一个区间时，只需要让 $l\gets r+1$ 即可。


<details> 

<summary>参考代码</summary>


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 3e5 + 5;
int n, a[N], stk[N], top, rmn[N], rmx[N];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        rmn[i] = rmx[i] = n + 1;
    }
    for (int i = 1; i <= n; i++)
    {
        while (top && a[i] < a[stk[top]]) 
            rmn[stk[top--]] = i;
        stk[++top] = i;
    }
    top = 0;
    for (int i = 1; i <= n; i++)
    {
        while (top && a[i] >= a[stk[top]]) 
            rmx[stk[top--]] = i;
        stk[++top] = i;
    }
    int ans = 0;
    for (int l = 1; l <= n; l++)
    {
        int r = l;
        while (r <= n && rmx[r] < rmn[l])
            r = rmx[r];
        ans++;
        l = r;
    }
    cout << ans;
    return 0;
}
```

</details>




____


## 补充：T5 [GESP202509 六级] 划分字符串


划分若干段，可以考虑 DP 求解。

定义：$dp_i$ 表示前 $i$ 个字符划分为若干段的最大分数。

转移：枚举上一段的结尾位置 $j$，其中 $j\in [0,i-1]$，当满足 $[j+1,i]$ 没有重复字符。则可以转移更新：

$$
dp_i=\max(dp_i,dp_{j}+a[i-j])
$$

其中 $a[i-j]$ 表示 $[j+1,i]$ 这一段长度的代价。$dp_{j}$ 表示 $[1,j]$ 的最大分数。


乍一看复杂度是 $O(n^2)$，但根据鸽笼原理，如果 $[j+1,i]$ 的长度大于 $26$，则必然有一个字母会重复出现。通过这样的优化，总复杂度可以降低为 $O(26n)$。


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1e5 + 5;

signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n;
	cin >> n;
	string s;
	cin >> s;
	s = " " + s;
	vector<int> a(n + 1);
	for (int i = 1; i <= n; i++)
		cin >> a[i]; 
	vector<int> dp(n + 1); 
	for (int i = 1; i <= n; i++)
	{
		vector<bool> vis(26);
		// 枚举上一段的结尾 j，[0, i-1]
		// dp[i] = dp[j] + a[i - j] [j + 1 ~ i] 的代价是 a[i-j] 
		for (int j = i - 1; j >= 0 && i - j <= 26; j--)
		{
			if (vis[s[j + 1] - 'a']) break; // s[j + 1] 是新增的字符如果已经出现过就直接结束
			vis[s[j + 1] - 'a'] = true;
			dp[i] = max(dp[i], dp[j] + a[i - j]);
		}
	}
	cout << dp[n];
    return 0;
}


```


