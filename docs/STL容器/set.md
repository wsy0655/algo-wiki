

### 一、概述

`set` 是关联容器，含有键值类型对象的已排序集，搜索、移除和插入拥有对数复杂度。`set` 内部通常采用 **红黑树** 实现。平衡二叉树 的特性使得 `set` 非常适合处理需要同时兼顾查找、插入与删除的情况。


- 升序排序；
- 插入、删除、查找复杂度为 $O(\log n)$。

定义方式：

```cpp
set<value_type> s;
```

**常见示例：**

```cpp
set<int> s;
set<long long> s;
set<string> s;
set<pair<int, int>> s;
set<int> s[100005]; // 100005 个 set
```

**降序的 set**

传入参数：`greater<>()` 即可，例如 `set<int, greater<>()> s;`

**📌 注意：** 类型必须支持 $<$ 比较运算符。若使用自定义类型作键，需自定义比较函数。

---


### 二、基础操作

以下操作以 `set<int> s` 为例。

以 `set<int> s` 为例：

- `s.insert(x)`  
    - 当容器中没有等价元素时，将元素 $x$ 插入到 `set` 中。  
    - 时间复杂度：$O(\log{n})$

- `s.erase(x)`  
    - 删除值为 $x$ 的元素。  
    - 时间复杂度：$O(\log{n})$

- `s.erase(pos)`  
    - 删除迭代器为 $pos$ 的元素，迭代器必须存在，否则会导致运行错误（RE）。  
    - 时间复杂度：$O(\log{n})$

- `s.erase(first, last)`  
    - 删除迭代器范围 $[first, last)$ 内的所有元素，不包括 `last` 所指元素。

- `s.clear()`  
    - 清空 `set`。

> **注意**：涉及迭代器的操作，如 `s.erase(s.begin())`，删除集合最小值，若集合为空，会导致运行错误（RE），务必先判断集合是否为空。



| 操作   | 示例           | 说明                                               | 复杂度      |
| ------ | -------------- | -------------------------------------------------- | ----------- |
| 插入   | `s.insert(x);` | 若 `x` 不存在则插入                                | $O(\log n)$ |
| 存在性 | `s.count(x)`   | 存在返回 1，不存在返回 0                           | $O(\log n)$ |
| 查找   | `s.find(x)`    | 存在返回 迭代器，不存在返回 `s.end()`              | $O(\log n)$ |
| 删除   | `s.erase(x);`  | 删除值为 `x` 的元素，允许不存在 $x$                | $O(\log n)$ |
| 删除   | `s.erase(it);` | 删除迭代器为 `it` 对应的的元素，要求迭代器必须存在 | $O(\log n)$ |
| 大小   | `s.size();`    | 返回元素数量                                       | $O(1)$      |
| 清空   | `s.clear();`   | 清空所有元素                                       | $O(n)$      |

---




### 三、遍历方式

以 `set<int> s` 为例，介绍两种常用遍历方法：

**方法一：传统迭代器写法**

```cpp
for (set<int>::iterator it = s.begin(); it != s.end(); ++it)
{
    // *it 是当前元素
    cout << *it << " ";
}
```

- s.begin() 返回指向集合中最小元素的迭代器。
- s.end() 是指向集合尾部后一个位置的迭代器，不能解引用。
- 通过迭代器访问元素需要使用 `*it`。相当于解引用。


**方法二：使用`auto`**


```cpp
for (auto x : s)
{
    cout << x << " ";
}
```


- 语法简单，适合大多数情况。
- 遍历顺序是有序的（默认升序）。




### 四、其余函数和技巧

- `s.find(x)`  
    - 判断集合里是否存在 $x$，返回迭代器，若不存在返回 `s.end()`。  
    - 时间复杂度：$O(\log{n})$

- `s.lower_bound(x)`  
    - 返回集合中第一个大于或等于 $x$ 的元素的迭代器，若不存在返回 `s.end()`。

- `s.upper_bound(x)`  
    - 返回集合中第一个大于 $x$ 的元素的迭代器，若不存在返回 `s.end()`。

- 获取最小值  

    ```cpp
    if (!s.empty()) 
    {
        cout << *s.begin(); // 最小值
    }
    ```
    
- 获取最大值  

    ```cpp
    // 法一
    if (!s.empty()) 
    {
        auto it = s.end();
        it--;
        cout << *it; // 最小值
    }
    // 法二
    if (!s.empty()) 
    {
        auto it = s.rbegin();
        cout << *it;
    }
    ```

- 删除最小值

    ```cpp
    if (!s.empty()) 
        s.erase(s.begin());
    ```
    
- 删除最大值

    ```cpp
    if (!s.empty()) 
    {
        auto it = s.end();
        it--;
        s.erase(it);
    }
    ```

#### 复杂类型的使用

以 `set<pair<int, int>> s` 为例：

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    set<pair<int, int>> s;
    s.insert({1, 2});
    s.insert({3, 4});
    s.insert({5, 6});

    s.erase({1, 2}); // 按照值删除

    auto it = s.find({1, 2}); 
    if (it != s.end()) s.erase(it); // 按照迭代器删除
    // 遍历 pair<int, int> 类型的 set 
    for (auto it : s)
    {
        cout << it.first << " " << it.second << "\n";	
    } 

    // 获取最小值

    if (!s.empty())
    {
        auto it = s.begin();
        cout << (*it).first << " " << (*it).second << "\n";
    } 
    return 0;
}
```



**模板题代码**

[点我跳转模板题](http://www.tarjanoj.com/d/tarjan24/p/180)


```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    set<int> s;
    int q;
    cin >> q;
    while (q--)
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int x;
            cin >> x;
            s.insert(x);
        }
        if (op == 2)
        {
            int x;
            cin >> x;
            s.erase(x);
        }
        if (op == 3)
        {
            int x, y;
            cin >> x >> y;
            vector<int> temp;
            for (auto val : s)
                if (val >= x && val <= y)
                    temp.push_back(val);
            for (auto val : temp)
                s.erase(val);
        }
        if (op == 4)
        {
            if (s.empty()) cout << "-1\n";
            else cout << *s.begin() << "\n"; 
        }
    } 
    return 0;
}
```

### 五、multiset

`multiset` 是一个关联式容器，内部元素自动排序（默认升序），**允许重复元素**。搜索、移除和插入操作的时间复杂度均为 $\log$ 级别。

定义格式：`multiset<类型名> 变量名`。

以 `multiset<int> s` 为例：

- 插入和查找操作与 `set` 相同。

- 删除操作区别：

    - 按值删除：`s.erase(x)` 会删除所有值为 $x$ 的元素。

    - 按迭代器删除：只会删除迭代器指向的一个元素。

```cpp
auto it = s.find(x); // 找到第一个值为 x 的元素的迭代器
if (it != s.end()) s.erase(it); // 删除这个元素，若不存在则不操作
```


### 六、其它类型的 set


- `unordered_set`：无序集合，元素去重但不排序。竞赛中较少使用。
- `unordered_multiset`：无序集合，允许重复元素，不排序。竞赛中较少使用。