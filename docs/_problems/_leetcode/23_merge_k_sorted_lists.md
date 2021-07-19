<!-- prettier-ignore-start -->

> 给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。
> <br><br>
Example1:
```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```
Example2:
```
输入：lists = []
输出：[]
```
Example3:
```
输入：lists = [[]]
输出：[]
```
Note:
>
-   k == lists.length, 0 <= k <= 10^4
-   0 <= lists[i].length <= 500, -10^4 <= lists[i][j] <= 10^4
>
> (Hard)

<!-- prettier-ignore-end -->

<br>

相当于给定了归并排序的一个中间状态，继续二路归并即可，熟悉归并排序会非常好写

??? adcodes "Solution"

    ```java
    class Solution {
        public ListNode mergeKLists(ListNode[] lists) {
            int validLength = lists.length;
            if (validLength == 0)
                return null;
            while (validLength > 1) {
                int t = validLength, i, j;
                for (i = 0, j = 0; i < t - 1; i += 2) {
                    ListNode newNode = merge2Lists(lists[i], lists[i + 1]);
                    validLength--;
                    lists[j++] = newNode;
                }
                if (t % 2 == 1)
                    lists[j++] = lists[i];
            }
            return lists[0];
        }

        public ListNode merge2Lists(ListNode node1, ListNode node2) {
            ListNode head = new ListNode();
            ListNode p1 = node1, p2 = node2, curr = head;
            while (p1 != null && p2 != null) {
                if (p1.val < p2.val)
                    p1 = appendNode(p1, curr);
                else
                    p2 = appendNode(p2, curr);
                curr = curr.next;
            }

            while (p1 != null) {
                p1 = appendNode(p1, curr);
                curr = curr.next;
            }

            while (p2 != null) {
                p2 = appendNode(p2, curr);
                curr = curr.next;
            }

            return head.next;
        }

        public ListNode appendNode(ListNode node, ListNode curr) {
            ListNode ret = node.next;
            node.next = null;
            curr.next = node;
            return ret;
        }

    }
    ```

设有 $k$ 组链表，最长一个为 $n$，则第一轮复杂度为 $O(\frac{k}{2} \cdot 2n)$，第二轮 $O(\frac{k}{4} \cdot 4n)$，故总复杂度 $O(kn\log k)$
