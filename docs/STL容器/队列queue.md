# 🌟 队列（Queue）使用手册

## 📌 概述


![](https://oi-wiki.org/ds/images/queue.svg)

队列（`queue`）是一种**先进先出**（FIFO, First In First Out）的线性数据结构。简单地说：**谁先来，谁先走！** ✅

👀 类比：

- 排队买奶茶，先到的人先买完走。
- 打印任务，先提交的先打印。

---

## 🧱 实现方式

队列的实现方式主要有两种：

1. 数组模拟队列 🧮
2. STL 的 `queue`（标准模板库）📦

---

## 🧮 数组模拟队列

> 使用数组 + 双指针（头指针 `h`、尾指针 `t`）模拟队列操作。

```cpp
const int N = 100010;
int q[N];
int h = 1, t = 0; // 初始状态，队列为空
```

📌 操作说明：

|   操作   |      代码       | 说明               |
| :------: | :-------------: | :----------------- |
| 插入元素 |  `q[++t] = x;`  | 将元素加入队尾     |
| 删除队头 |     `h++;`      | 队头元素出队       |
| 获取队头 |     `q[h];`     | 查看队头元素       |
| 获取队尾 |     `q[t];`     | 查看队尾元素       |
| 队列大小 |  `t - h + 1;`   | 当前队列中元素个数 |
| 清空队列 | `h = 1, t = 0;` | 恢复初始状态       |


**代码实现**


```cpp
#include <bits/stdc++.h>

using namespace std;
const int N = 1e4 + 5;
int q[N], h = 1, t = 0, n; 
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0); // 不要用 endl，换行用 \n 
    cin >> n;
    while (n--)
    {
    	int op, x;
		cin >> op;
		if (op == 1)
		{
			cin >> x;
			q[++t] = x;
		}
		if (op == 2)
		{
			if (h <= t) h++;
			else cout << "ERR_CANNOT_POP\n";
		}
		if (op == 3)
		{
			if (h <= t) cout << q[h] << "\n";
			else cout << "ERR_CANNOT_QUERY\n";
		} 
		if (op == 4)
		{
			cout << t - h + 1 << "\n";
		}
	}
    return 0;
}
```

---

## 📦 STL 的 `queue`

> 适合更灵活开发，调用简单，功能强大。

```cpp
#include <queue>
using namespace std;
queue<int> q;
```

🔧 常用操作：

| 函数        | 说明                       | 注意事项                                                     |
| ----------- | -------------------------- | ------------------------------------------------------------ |
| `q.push(x)` | 插入元素到队尾             | 函数无返回值。                                               |
| `q.pop()`   | 删除队头元素（需保证非空） | 函数无返回值。保证队列不为空才能使用，否则 `RE`。            |
| `q.front()` | 查看队首元素               | 返回值取决于队列的类型。 保证队列不为空才能使用，否则 `RE`。 |
| `q.back()`  | 查看队尾元素               | 返回值取决于队列的类型。 保证队列不为空才能使用，否则 `RE`。 |
| `q.size()`  | 获取队列大小               | 返回值为 `unsigned int` 类型。                               |
| `q.empty()` | 检查队列是否为空           | 返回值为 `bool`，`true` 代表空，`false` 代表不空。           |




🧹 清空队列：

```cpp
while (!q.empty()) q.pop();
```



**模板题代码实现**


[点我跳转模板题](http://www.tarjanoj.com/d/tarjan24/p/1106)

```cpp
#include <bits/stdc++.h>
using namespace std;
int n; 
queue<int> q;
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0); // 不要用 endl，换行用 \n 
    cin >> n;
    while (n--)
    {
    	int op, x;
		cin >> op;
		if (op == 1)
		{
			cin >> x;
			q.push(x);
		}
		if (op == 2)
		{
			if (!q.empty()) q.pop();
			else cout << "ERR_CANNOT_POP\n";
		}
		if (op == 3)
		{
			if (!q.empty()) cout << q.front() << "\n";
			else cout << "ERR_CANNOT_QUERY\n";
		} 
		if (op == 4)
		{
			cout << q.size() << "\n";
		}
	}
    return 0;
}
```

---

## ♻️ 双端队列 Deque

> 可在两端进行插入与删除的「双开口」队列。既是栈也是队列的结合体！

```cpp
#include <deque>
deque<int> dq;
```

⚙️ 常用操作：

| 函数               | 说明                     | 注意事项                                                     |
| ------------------ | ------------------------ | ------------------------------------------------------------ |
| `dq.push_front(x)` | 从队首插入元素           |                                                              |
| `dq.push_back(x)`  | 从队尾插入元素           |                                                              |
| `dq.pop_front()`   | 从队首删除元素（需非空） | 注意保证队列不为空                                           |
| `dq.pop_back()`    | 从队尾删除元素（需非空） | 注意保证队列不为空                                           |
| `dq.front()`       | 查看队首元素             | 返回值取决于队列的类型。 保证队列不为空才能使用，否则 `RE`。 |
| `dq.back()`        | 查看队尾元素             | 返回值取决于队列的类型。 保证队列不为空才能使用，否则 `RE`。 |
| `dq.size()`        | 获取队列大小             | 队列大小为 `unsigned int` 类型。                             |
| `dq.empty()`       | 检查队列是否为空         | 队列为空返回 `true`，否则返回 `false`。                      |


🚨 注意：`deque` 常数较大，占用内存大。避免开太多！

---

**模板题代码实现**

[点我跳转模板题](http://www.tarjanoj.com/d/tarjan24/p/171)


```cpp
#include <bits/stdc++.h>
using namespace std;
deque<int> dq;
int n;
int main()
{
    cin >> n;
    while (n--)
    {
        int x, op;
        cin >> op;
        if (op == 1)
        {

            cin >> x;
            dq.push_back(x);
        }
        if (op == 2)
        {
            cin >> x;
            dq.push_front(x);
        }
        if (op == 3)
        {
            if (dq.empty())
                cout << "ERR_CANNOT_POP\n";
            else
                dq.pop_front();
        }
        if (op == 4)
        {
            if (dq.empty())
                cout << "ERR_CANNOT_POP\n";
            else
                dq.pop_back();
        }
        if (op == 5)
        {
            if (dq.empty())
                cout << "ERR_CANNOT_QUERY\n";
            else
                cout << dq.front() << "\n";
        }
        if (op == 6)
        {
            if (dq.empty())
                cout << "ERR_CANNOT_QUERY\n";
            else
                cout << dq.back() << "\n";
        }
        if (op == 7)
            cout << dq.size() << "\n";
    }
}
```



🔚 队列是算法竞赛与程序开发中不可或缺的工具，掌握它，让你逻辑更清晰，代码更优雅！✨