### 排列 DFS

根据给定条件，生成所有的序列排列:

-   构造原问题的 DFS 搜索树 (所有叶节点深度相同)，然后套用 DFS 模板即可
-   如果序列长度很小，可用状态压缩优化

例子:

<div class="outerlink">
<a href="../_leetcode/526/">526. 优美的排列</a> (典型的由 DFS 搜索到记忆化搜索再到 DP) <br>
<a href="../_leetcode/46/">46. 全排列</a> (回溯，变式 DFS) <br>
<a href="../_leetcode/47/">47. 全排列 2</a> (全排列基础上做个小修改去重) <br>
<a href="../_leetcode/996/">996. 正方形数组的数目</a> (全排列 2 的变形) <br>
<a href="../_leetcode/22/">22. 括号生成</a> <br>
<a href="../_leetcode/72/">72. 单词搜索</a> <br>
<a href="../_leetcode/212/">212. 单词搜索 II</a> (单词搜索 + trie) <br>
</div
