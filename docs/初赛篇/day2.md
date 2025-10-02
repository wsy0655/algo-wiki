## [蓝桥杯 2024 国 Java A] 栈

<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 1e6 + 5;
int n, l[N], r[N], h = 0, t = 1e6 + 1, ans;
bool vis[N]; 
void ins(int x, int y) // x 右边插入 y
{
	int z = r[x];
	r[x] = y, l[y] = x;
	r[y] = z, l[z] = y;
	if (x != h && (x + y) & 1)
		ans++; 
} 
void del(int y)
{
	int x = l[y], z = r[y];
	r[x] = z, l[z] = x;
	if (x != h && (x + y) & 1) ans--; 
	if (z != t && (y + z) & 1) ans--;
	if (x != h && z != t && (x + z) & 1) ans++; 
}
int main()
{
    cin >> n;
    r[h] = t, l[t] = h;
	while (n--)
	{
		int x;
		cin >> x;
		if (vis[x])
		{
			del(x);
		}
		vis[x] = 1;
		ins(l[t], x);
		cout << ans << "\n"; 
	} 
    return 0;
}
```

</details>
____


## 合并果子加强版


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N = 1e5 + 5;
int n, ans;
int cnt[N];
queue<int> q1, q2; 
int read()
{
	int s = 0, f = 1;
	char ch = getchar();
	while (ch < '0' || ch > '9')
	{
		if (ch == '-') f = -1;
		ch = getchar();
	}
	while (ch >= '0' && ch <= '9')
	{
		s = s * 10 + ch - '0';
		ch = getchar();
	}
	return s * f;
}
int top()
{
	if (q2.empty() || !q1.empty() && q1.front() < q2.front())	
	{
		int x = q1.front();
		q1.pop();
		return x;
	}
	else
	{
		int x = q2.front();
		q2.pop();
		return x;
	}
}	
signed main()
{
    n = read();
    for (int i = 1; i <= n; i++) 
    {
    	int x = read();
    	cnt[x]++;
	}
	for (int i = 1; i <= 1e5; i++)
		while (cnt[i]--)
			q1.push(i);
	for (int i = 1; i <= n - 1; i++)
	{
		int x = top();
		int y = top();
		ans += x + y;
		q2.push(x + y); // q2 放新合并的数字 
	}
	cout << ans; 
    return 0;
}
```

</details>
____


## [NOI2015] 荷马史诗


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
using pii = pair<int, int>;
constexpr int N = 1e5 + 5;
int n, k, dep[N], ans, mx;
priority_queue<pii, vector<pii>, greater<pii>> q;
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        int x;
        cin >> x;
        q.push({x, 1}); // 初始深度为 1
    }
    if ((n - 1) % (k - 1) != 0) // 设置 0 的点进去
    {
        for (int i = 1; i <= (k - 1) - (n - 1) % (k - 1); i++)
        {
            q.push({0, 1});
        }
    }
    while (q.size() > 1)
    {
        int sum = 0, mx_h = 0;
        for (int i = 1; i <= k; i++)
        {
            auto [w, h] = q.top(); // C++17 语法，考场禁用！
            q.pop();
            mx_h = max(mx_h, h);
            sum += w;
        }
        q.push({sum, mx_h + 1});
        mx = max(mx, mx_h);
        ans += sum;
    }
    cout << ans << "\n" << mx;
    return 0;
}
```

</details>


## 分裂



<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
using pii = pair<int, int>;
template <typename T>
using vec = vector<T>;
constexpr int N = 2e5 + 5, mod = 998244353;

signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int L, q;
    cin >> L >> q;
    set<pii> s;
    s.insert({1, L});
    while (q--)
    {
        int op, x;
        cin >> op >> x;
        if (op == 1)
        {
            auto it = s.upper_bound({x, L + 1});
            it--;
            int l = it->first, r = it->second;
            s.erase(it);
            s.insert({l, x});
            s.insert({x + 1, r});
        }
        else
        {
            auto it = s.upper_bound({x, L + 1});
            it--;
            cout << it->second - it->first + 1 << '\n';
        }
    }   
    return 0;
}
```

</details>


## [ABC228D] Linear Probing


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1 << 20;
int q, fa[N], a[N];
int find(int x)
{
    if (fa[x] == x)
        return x;
    return fa[x] = find(fa[x]);
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> q;
    for (int i = 0; i < N; i++)
        fa[i] = i, a[i] = -1;
    while (q--)
    {
        int op, x;
        cin >> op >> x;
        if (op == 1)
        {
            int h = x % N;
            int f = find(h);
            a[f] = x;
            fa[f] = find((f + 1) % N);
        }
        else
        {
            cout << a[x % N] << "\n";
        }
    }
    return 0;
}
```

</details>


## [eJOI2020 Day1] Fountain


<details>

<summary>完整代码</summary>


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1e5 + 5;
int n, q, d[N], c[N], stk[N], top;
int f[N][17], g[N][17];
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> q;
    for (int i = 1; i <= n; i++) cin >> d[i] >> c[i];
    // 这个题留到地面输出 0，不用初始化 n + 1 
    for (int i = 1; i <= n; i++)
    {
    	while (top && d[i] > d[stk[top]]) 
    	{
    		f[stk[top]][0] = i; // 栈顶右边大于自己的是 i 
			g[stk[top]][0] = c[i]; // 栈顶右边大于自己的盘子的容量是 c[i] 
			top--; 
		}
		stk[++top] = i; 
	}
	for (int j = 1; j <= 16; j++)
	{
		for (int i = 1; i <= n; i++)
		{
			f[i][j] = f[f[i][j - 1]][j - 1];
			g[i][j] = g[i][j - 1] + g[f[i][j - 1]][j - 1];
		}
	}
	while (q--)
	{
		int r, v;
		cin >> r >> v;
		v -= c[r];
        for (int j = 16; j >= 0; j--)
        {
            if (v > g[r][j]) // 不能写等于，严格大于才有多余的水流向后面
            {
                v -= g[r][j];
                r = f[r][j];
            }
        }
        if (v > 0) r = f[r][0];
        cout << r << "\n";
	}
    return 0;
}
```

</details>

