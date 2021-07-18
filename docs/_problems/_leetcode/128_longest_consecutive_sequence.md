<!-- prettier-ignore-start -->

> 给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。<br>
> 请你设计并实现时间复杂度为 O(n) 的算法解决此问题。
<br><br>
Example1:
```
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```
Example2:
```
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```
Note:
>
-   0 <= nums.length <= 10^5
-   -10^9 <= nums[i] <= 10^9
>
> (Medium)

<!-- prettier-ignore-end -->

<br>

哈希表查找相邻数，如果不是连续序列的第一个数则跳过。实现时注意在去重后的集合上遍历。

??? adcodes "Solution"

    ```java
    public int longestConsecutive(int[] nums) {
        Set<Integer> integers = new HashSet<Integer>();
        for (int num : nums)
            integers.add(num);
        int maxLen = 0;
        for (int num : integers) {
            if (!integers.contains(num - 1)) {
                int i = 1, len = 1;
                while (integers.contains(num + i)) {
                    i += 1;
                    len += 1;
                }
                maxLen = Math.max(maxLen, len);
            }
        }
        return maxLen;
    }
    ```
