## T1 山丘（mountain）

签到题。

将输入的字符画看作是 $n$ 个单独的字符串 $s$。遍历输入的字符串 $s$，若存在相邻两个字符 $s_i,s_{i+1}$，满足：`s[i] = '/‘ && s[i+1] = '\\'`，则答案加 $1$ 即可。

<details>

<summary>代码</summary>


```cpp
int ans = 0;
for (int i = 0; i < n; i++)
{
    string s;
    cin >> s;
    for (int j = 0; j + 1 < m; j++)
    {
        ans += (s[j] == '/' && s[j + 1] == '\\');
    }
}
cout << ans << '\n';
```

</details>

____


## T2 字符对字符错（string）


知识点：枚举，计数。

答案可以通过总的字符对数量减去“字符对”的个数来求得。

总字符对数为：$\binom{n}{2}=\frac{n \times (n-1)}{2}$。

为了最大化“字符错”的数量，应该最小化“字符对”的数量。

如果整个字符串不存在 `?`，则“字符对”的数量是唯一的。

- 预处理每个字母的出现次数记作 `cnt[i]`，其中 $i\in[0,25]$。
- 枚举每个字母，记作 `c`。
- 每个字母的次数 `cnt[c]` 对“字符对”的贡献为 $\binom{cnt[c]}{2}=\frac{cnt[c] \times (cnt[c]-1)}{2}$。

现在有若干个 `?` 需要替换为某个字母，需求仍然是最小化“字符对”的个数。


- 假设只有一个 `?`，则将该 `?` 替换为出现次数最少的那个字母。
- 假设有多个 `?`，则将这些 `?` 依次替换为出现次数最少的那个字母。
    - 注意这一步随着字母次数的更新，出现次数最少的字母或许会变化，因此需要动态维护。

记 `?` 的个数为 `sum`，则循环 `sum` 次，每次遍历 $[0,25]$ 对应的字母，找到次数最小的那个字母记作 `c`，执行 $cnt[c]\gets cnt[c]+1$ 即可。

最后重新枚举每个字母的出现次数，计算更新后的“字符对”的个数。用总字符对数减去“字符对”的个数得到答案。

时间复杂度：$O(26n)$。注意使用 $\text{long long}$。


<details>

<summary>代码</summary>


```cpp
string s;
cin >> s;
int sum = 0, n = s.size();
vector<int> cnt(26);
for (char c : s) 
{
    // isalpha(c) 判断是否为字母
    if (isalpha(c)) cnt[c - 'A']++;
    else sum++;
}
while (sum--)
{
    int mn = 1e9, c = -1;
    for (int i = 0; i < 26; i++)
    {
        if (cnt[i] < mn)
        {
            mn = cnt[i];
            c = i;
        }
    }
    cnt[c]++;
}
int sum = 0;
for (int x : cnt) sum += x * (x - 1) / 2;
cout << n * (n - 1) / 2 - sum << "\n";
```

</details>


____


## T3 旅行（travel）


知识点：动态规划


**子任务 $1$**：$p_1=1$ 

由于字符串长度 $n\leq 2000$。此时的意思是买单程票是最划算的，因此统计两个字符串有多少个字符 `1` 即可。

时间复杂度：$O(n)$。


____


**子任务 $2$**：$p_{pair}=1$

此时显然购买组合票是最划算的，但并不像子任务 $1$ 直接统计 `1` 的数量那样简单。

值得注意的是：买了组合票以后，接下来几天有可能是不需要继续买票的。

具体实现：

- 使用一个名为 `covered` 的数组，记录每一天是否被组合票覆盖。
- 遍历两个字符串，只有当 `a[i]= '1' || b[i] = '1'` 并且 `covered[i] = false` 时，才需要购票。
    - 且由于购买了组合票的缘故，需要将接下来的几天标记为 `true`。


时间复杂度：$O(n)$。


____



**子任务 $3$**：$\forall i\in[1,n],a_i=b_i=1$


此时每一天都需要有票。可以使用枚举法解决。

枚举 $p_3,p_{5},p_{pair}$ 分别买了 $x,y,z$ 张。检验：

- 记 `covered = 3 * x + 5 * y + 4 * z`。
- 则需要补 $n-covered$ 张单人票。

通过枚举，计算出买票花费的最小值。


乍一看时间复杂度为 $O(n^3)$，但由于 $3,5,pair$ 票的上限远达不到 $n$，因此实际可以通过这一部分。


___

**子任务 $4$**：$\forall i\in[1,n],b_i=0$


此时不需要考虑第二个人，只需要考虑第一个人怎么买票花费最少。

这部分可以使用 DP 完成。

定义 $dp_i$ 表示前 $i$ 天计划完成后的最少花费，答案为 $dp_n$。

转移：

- 若 $a_i=1$，则 $dp_i=dp_{i-1}$
- 否则：
    - 买单程票：$dp_i=\min(dp_i,dp_{i-1}+p_1)$
    - 买 $3$ 日票：$dp_i=\min(dp_i,dp_{\max(0,i-3)}+p_3)$
    - 买 $5$ 日票：$dp_i=\min(dp_i,dp_{\max(0,i-5)}+p_5)$
    - 买组合票：$dp_i=\min(dp_i,dp_{\max(0,i-4)}+p_{pair})$


时间复杂度：$O(n)$。


___


**子任务 $5$**

让我们思考更一般的模式，不难想到：

定义 $dp_{i,j}$ 表示翁老师前 $i$ 天计划完成后以及聪聪老师前 $j$ 天的计划完成后的最少花费，答案为 $dp_{n,n}$。

初始化：

- $dp_{0,0}=0$。
- $dp_{i,0}=\min(dp_{i-1,0}+p_1,dp_{\max(0,i-3),0}+p_3,dp_{\max(0,i-5),0}+p_5,dp_{\max(0,i-4),0}+p_{pair})$。
- 同理初始化：$dp_{0,i}$
- 其余 $dp_{i,j}=\infty$。

接下来状态转移类似于子任务 $4$ 的状态转移。

- $dp_{i,j}=\min(dp_{i-1,j}+p_1\cdot [a_i=1],dp_{i,j-1}+p_1\cdot [b_j=1])$
- $dp_{i,j}=\min(dp_{\max(0,i-3),j}+p_3,dp_{i,\max(0,j-3)}+p_3)$
- $dp_{i,j}=\min(dp_{\max(0,i-5),j}+p_5,dp_{i,\max(0,j-5)}+p_5)$
- 注意：当 $i=j$ 时，才可以组合票，因为组合票是一起用的。
    - $dp_{i,j}=\min(dp_{i,j},dp_{\max(0,i-4),\max(0,j-4)}+p_{pair})$


注意初始化，注意 `long long` 等细节即可。



____




## T4 冰淇淋（ice）


知识点：二分，枚举，博弈

**子任务1**

由于 $x=1,y=1$ 因此前两个选择都以固定，第三个选择是翁老师选择，翁老师的目的是最大化得分。

因此答案就是找到一个 $i$，使得 $|a_1+b_1+c_i-p|$ 最大即可。

直接枚举即可。


___


**子任务2**


仅仅 $x=1$，第一个选择已固定。第二个选择希望答案最小，第三个选择希望答案最大。

因此答案就是找到一个 $i,j$ 使得 $\max(|a_1+b_i+c_j-p|)$ 最小。


```cpp
long long mn = 1e18;
for (int i = 1; i <= y; i++)
{
    long long mx = 0;
    for (int j = 1; j <= z; j++)
    {
        mx = max(mx, abs(a[1] + b[i] + c[j] - p));
    }
    mn = min(mn, mx);
}
```

___



**子任务3**


相比子任务 $2$ 多了一个第一层的选择，第一层的目的是希望答案最大，外面套一个求最大值的循环即可。


```cpp
int ans = 0;
for (int i = 1; i <= x; i++)
{
    int mn = 1e18;
    for (int j = 1; j <= y; j++)
    {
        int mx = 0;
        for (int k = 1; k <= z; k++)
        {
            mx = max(mx, abs(a[i] + b[j] + c[k] - p));
        }
        mn = min(mn, mx);
    }
    ans = max(ans, mn);
}
cout << ans;
```

___



**子任务4**


注意到一旦 $i,j$ 确定后， $k$ 的目的就是让答案最大化。也就是 $|a_i+b_j-p|$ 已是定值记为 $S$，在找到一个 $c_k$ 使得 $|S+c_k|$ 最大。

不难证明：答案一定在 $\min(c_k)$ 和 $\max(c_k)$ 处取得。

因此不需要枚举 $k$，提前维护出 $\min(c_k)$ 和 $\max(c_k)$ 即可。



```cpp
int ans = 0;
for (int i = 1; i <= x; i++)
{
    int mn = 1e18;
    for (int j = 1; j <= y; j++)
    {
        int mx = max(abs(a[i] + b[j] + mxc - p), abs(a[i] + b[j] + mnc - p));
        mn = min(mn, mx);
    }
    ans = max(ans, mn);
}
cout << ans;
```

___



**子任务5**


说白了就是不枚举 $j$ 也把答案求出来。


当枚举 $i$ 以后，设 $f(j)=\max(|a_i+b_j+\max(c_k)-p|,a_i+b_j+\min(c_k)-p)$。

定义 $L=a_i+\max(c_k)-p$，$R=a_i+\min(c_k)-p$。

则 $f(j)=\max(|L+b_j|,|R+b_j|)$。其中 $j\in[1, y]$，我们需要求出 $f(j)$ 函数的最小值，然后在针对枚举的每一个 $i$ 去求最大值即可。


子任务 $4$ 实际就是通过枚举 $j$ 找到了函数 $f(j)$ 的最小值而已。


**如何快速找到函数 $f(j)$ 的最小值呢？**

变形函数得到 $f(j)=\max(|b_j-(-L)|,|b_j-(-R)|)$。思考 $|a-b|$ 这个绝对值算式的几何意义：就是数轴上点 $a\to b$ 的距离。

因此 $f(j)$ 函数的几何意义就是 $b_j$ 离 $-L$ 和 $-R$ 的距离中较大的那个。


**结论：**

- 要让它尽量小，就把 $j$ 放在两点的“切中位置”。
- **直观证明**：在中点处两项相等，此时最大者被“压平”到最小；一旦离开中点，远端那一项会变大，从而 $\max(\cdot)$ 增大。


**具体实现**

由于在实际题目中 $b_j$ 都是整数，因此求出 $\text{mid}=\lfloor \dfrac{-(L+R)}{2}\rfloor$。


使用 `lower_bound` 求出刚好大于等于 $\text{mid}$ 的 $j$。比较 $f(j),f(j-1)$ 二者哪个函数值最小，选最小的那个作为答案即可。


最终时间复杂度：$O(x\log{y})$。


```cpp
int x, y, z, p, a[N], b[N], c[N];
int f(int L, int R, int j)
{
    return max(abs(L + b[j]), abs(R + b[j]));
}
signed main()
{
    cin >> x >> y >> z >> p;
    for (int i = 1; i <= x; i++)
        cin >> a[i];
    for (int i = 1; i <= y; i++)
        cin >> b[i];
    for (int i = 1; i <= z; i++)
        cin >> c[i];
    sort(b + 1, b + y + 1);
    sort(c + 1, c + z + 1);
    int mxc = c[z];
    int mnc = c[1];
    int ans = 0;
    for (int i = 1; i <= x; i++)
    {
        int L = a[i] - p + mxc, R = a[i] - p + mnc;
        int mid = -(L + R) / 2;
        int pos = lower_bound(b + 1, b + y + 1, mid) - b;
        int mn = 1e18;
        if (pos != y + 1)
        {
            mn = min(mn, f(L, R, pos));
        }
        if (pos != 1)
        {
            mn = min(mn, f(L, R, pos - 1));
        }
        ans = max(ans, mn);
    }
    cout << ans;
    return 0;
}
```




