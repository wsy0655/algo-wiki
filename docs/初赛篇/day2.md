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
using namespace std;
using ll = long long;
using pii = pair<ll, ll>;
const int N = 1e5 + 5, mod = 998244353;
ll n, k, dep[N], ans, mx;
priority_queue<pii, vector<pii>, greater<pii>> q;
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        ll x;
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
    while (q.size() >= k)
    {
        ll sum = 0, mx_h = 0;
        for (int i = 1; i <= k; i++)
        {
            auto [w, h] = q.top();
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