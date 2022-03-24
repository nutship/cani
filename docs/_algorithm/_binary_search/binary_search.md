### 1. 二分查找的种类

&emsp; <img src="../img/binary_search_cases.png" width=540>

上界的情况均可以通过下界的索引减一得到。

### 2. 下界二分查找

```py
def lower_bound(array, left, right, value):
    while left < right:
        mid = left + (right - left) // 2
        if array[mid] < value:
            left = mid + 1 # [mid + 1, right)
        else
            right = mid    # [left, mid)
    return left
```

#### 溢出问题

直接 `mid = (left + right) // 2` 可能导致溢出

#### 思路

完整理解 `lower_bound` 需要考虑以下方面

-   目标状态: &ensp; `left=right`，且 `[start,left)` 内元素 `< value`，`[right,end)` 内元素 `> value`，这样 `array[left]` 即为所求
-   区间跳转保持目标状态: &ensp; 跳转需要保持目标状态的不变式，考虑 `mid` 指针在 `array=[1,2,3,3,3,4,5]` 上滑动 (`value=3`)
    -   当 `array[mid] < value` 时，可以向右跳转，保持 `array[start...first) < value`
    -   else，可以向左跳转，保持 `array[last...end) >= value`
-   区间划分边界: &ensp; 考虑区间长为 2 或 3，需要保证划分的两个区间确实更小了，因此为 `[first,mid) + mid + [mid+1,last)`
-   元素不存在的情况: &ensp; 如果 `array` 的元素都小于 `value`，会不断向右跳转，直到 `first=last=end`

`upper_bound` 需要状态 `left <= value & right > value`，只需改条件为 `array[mid] <= value`

### 3. 二分总结

-   基本的二分考虑为上述四步: 目标状态、跳转可以保持目标状态、区间跳转的边界、元素不存在的情况
-   `upper_bound` 使用左闭右开的好处是方便表示空区间，很多时候两闭也会更方便
-   `upper_bound` 是保持区间两端的性质，普通的思路是保持区间的性质
