### 1. 二分查找的种类

&emsp; <img src="../img/binary_search_cases.png" width=540>

上界的情况均可以通过下界的索引减一得到。

### 2. 下界二分查找

```py
def lower_bound(array, first, last, value):
    while first < last:
        mid = first + (last - first) // 2
        if array[mid] < value:
            first = mid + 1 # [mid + 1, last)
        else
            last = mid      [first, mid)
    return first
```

#### 溢出问题

直接 `mid = (first + last) // 2` 可能导致溢出

#### 思路

完整理解 `lower_bound` 需要考虑以下方面

-   目标状态: &ensp; `first=last`，且 `[start,first)` 内元素 `< value`，`[first,end)` 内元素 `> value`，这样 `array[first]` 即为所求
-   区间划分: &ensp; 需要保证划分的两个区间确实更小了，因此为 `[first,mid) + mid + [mid+1,last)` (考虑边界 case `[0,1)`)
-   区间跳转: &ensp; 跳转需要保持目标状态的不变式，考虑 `mid` 指针在 `array=[1,2,3,3,3,4,5]` 上滑动 (`value=3`)
    -   当 `array[mid] < value` 时，可以向右跳转，保持 `array[start...first) < value`
    -   else，可以向左跳转，保持 `array[last...end) >= value`
-   边界情况: &ensp; 如果 `array` 的元素都小于 `value`，会不断向右跳转，直到 `first=last=end`

`upper_bound` 需要状态 `left <= value & right > value`，只需改条件为 `array[mid] <= value`
