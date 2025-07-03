## <center>set</center>

### 概述

`set` 是一个关联式容器。其内部元素自动排序且去重（默认升序）。搜索、移除和插入拥有 $\log$ 级别的复杂度。

定义格式：`set<类型名> 变量名`。

常见示例：
- `set<int> s`
- `set<long long> s`
- `set<string> s`
- `set<pair<int, int>> s`

---

### 操作函数

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

---


### set 的遍历

以 `set<int> s` 为例，介绍两种常用遍历方法：

**使用迭代器遍历**

```cpp
for (set<int>::iterator it = s.begin(); it != s.end(); ++it)
{
    // *it 是当前元素
    cout << *it << " ";
}
```

- s.begin() 返回指向集合中最小元素的迭代器。
- s.end() 是指向集合尾部后一个位置的迭代器，不能解引用。
- 通过迭代器访问元素需要使用 *it。


**使用 auto 方法**


```cpp
for (auto x : s)
{
    cout << x << " ";
}
```


- 语法简单，适合大多数情况。
- 遍历顺序是有序的（默认升序）。


**降序的 set**

传入参数：`greater<>()` 即可，例如 `set<int, greater<>()> s;`


### 其余函数和技巧

- `s.find(x)`  
    - 判断集合里是否存在 $x$，返回迭代器，若不存在返回 `s.end()`。  
    - 时间复杂度：$O(\log{n})$

- `s.lower_bound(x)`  
    - 返回集合中第一个大于或等于 $x$ 的元素的迭代器，若不存在返回 `s.end()`。

- `s.upper_bound(x)`  
    - 返回集合中第一个大于 $x$ 的元素的迭代器，若不存在返回 `s.end()`。

- 获取最小值  

    ```cpp
    auto it = s.begin();
    if (it != s.end()) 
    {
        cout << *it; // 最小值
    }
    ```
- 获取最大值  

    ```cpp
    auto it = s.rbegin();
    if (it != s.rend()) 
    {
        cout << *it; // 最小值
    }
    ```

### multiset

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


### 其它类型的 set


- `unordered_set`：无序集合，元素去重但不排序。竞赛中较少使用。
- `unordered_multiset`：无序集合，允许重复元素，不排序。竞赛中较少使用。