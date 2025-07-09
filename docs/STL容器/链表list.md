# 📜 list 容器详解 

## 📌 概述 

`list` 是 C++ STL 中的序列式容器，底层采用的是 **双向链表** 结构，因此在插入、删除操作方面非常高效，尤其是在 **任意位置频繁插入或删除元素** 的场景下表现优异。不同于 `vector` 的连续内存结构，`list` 的元素不连续，访问元素效率较低。

## 🛠 基本用法 

```cpp
#include <list>
using namespace std;

list<int> l; // 创建一个空的 list 容器
```

## 📖 常用函数及操作 

| 函数名           | 功能描述                       | 使用示例               | 注意事项           |
| ---------------- | ------------------------------ | ---------------------- | ------------------ |
| `push_back(x)`   | 在尾部插入元素                 | `l.push_back(1);`      | 时间复杂度 O(1)    |
| `push_front(x)`  | 在头部插入元素                 | `l.push_front(2);`     | 时间复杂度 O(1)    |
| `pop_back()`     | 删除尾部元素                   | `l.pop_back();`        | list 不能为空      |
| `pop_front()`    | 删除头部元素                   | `l.pop_front();`       | list 不能为空      |
| `insert(pos, x)` | 在指定位置插入元素             | `l.insert(it, 5);`     | 传入的是迭代器位置 |
| `erase(pos)`     | 删除指定位置的元素             | `l.erase(it);`         | 迭代器失效要谨慎   |
| `clear()`        | 清空容器                       | `l.clear();`           |                    |
| `empty()`        | 判断是否为空                   | `l.empty();`           | 为空返回 true      |
| `size()`         | 获取元素个数                   | `l.size();`            |                    |
| `front()`        | 访问第一个元素                 | `l.front();`           | 不可为空           |
| `back()`         | 访问最后一个元素               | `l.back();`            | 不可为空           |
| `begin()`        | 获取指向第一个元素的迭代器     | `auto it = l.begin();` |                    |
| `end()`          | 获取指向末尾后一个位置的迭代器 | `auto it = l.end();`   |                    |
| `sort()`         | 排序                           | `l.sort();`            | 只能用成员函数排序 |
| `reverse()`      | 反转 list                      | `l.reverse();`         |                    |
| `unique()`       | 去重（仅相邻重复元素）         | `l.unique();`          | 需先 `sort()`      |

## 📌 示例代码 

```cpp
#include <iostream>
#include <list>
using namespace std;

int main() {
    list<int> l;

    l.push_back(1);     // 尾部插入
    l.push_front(2);    // 头部插入
    l.push_back(3);

    for (auto x : l) cout << x << " "; // 输出 2 1 3
    cout << endl;

    auto it = l.begin();
    advance(it, 1);     // 移动迭代器到第二个位置
    l.insert(it, 10);   // 插入到第二个位置前

    for (auto x : l) cout << x << " "; // 输出 2 10 1 3
    cout << endl;

    l.pop_front();
    l.pop_back();

    for (auto x : l) cout << x << " "; // 输出 10 1
    cout << endl;

    l.sort();           // 排序
    l.reverse();        // 反转

    for (auto x : l) cout << x << " "; // 输出 10 1
    cout << endl;

    return 0;
}
```

## ⚠️ 注意事项 

* `list` 不支持随机访问，不能使用 `[]` 访问元素。
* 所有插入/删除操作时间复杂度为 O(1)，而遍历是 O(n)。
* 与 `vector` 相比，它更适合频繁的插入和删除操作。
* 排序时只能使用 `list` 自带的 `sort()`，不能用 `std::sort()`。

## 💡 应用场景 

* 📥 实现 **LRU 缓存淘汰策略**（双向链表 + 哈希表）
* 🔄 **循环链表模拟**
* 📚 频繁插入与删除的场景（如编辑器、链表模拟）
* 📍 用于滑动窗口问题（与 deque 结合）

## ✅ 总结 

🌟 `list` 是一个非常适合处理链式数据结构的 STL 容器。掌握它的用法能够让你在一些特定题型中事半功倍！记得多练习，多用迭代器，写出又快又优雅的代码吧！🎯
