
## T1

知识点：字符串

带空格的字符串有两种处理办法。

- 使用 `getline()` 函数。

```cpp
string s;
getline(cin, s);
```

- 将输入的字符串看成是若干个字符串，使用循环输入。


```cpp
string s;
while (cin >> s)
{

}
```

显然本题用方法二更简单。这样输入后就紧接着遍历字符串 $s$ 对其进行修改即可。

```cpp
string s;
while (cin >> s)
{
    for (int i = 0; i < s.size(); i++)
    {
        if (i % 2 == 0)
        {
            s[i] = toupper(s[i]);
        }
        else
        {
            s[i] = tolower(s[i]);
        }
    }
    cout << s << " ";
}
```

___

## T2

知识点：最短路


由于路径既要计算点权也要计算边权。考虑将点权转化为边权。

对于输入的边 $(u,v,w)$ 可以认为其边权 $w'=w+a[v]$。

那么起点 $dis[s]$ 初始化不再是 $0$ 而是 $a[s]$。

这样就完美解决了。

根据数据范围使用堆优化 dijkstra 即可。

注意优先队列的第一维要开 `long long`。存图的边权最多 $2\times 10^9$ 可以不开。


```cpp
#define pli pair<long long, int>
void dijkstra(int s) 
{
    for (int i = 1; i <= n; i++) dis[i] = 1e18;
    dis[1] = a[1];
    priority_queue<pli, vector<pli>, greater<pli>> q;
    q.push({dis[1], 1});
    while (q.size())
    {
        auto [d, u] = q.top();
        q.pop();
        if (vis[u]) continue;
        vis[u] = 1;
        for (auto [v, w] : e[u])
        {
            if (dis[v] > dis[u] + w)
            {
                dis[v] = dis[u] + w;
                q.push({dis[v], v});
            }
        }
    }
}
int main()
{
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].push_back({v, w + a[v]});
        e[v].push_back({u, w + a[u]});
    }
    dijkstra(1);
}
```

___


## T3

知识点：最短路


使用 `floyd` 算法对输入的 $10\times 10$ 的 c 矩阵做一遍可以得到任意一个数字 $i$ 转换为 $j$ 的最小代价。

然后输入的 $n\times m$ 的矩阵 $a$，若 $a_{i,j}$ 不等于 $-1$，则累加 $c[a[i][j]][1]$，即累加该数字转化为 $1$ 的代价。

```cpp
#include <bits/stdc++.h>
using namespace std;
int n, m, ans, c[15][15]; 
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 0; i <= 9; i++)
        for (int j = 0; j <= 9; j++)
            cin >> c[i][j];
    for (int k = 0; k <= 9; k++)
        for (int i = 0; i <= 9; i++)
            for (int j = 0; j <= 9; j++)
                c[i][j] = min(c[i][j], c[i][k] + c[k][j]);
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            int x;
            cin >> x;
            if (x != -1)
            {
                ans += c[x][1];
            }
        }
    }
    cout << ans;
    return 0;
}
```

## T4

知识点：倍增

洗牌的改编版本。也是洗 $k$ 次，每次位置 $i$ 洗到位置 $x_i$。

主要的区别在于位置 $i$ 的牌是 $a_i$ 而不是 $i$。

容易写出一个下面的暴力代码。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
int n, x[N], a[N], ans[N];
long long k;
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> x[i];
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
    {
        int now = i; // 初始位置 i
        for (int j = 1; j <= k; j++) now = x[now];
        ans[i] = a[now];
    }
    for (int i = 1; i <= n; i++) cout << ans[i] << " ";
    return 0;
}
```

使用倍增加速，令 $f_{i,j}$ 为 $i$ 跳了 $2^j$ 后的位置。

初始化：$f_{i,0}=x_i$，转移：$f_{i,j}=f_{f_{i,j-1},j-1}$

注意第二维取决于 $k$ 的范围，由于 $2^{60}>10^{18}$，所以第二维需要开到 $60$。

```cpp
for (int i = 1; i <= n; i++)
    f[i][0] = x[i];
for (int j = 1; j <= 59; j++)
    for (int i = 1; i <= n; i++)
        f[i][j] = f[f[i][j - 1]][j - 1];
for (int i = 1; i <= n; i++)
{
    int now = i;
    for (int j = 59; j >= 0; j--)
    {
        if (k >> j & 1)
        {
            now = f[now][j];
        }
    }
    ans[i] = a[now];
}
```


**类似于快速幂的实现方法**

```cpp
#include <bits/stdc++.h>
using namespace std;
long long n, k;
vector<int> mul(vector<int> &a, vector<int> &x)
{
    vector<int> ret(n + 1, 0);
    for (int i = 1; i <= n; i++)
        ret[i] = a[x[i]];
    return ret;
}
int main()
{
	cin >> n >> k;
	vector<int> x(n + 1);
	for (int i = 1; i <= n; i++) cin >> x[i];
	vector<int> a(n + 1);
	for (int i = 1; i <= n; i++) cin >> a[i];
	while (k)
	{
	    if (k & 1)
	        a = mul(a, x);
	    x = mul(x, x);
	    k >>= 1;
	}
	for (int i = 1; i <= n; i++) cout << a[i] << " ";
	return 0;
}
```