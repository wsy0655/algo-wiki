## T1

主要考察字符串的一些函数使用。

**操作一：利用字符串拼接即可。**

- **拼接字符串：**


时间复杂度：$O(|S|)$。

```cpp
string s = "hello";
string t = "world";
cout << s + t; // helloworld
```

- **拼接字符：**

```cpp
string s = "hello";
char t = 'w';
cout << s + t; // hellow
```

**操作二：截取子串**



**`substr()`**

* **功能**：从字符串中截取指定长度的子串。
* **语法**：

时间复杂度：$O(len)$，$len$ 为截取长度。

```cpp
s.substr(a, b); // 从下标 a 开始，取 b 个字符
s.substr(a);    // 从下标 a 开始到末尾
```

* **示例**：

```cpp
string s = "hello";
string t = s.substr(2, 3); // "llo"
string t = s.substr(1);    // "ello"
```

* **打印所有子串**

```cpp
string s;
cin >> s;
int n = s.size();
for (int i = 0; i < n; i++)
{
    for (int j = i; j < n; j++)
    {
        // [i, j] 一共有几个字符
        int len = j - i + 1;
        string t = s.substr(i, len);
        cout << t << "\n"; 
    }
}
```


**操作三：插入函数**


时间复杂度：$O(|S|)$


**`insert()`**

* **功能**：在指定位置前插入字符串。
* **语法**：

```cpp
s.insert(pos, str);
```

* **示例**：

```cpp
string s = "hlo";
s.insert(1, "el"); // s 变为 "hello"
```



**操作四：查找函数**


**`find()`**

* **功能**：查找字符串中某个字符或子串**第一次出现**的位置。
* **语法**：


时间复杂度：$O(|S|)$

```cpp
string s = "heblb";
int pos = s.find("b");
// pos = 2
if (pos == -1)
{
    cout << "未找到" << endl;
}
int pos = s.find("hello");
if (pos == -1)
{
    cout << "未找到" << endl;
}
```

* **返回值**：若找到，返回首字符的下标；若未找到，返回 `s.npos`。
    * 也可以赋值后与 $-1$ 比较。 

**`rfind()`**

* **功能**：查找字符串中某个字符或子串**最后一次出现**的位置。
* **语法**：

```cpp
s.rfind("l");
```

* **返回值**：同 `find()`。

```cpp
int op;
cin >> op;
if (op == 1)
{
    string t;
    cin >> t;
    s += t;
    cout << s << "\n";
}
if (op == 2)
{
    int a, b;
    cin >> a >> b;

    s = s.substr(a, b);
    cout << s << "\n";
}
if (op == 3)
{
    int a;
    cin >> a;
    string t;
    cin >> t;
    s.insert(a, t);
    cout << s << "\n";
}
if (op == 4)
{
    string t;
    cin >> t;
    int pos = s.find(t);
    if (pos == -1) cout << "-1\n";
    else cout << pos  << "\n";
}
    
```

___

## T2


考察全排列，暴力枚举。

比较容易的做法是将输入看作是字符串。首先对该字符串排序，确保是最小字典序。


- 利用 `next_permutation` 函数，将字符串进行全排列。

> 为何要排序字符串？
>
> `next_permutation` 函数会按照字典序生成一个排列。因此要从字典序最小的排列开始逐个生成。


```cpp
string s;
cin >> S;
sort(s.begin(), s.end());
do
{

} while (next_permutation(s.begin(), s.end()));
```


接下来枚举分割位置 $i$，其中 $i\in [1,n-1]$，$n=s.size()$。


- 那么 $[0, i-1]$ 属于前半部分，$[i,n-1]$ 属于后半部分。
- 这样枚举可以确保两个部分都有字符，不为空。

利用 `substr` 函数切割出两个部分即可。分别记作字符串 $l$ 与 $r$。

- 若 `l[0] == '0'` 或 `r[0] == '0'`，则跳过。


否则利用 `stoi` 函数将字符串转为数字。求乘积的最大值即可。

时间复杂度：$O(n!\cdot n^2)$，其中 $n$ 为字符串的长度根据数据范围来看可以通过。


```cpp
sort(s.begin(), s.end());
int ans = 0;
do
{
    int n = s.size();
    for (int i = 1; i < n; i++)
    {
        string l = s.substr(0, i), r = s.substr(i);
        if (l[0] == '0' || r[0] == '0') 
        {
            continue;
        }
        ans = max(ans, stoi(l) * stoi(r));
    }
} while (next_permutation(s.begin(), s.end()));
```


____


## T3

单调栈模板题，定义 $r_i$ 为 $i$ 右侧第一个大于或等于自己的位置。

- 等于的在本题也会被挡住无法继续向右看。

- 不存在则为 $n+1$

维护一个从栈顶到栈底单调递增的栈，栈内元素为索引。

若新元素 $a[i]$ **大于或等于** 栈顶，则对于栈顶来说，右边第一个大于自己的编号就是 $i$，这个时候更新答案即可。

注意开 `long long`。显然当 $n=8\times 10^4$，构造一个单调递减的序列，答案可以超过 `int`。


```cpp
for (int i = 1; i <= n; i++) r[i] = n + 1;
for (int i = 1; i <= n; i++)
{
    cin >> a[i];
    while (top && a[i] >= a[stk[top]])
    {
        r[stk[top--]] = i;
    }
    stk[++top] = i;
}
for (int i = 1; i <= n; i++)
{
    ans += r[i] - i - 1;
}
cout << ans;
```


___


## T4

考察单调队列的理解，单调队列在剔除过期元素以后，为了保证队头是最大值，此时队列必然是从头到尾呈递减状态。那么队列里有多少个元素就有多少个后缀最大值。

注意使用 `unsigned long long` 类型。


> 一个坑点
>
> 有些人将所有变量都设为了 `unsigned long long`，但是 `for` 循环内依然是 `int i`。
>
> 导致 `i - k + 1` 是 `int` 和 `unsigned long long` 相减，导致结果为 `unsigned long long`。从而发生一些错误。

```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 1e6 + 5;
int n, k, q[N], h, t; 
unsigned long long a[N];    
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> k;
    h = 1, t = 0;
    for (int i = 1; i <= n; ++i)
    {
        cin >> a[i];
        while (h <= t && q[h] < i - k + 1) h++;
        while (h <= t && a[i] >= a[q[t]]) t--;
        q[++t] = i;
        if (i >= k)
            cout << t - h + 1 << '\n';
    }
    return 0;
}
```


