## <center>set<center/>

### <center>概述<center/>

`set` 是一个关联式容器。其内部元素自动排序且去重（默认升序）。搜索、移除和插入拥有 $\log$ 级别的复杂度。

set<类型名> 变量名。

* `set<int> s, set<long long> s`
* `set<string> s, set<pair<int, int>> s`

### <center>操作函数<center/>

以 `set<int> s` 为例。

* `s.insert(x)` 当容器中没有等价元素的时候，将元素 $x$ 插入到 `set` 中。时间复杂度 $O(\log{n})$
* `s.erase(x)` 删除值为 $x$ 的元素。时间复杂度 $O(\log{n})$
* `s.erase(pos)` 删除迭代器为 $pos$ 的元素，要求迭代器必须存在，否则 `RE`。时间复杂度 $O(\log{n})$
* `s.erase(first,last)` 删除迭代器在 $[first,last)$ 范围内的所有元素。（不包括迭代器 `last`）
* `s.clear()` 清空 `set`。

注意一些和迭代器有关的操作先判断是否存在该迭代器在操作，例如 `s.erase(s.begin())` 相当于删除集合的最小值。若集合为空这是一个会造成 `RE` 的操作。

### <center>其余函数和技巧<center/>

* `find` 函数，`s.find(x)` 判断集合里是否存在 $x$，返回值为迭代器，不存在返回 `s.end()`。时间复杂度 $O(\log{n})$
* `lower_bound` 函数，`s.lower_bound(x)` 找到集合里首个大于或等于元素 $x$ 的迭代器，不存在返回 `s.end()`。
* `upper_bound` 同上，找到首个大于 $x$ 的迭代器。
* 获取最小值：`auto it = s.begin()`。

注意 `it` 是一个迭代器。通过 `*it` 解引用获取迭代器对应的数值。

* 获取最大值：`auto it = s.rbegin()`，利用 `rbegin` 逆迭代器即可。

注意一些和迭代器有关的操作先判断是否存在该迭代器在操作，例如 `s.erase(s.begin())` 相当于删除集合的最小值。若集合为空这是一个会造成 `RE` 的操作。

### <center>multiset<center/>

`multiset` 是一个关联式容器。其内部元素自动排序（默认升序）$\textcolor{red}{不去重}$。搜索、移除和插入拥有 $\log$ 级别的复杂度。

multiset<类型名> 变量名。下面以 `multiset<int> s` 为例。

* 插入和二分等函数都没有区别。
* 注意删除函数的差异。

按值删除：`s.erase(x)`，会删除所有的 $x$。

按迭代器删除：只会删除迭代器这个位置的元素。

```cpp
auto it = s.find(x); // 找到第一个 x 的迭代器
if (it != s.end()) s.erase(it); // 不存在别删除
```

### <center>其它类型的 set<center/>

* `unordered_set`：无序集合，元素去重但不排序。竞赛中不常用。
* `unordered_multiset`：无序可重集合，元素不去重也不排序。竞赛中不常用。