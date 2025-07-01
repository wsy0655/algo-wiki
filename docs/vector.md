
## <center>vector<center/>

### <center>概述<center/>

`vector` 是 STL 提供的 内存连续的、可变长度 的数组（亦称列表）数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问。

### <center>定义和初始化<center/>

使用方法为 vector<元素类型> 变量名; 使用前记得导入头文件 `<vector>` 或万能头文件。

注意以下内容为定义时的初始化。

* `vector<int> a` 就定义了一个名为 $a$ 元素类型均为 `int` 的 `vector`。
* `vector<int> a(10)` 初始化一个长度为 $10$ 的 `vector`，由于 `vector` 支持随机访问，我们可以像使用数组一样，通过 $a[0]$ 到 $a[9]$ 来访问或修改其中的元素。
* `vector<int> a(10, 3)` 初始化一个长度为 $10$ 且元素都为 $3$ 的 `vector`。

### <center>常用函数介绍<center/>

* 向尾部添加元素：`push_back()`，向 `vector` 的尾部添加一个元素，例如 `a.push_back(5)`。
* 删除尾部元素：`pop_back()`，删除尾部元素，前提是 `vector` 不为空。
* 大小：`size()`，`a.size()` 返回 `vector` 的元素个数。
* 清空 `vector`：`clear()`，`a.clear()` 清空 `vector` 的元素。
* 获取尾部元素：`a.back()`，返回尾部元素，前提是 `vector` 不为空。该方法也可以用 `a[a.size() - 1]` 代替。
* 获取头部元素：`a.front()`，返回头部元素，前提是 `vector` 不为空，该方法也可以用 `a[0]` 代替。

### <center>其余强大函数<center/>

* 初始化函数：`assign`，`a.assign(4, 2)` 初始化 $a$ 的长度为 $4$，元素均为 $2$。该函数用于初始化之后想重置的时候。
* 交换函数：`a.swap(b)`，交换两个 `vector` 的元素。
* 支持比较运算符，例如 `a == b` 可以判断两个 `vector` 是否相等。相等指的就是长度相等且每一位的内容都一致。
* 同时还有一些插入删除函数，感兴趣可以自己了解。

### <center>迭代器简述<center/>

迭代器（Iterator）是 C++ STL（标准模板库）中用来遍历容器元素的工具。可以将迭代器想象成指向容器元素的指针。迭代器支持多种操作，包括：

例如创建了一个名为 it 的迭代器变量。

* 解引用 `*it` : 获取迭代器指向的元素。
* 访问容器的开始和结束 `begin()` 和 `end()` : 获取指向容器开始和结束位置的迭代器。

注意以下两个操作确保不会接触到迭代器的末尾或起始之前。

* 前进 `++it` 或 `it++` : 将迭代器移动到下一个元素。也可以用 `next(it)`，确保下一个迭代器不是 `end()`
* 后退 `--it` 或 `it--` : 将迭代器移动到前一个元素（对于双向迭代器）。也可以用 `prev(it)`，确保上一个不能小过 `begin()`。

### <center>vector 内使用迭代器的函数举例<center/>

vector 是一个非常常用的容器，它通过提供快速随机访问和简单的迭代器接口，使得处理动态数组变得方便。通过理解迭代器和容器的使用，可以更加高效地进行 C++ 编程。

* `sort(a.begin(), a.end())` 可以实现将 `vector` 的元素从小到大排序。
* `sort(a.begin() + l, a.begin() + r + 1)` 可以实现将 `vector` 的 $a_l\sim a_r$ 进行排序。注意下标从 $0$ 开始。
* `sort(a.begin(), a.end(), greater<>())` 可以实现将 `vector` 的元素从大到小排序。
* `int pos = lower_bound(a.begin(), a.end(), x) - a.begin();` 含义是在 $a$ 中二分找到第一个大于等于 $x$ 的下标并返回给变量 $pos$。不存在返回的值是 `a.size()`。（下标范围是 $0\sim a.size() - 1$）

也可以 `auto pos = lower_bound(a.begin(), a.end(), x)` 通过 `*it` 解引用获取具体的值。不存在 `pos` 等于 `a.end()`。

* `int pos = upper_bound(a.begin(), a.end(), x) - a.begin();` 含义是在 $a$ 中二分找到第一个大于等于 $x$ 的下标并返回给变量 $pos$。不存在返回的值是 a.size()。（下标范围是 $0\sim a.size() - 1$）。迭代器访问同上。
* 翻转函数：`reverse(a.begin(), a.end())` 翻转整个 `vector`
* 排列函数：`next_permutation(a.begin(), a.end())`

如果只对某一区间操作，自己修改迭代器范围即可。

### <center>遍历 vector<center/>

* 法一，通过下标

```cpp
vector<int> = {1, 2, 3};
for (int i = 0; i < a.size(); i++) cout << a[i] << " ";
```

* 法二，通过 `auto`

auto 是 C++11 引入的一个关键字，用于自动推断变量的类型。

让编译器根据变量的初始化表达式自动推断类型。例如：\\

```cpp
auto x = 10; // x 是 int
auto y = 3.14; // y 是 double
```

对于遍历容器（如 vector、map、set、string、数组等），auto 可以简化代码：

```cpp
vector<int> = {1, 2, 3};
for (auto x : a) cout << x << " ";

string s = "hello";
for (auto x : s) cout << x << " ";
```

### <center>vector 的优点<center/>

* 它可以动态分配内存。且相比于普通数组支持更多的函数去操作，从某种意义上会方便一些，实现常数略大于数组。
* 动态分配内存指的是，当我们定义 `vector<int> a`，此时并未占用空间，随着不断的 `push_back()` 加入元素，才会随之分配空间。

### <center>二维 vector<center/>

二维 `vector` 有以下两种常见定义方式。

* 在定义时使用 `vector<int> a[105]` 这样，相当于直接定义了 $105$ 个 `vector`，来实现二维的目的。

每一个 `a[i]` 都是一个 `vector`，访问第 $i$ 个 `vector` 的第 $j$ 个元素（假设存在）

使用 `a[i][j - 1]` 来访问。这种定义方法需要预先开好 `vector` 的个数，类似于数组开大小。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e5 + 5; 
vector<int> a[N]; // 直接开 N 个 vector，相当于二维数组。
int main()
{
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
        a[i].push_back(n);
    return 0;
}
```

* 输入完 $n$ 以后再进行定义，使用 `vector<vector<int>> a(n)`，这样 $a[0]$ 到 $a[n-1]$ 都是一个 `vector`。

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int n;
    cin >> n;
    // 开 n 个 vector，编号为 1 到 n
    // vector 的类型是 vector 也是一个二维的
    vector<vector<int>> a(n + 1);
    for (int i = 1; i <= n; i++)
        a[i].push_back(n);
    return 0;
}
```

* 初始化一个 $n$ 行 $m$ 列的 二维 vector

```cpp
int n, m;
cin >> n >> m;
vector<vector<int>> a(n, vector<int>(m, 0));
for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        cin >> a[i][j];
```

### <center>额外的插入函数 emplace_back<center/>

`emplace_back` 和 `push_back` 都是 C++ 标准库 vector 的成员函数，用于向 vector 的末尾添加元素。

* 使用 `emplace_back` 时，元素在向量内部直接构造，避免了额外的复制或移动操作。
* 对于复杂类型（如自定义类），`emplace_back` 可以提高性能，因为它减少了不必要的对象拷贝或移动。
* 总结来说：`emplace_back` 的性能会更优一些。