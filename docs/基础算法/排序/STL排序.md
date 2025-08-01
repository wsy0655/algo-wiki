## STL 中的排序函数

C++ 标准库 `<algorithm>` 提供高效的通用排序函数 `sort`，通常通过 **快速排序** 实现，在平均和最坏情况下均能达到 $O(n\log n)$ 的时间复杂度。

- 时间复杂度优于冒泡，选择，插入等排序。

---

## 用法概述


- **`sort(first, last)`**：对区间 $[\texttt{first}, \texttt{last})$ 内的元素进行排序。
    - 注意区间是左闭右开，不包括右端点。
- **`sort(first, last, comp)`**：对区间 $[\texttt{first}, \texttt{last})$ 内的元素进行排序，排序规则由 comp 指定。


- **参数**：
    - `first, last`：普通数组指针的开始与结束位置。
    - `comp`：可选的比较函数对象。
- **返回值**：无。直接修改排序对象本身。

### 一、对普通数组使用 `sort`

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() 
{
    int a[7] = {0, 5, 2, 9, 1, 5, 6};
    int n = 6;

    // 升序排序
    sort(a + 1, a + n + 1);
    cout << "升序：";
    for (int i = 1; i <= n; i++) cout << a[i] << ' ';
    cout << endl;

    // 降序排序
    sort(a + 1, a + n + 1, greater<int>());
    cout << "降序：";
    for (int i = 1; i <= n; i++) cout << a[i] << ' ';

    return 0;
}
```

> 如果你的数组是 `a[0]` $\sim$ `a[n - 1]` 存储，则改为 `sort(a, a + n)`。


---

### 二、对 `string` 使用 `sort`

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() 
{
    string s = "sorting";

    // 将字符串按字符升序排序
    sort(s.begin(), s.end());
    cout << "字符升序：" << s << endl;  // 输出：ginorst

    // 将字符串按字符降序排序
    sort(s.begin(), s.end(), greater<char>());
    cout << "字符降序：" << s << endl;  // 输出：tsroing

    return 0;
}
```

* `string` 支持随机访问迭代器，可直接调用 `sort`。
* 利用 `greater<char>()` 或自定义比较器，可灵活调整排序顺序。

---

### 三、注意事项

- **稳定排序需求**：若需保持相等元素相对顺序，可使用 `stable_sort`。
- **就地排序**：`sort` 就地直接修改排序对象本身，没有返回值。

---
