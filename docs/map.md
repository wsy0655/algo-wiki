## <center>map<center/>

### <center>概述<center/>

`map` 是有序键值对容器，它的元素的键是唯一的。查找、删除和插入操作拥有对数复杂度（$\log{n}$）。`map` 通常实现为 $\textbf{红黑树}$。

定义：map<键, 值> 变量名。其中键和值写对应的元素类型即可。例如：

* `map<int, int> mp`。定义一个键和值都是 `int` 类型的 `map`。
* `map<string, int> mp`。定义一个键是 `string`，值是 `int` 类型的 `map`。
* `map<vector<int>, int> mp`。定义一个键是 `vector<int>`，值是 `int` 类型的 `map`。
* `map<int, vector<int>> mp`。定义一个键是 `int`，值是 `vector<int>` 类型的 `map`。
* `map<int, map<int, int>> mp`。定义一个键是 `int`，值是 `map<int, int>` 类型的 `map`。

注意键必须是支持比较的类型。若使用自定义的结构体类型时，还需要自定义比较规则传入 `map`。

### <center>基础使用<center/>

以 `map<int, int> mp` 为例，设 $n$ 为 `map` 里存储的元素个数。

* 修改（创建）：和数组用法类似，执行 `mp[x] = y`。只要 $x$ 和 $y$ 均在 `int` 范围内即可。复杂度 $O(\log{n})$
* 查找（查找键）：使用 `count` 函数，`mp.count(x)`。返回值为 `true` 说明存在键 $x$，否则不存在。复杂度 $O(\log{n})$
* 删除（删除键）：使用 `erase` 函数，`mp.erase(x)`。$\textcolor{red}{前提键 x 存在}$，复杂度 $O(\log{n})$
* 键的个数：使用 `size` 函数。`mp.size()` 可以求出存了几个键（几个键值对）。
* 清空 `map`：使用函数 `clear` 完成 `mp.clear()`，可以清除 map 的所有键值对。

### <center>map 的遍历<center/>

以 `map<int, int> mp` 为例。

#### 法一

使用迭代器遍历。首先 `map<int, int> iterator:: it`。创建一个名为 `it` 的 `map<int, int>` 的迭代器。

```cpp
map<int, int> iterator:: it;
for (it = mp.begin(); it != mp.end(); it != mp.end())
{
    // 键是 it->first，值是 it->second
    cout << it->first << " " << it->second << "\n";
}
```

`->` 运算符： `->` 是一个成员访问运算符。对于迭代器，它用于访问迭代器所指向的元素的成员。

`map` 的键值对是一个 `pair` 对象，当我们使用迭代器遍历时，需要使用 `it->first` 和 `it->second` 分别获取键和值。

#### 法二

使用 auto 遍历。语法简单且推荐。

```cpp
for (auto it : mp)
{
    // 键是 it.first，值是 it.second
    cout << it.first << " " << it.second << "\n";
}
```

### <center>应用场景<center/>

通俗理解 `map` 就是一个下标是任意类型的 $\textbf{数组}$，通过以下例子体会 `map` 的使用场景。

* 输入 $n$ 个数字 $a_i$，请统计每个数字的出现次数。（$1\leq n,a_i\leq 10^5$）

答： 简单，开一个大小为 $10^5$ 的数组 `cnt`，执行 `cnt[x]++` 即可。

* 输入 $n$ 个数字 $a_i$，请统计每个数字的出现次数。（$1\leq n\leq 10^5$，$1\leq a_i\leq 10^9$）

答：开大小为 $10^9$ 的数组通常会 `MLE`。主要是由于只存储 $10^5$ 个数字，但数值范围过大，有很多位置根本不需要。如何解决？

定义 `map<int, int> cnt` 即可，如果存储 `long long` 范围内的数字就改为 `map<long long, int> cnt`。

* 输入 $n$ 个字符串 $s$，请统计每个字符串的出现次数。

答：定义 `map<string, int> cnt` 即可。

### <center>算法函数<center/>

由于 map 自动按照键排序，因此支持一些算法函数。

以 `map<int, int> mp` 为例。

* `lower_bound(x)`: 返回指向刚好大于或等于 $x$ 的 $\textcolor{red}{键的迭代器}$。

`mp.lower_bound(x)`。注意和 `vector` 和数组的使用方式区分。

返回值是迭代器，不存在返回 `mp.end()`。

```cpp
auto it = mp.lower_bound(x);
if (it != mp.end())
{
    cout << it->first << " " << it->second;
}
```

* `upper_bound(x)`: 返回指向刚好大于 $x$ 的 $\textcolor{red}{键的迭代器}$。用法同上

### <center>二维 map<center/>

* 方式一：`map<int, int> mp[100005]`。每一个 `mp[i]` 都是一个 map。例如：`mp[i][j] = k`。

$\textcolor{red}{mp[i] 指的是第 i 个 map，第 i 个 map 创建了一个键为j 其值是 k。}$

* 方式二：定义 `map<int, map<int, int>> mp` 即可。 $\textcolor{red}{键是 int，其对应的值又是一个 map，相当于是一个二维的 map}$

$\textcolor{red}{mp[i] 指的是键为 i，其对应的值又是一个 map，在值这个 map 里，键是 j，值是 k。}$

### <center>unordered_map<center/>

* `unordered_map` 是 `C++` 标准库（STL）中的一个关联容器，用于存储键值对。它基于哈希实现，$\textcolor{red}{内部元素不以任何特定顺序进行排序}$。它的大部分操作最好情况下的时间复杂度是常数级别的，但最坏情况下都是 $O(n)$ 。（产生哈希冲突）

其大部分操作和 map 相同，但由于键不排序所以无法使用二分函数。

以下操作 $\textcolor{red}{在平均情况下}$ 复杂度为常数级别，$\textcolor{red}{最坏情况下}$ 为 $O(n)$。

以 `unordered_map<int, int> mp` 为例。

* 创建或赋值：`mp[x] = y`，平均复杂度为 $O(1)$，最坏复杂度为 $O(n)$。
* 删除 `erase`：`mp.erase(x)`，删除键 $x$，复杂度同上。
* 查找键 `count`: `mp.count(x)`，判断键是否存在，复杂度同上。
* 其他函数：`size`（键的个数），`clear`（清空）等。

### <center>multimap<center/>

它和 `map` 的区别在于：`map` 的键是唯一的，而 `multimap` 键是可以重复的。即可以储存更多的信息。因此操作函数会有一些差别。

由于键依然排序，所以复杂度还是 $O(\log{n})$ 级别的。

* `insert`：插入键值对函数，例如 `mp.insert({1, 3})`，插入键是 $1$ 值是 $3$。不支持像 `map` 一样的赋值操作。
* `find`：查找键 $\textcolor{red}{第一次出现}$ 的位置，返回值为迭代器。
* `erase`：删除 键 或 删除迭代器。由于键可以重复，若传入键会删除所有的键，用时需谨慎。
* `count`：统计键的个数。
* 支持二分函数，`lower_bound` 和 `upper_bound`。

### <center>总结<center/>

`map` 就像是一个下标可以是任意类型的数组。掌握好 `map` 的使用很多题做起来都游刃有余。