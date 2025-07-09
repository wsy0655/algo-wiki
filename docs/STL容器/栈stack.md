# 📦 栈（Stack）

## 📘 引入

![](https://oi-wiki.org/ds/images/stack.svg)

栈（Stack）是 OI 和日常编程中常见的一种线性数据结构，其特点是 **先进后出（LIFO）**。

想象一下手枪弹夹上弹的过程，先压入的子弹在最底部，最后压入的子弹最先打出🔫。


---

## 🛠️ 实现方式

### 📐 数组模拟栈

```cpp
int stk[N];
int top = 0; // 栈顶下标，top = 0 表示空栈
```

- 加入元素：`stk[++top] = x;`
- 弹出栈顶：`if (top > 0) top--;`
- 查看栈顶：`if (top > 0) cout << stk[top];`
- 获取栈大小：`cout << top;`
- 清空栈：`top = 0;`


✅ **优点**：可以直接访问任意位置，清空快，效率高。


**模板题代码实现**

[点我跳转模板题](http://www.tarjanoj.com/d/tarjan24/p/1105)


```cpp
#include <bits/stdc++.h>
#define ull unsigned long long
#define ll long long
using namespace std;
ull stk[1000005];
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0); // 不要用 endl，换行用 \n 
    int t;
    cin >> t;
    while (t--)
    {
    	int n;
		cin >> n;
		int top = 0; // 代表栈顶，也起到了多组数据清空栈的作用  
		for (int i = 1; i <= n; i++)
		{
			string op;
			cin >> op;
			if (op == "push")
			{
				ull x;
				cin >> x;
				stk[++top] = x;
			} 
			if (op == "pop")
			{
				if (top > 0) top--; // 删除栈顶
				else cout << "Empty\n"; 
			}
			if (op == "query")
			{
				if (top > 0) cout << stk[top] << "\n";
				else cout << "Anguei!\n";
			}
			if (op == "size")
			{
				cout << top << "\n";
			}
		} 
	}
    return 0;
}
```

---

### 🧰 STL `stack` 实现

```cpp
#include <stack>
stack<int> stk;
```

| 函数      | 作用         | 示例           | 注意事项                                                 |
| --------- | ------------ | -------------- | -------------------------------------------------------- |
| `push(x)` | 入栈         | `stk.push(5);` | 函数无返回值。                                           |
| `pop()`   | 出栈         | `stk.pop();`   | 函数无返回值。保证栈不为空才能使用，否则 `RE`。          |
| `top()`   | 获取栈顶元素 | `stk.top();`   | 返回值取决于栈的类型。 保证栈不为空才能使用，否则 `RE`。 |
| `size()`  | 获取大小     | `stk.size();`  | 返回值为 `unsigned int` 类型。                           |
| `empty()` | 判空         | `stk.empty();` | 返回值为 `bool`，`true` 代表空，`false` 代表不空。       |

🧹 清空队列：

```cpp
while (!stk.empty()) stk.pop();
```


**模板题代码实现**

[点我跳转模板题](http://www.tarjanoj.com/d/tarjan24/p/1105)

```cpp
#include <bits/stdc++.h>
#define ull unsigned long long
#define ll long long
using namespace std;

int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0); // 不要用 endl，换行用 \n 
    int t;
    cin >> t;
    while (t--)
    {
    	int n;
		cin >> n;
		stack<ull> stk;
		for (int i = 1; i <= n; i++)
		{
			string op;
			cin >> op;
			if (op == "push")
			{
				ull x;
				cin >> x;
				stk.push(x);
			} 
			if (op == "pop")
			{
				if (!stk.empty()) stk.pop(); // 删除栈顶
				else cout << "Empty\n"; 
			}
			if (op == "query")
			{
				if (!stk.empty()) cout << stk.top() << "\n";
				else cout << "Anguei!\n";
			}
			if (op == "size")
			{
				cout << stk.size() << "\n";
			}
		} 
	}
    return 0;
}
```


🆚 **对比**：

- 数组模拟效率更高；
- STL 使用更方便、易读；
- 实战中根据习惯选择即可。

---

## ✅ 栈的应用场景

| 应用场景             | 简介                                   |
| -------------------- | -------------------------------------- |
| ✅ **括号匹配**       | 判断括号是否成对出现、嵌套是否合法     |
| ✅ **表达式求值**     | 解析中缀、后缀、前缀表达式             |
| ✅ **单调栈**         | 寻找某一元素左/右第一个比它大/小的元素 |
| ✅ **下一个更大元素** | 每个元素右边第一个更大的数             |
| ✅ **最小栈**         | 实现支持 `getMin()` 的栈               |
| ✅ **DFS 非递归**     | 用栈模拟函数递归过程                   |

---

### 🔁 括号匹配算法（示例）


**✅ 合法格式:**

* `()()`, `(())` 等是合法的
* `)(`, `())` 是不合法的

**✏ 运行规则:**

1. 遍历字符串
2. `(` 压入栈
3. `)` 检查栈顶：如果不为空，`pop()`；否则 `Wrong`
4. 最后检查栈是否为空

```cpp
for (char x : s) 
{
    if (x == '(') stk.push(x);
    else 
    {
        if (stk.empty()) 
        {
            cout << "Wrong";
            break;
        } 
        else 
        {
            stk.pop();
        }
    }
}
if (!stk.empty()) cout << "Wrong";
```

---

### 🔣 表达式求值（后缀）

**◀️ 后缀表达式 (Reverse Polish Notation)**

如：

```
a + b * c * d + (e - f) * (g * h + i)
=> a b c * d * + e f - g h * i + * +
```

**计算逻辑 🧮**

- 遇到数字入栈；
- 遇到运算符，取出两个数计算 `b op a`；
- 将结果重新入栈；
- 最终栈顶即为结果。

**⚠ 注意：**

* 先出栈的是 a，后出的是 b，如 3-2 必须计算 3-2，而不是 2-3

**代码片段 📜**

```cpp
void eval(char op) 
{
    int a = stk.top(); stk.pop();
    int b = stk.top(); stk.pop();
    if (op == '+') stk.push(b + a);
    if (op == '-') stk.push(b - a);
    if (op == '*') stk.push(b * a);
    if (op == '/') stk.push(b / a);
}
```

**多位数处理技巧 ✌️**

```cpp
for (int i = 0; i < s.size(); i++) 
{
    if (isdigit(s[i])) 
    {
        int j = i, sum = 0;
        while (j < s.size() && isdigit(s[j])) 
        {
            sum = sum * 10 + s[j] - '0';
            j++;
        }
        stk.push(sum);
        i = j - 1;
    }
    else if (s[i] == '+' || s[i] == '-' || s[i] == '*') 
    {
        eval(s[i]);
    }
}
```

---

### 🧪 验证栈出栈序列是否合法


模拟入栈过程，同时判断是否能按顺序出栈

**▶ 思路:**

1. 为了让 b\[j] 出现在栈顶，将 a\[i] 按顺序入栈
2. 如果 `stk.top() == b[j]`，则 pop，同时 j++
3. 最后 j == n+1 即为合法

模拟栈操作：

```cpp
int j = 1;
for (int i = 1; i <= n; i++) {
    stk.push(a[i]);
    while (!stk.empty() && stk.top() == b[j]) {
        stk.pop();
        j++;
    }
}
if (j == n + 1) cout << "YES"; else cout << "NO";
```

---

## 📚 推荐题单

| 平台       | 题目                           | 链接                                               |
| ---------- | ------------------------------ | -------------------------------------------------- |
| 洛谷       | P2201 数列编辑器               | 🔗 https://www.luogu.com.cn/problem/P2201           |
| Codeforces | 1073B - Vasya and Books        | 🔗 https://codeforces.com/problemset/problem/1073/B |
| Codeforces | 821C - Okabe and Boxes         | 🔗 https://codeforces.com/problemset/problem/821/C  |
| Codeforces | 26B - Regular Bracket Sequence | 🔗 https://codeforces.com/problemset/problem/26/B   |
| 上海月赛   | 括号计分                       | 🔗 https://iai.sh.cn/problem/679                    |

---

🧠 **提示**：多做题目，多手写模拟，有助于深入理解栈的结构特性。

---

🎉 完整栈学习笔记到此结束！祝你 AC 不停，代码飞起 🚀🚀🚀