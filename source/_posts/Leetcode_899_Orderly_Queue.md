---
title: 【LeetCode】899. Orderly Queue
date: 2019-02-15 11:47:00
tag: 
- LeetCode
categories:
- 算法
---

Orderly Queue 题解

<!-- more -->

A string `S` of lowercase letters is given.  Then, we may make any number of *moves*.

In each move, we choose one of the first `K` letters (starting from the left), remove it, and place it at the end of the string.

Return the lexicographically smallest string we could have after any number of moves.

 

**Example 1:**

```
Input: S = "cba", K = 1
Output: "acb"
Explanation: 
In the first move, we move the 1st character ("c") to the end, obtaining the string "bac".
In the second move, we move the 1st character ("b") to the end, obtaining the final result "acb".
```

**Example 2:**

```
Input: S = "baaca", K = 3
Output: "aaabc"
Explanation: 
In the first move, we move the 1st character ("b") to the end, obtaining the string "aacab".
In the second move, we move the 3rd character ("c") to the end, obtaining the final result "aaabc".
```



解题思路:


一开始没思路，仔细观察可以发现，这里是分类讨论:

当K == 1时， 结果是字符串的最小表示法：

​&#8195;&#8195;从i=0,j=1位置开始比较，当某个位置s[i+k]<s[j+k]时， j=max(i+1,j+k+1)，因为[j+1,j+k]位置的字符都有对应的[i+1,i+k]字符与其相等或者更小，所以每次j更新为（i+1,j+k+1）中较大的值；同理，s[i+k]>s[j+k]时，类似。

当 K >= 2时，每次从左至右至少可以调整前两位的数字的位置：

​&#8195;&#8195;思考下和冒泡排序的思路相似，每次允许交换相邻两位置的数字。这里是允许前两位数字移动位置，对应结果也就是字符串有序结果。



以上，可以得到对应的解法如下:

Solution:

```Python
class Solution:
    def orderlyQueue(self, S:'str', K:'int') -> 'str': 
        if K == 1:
            return min(S[i:] + S[:i] for i in range(len(S)))
        return ''.join(sorted(S)
```



Ps: LeetCode-Python3终于加上支持参合法性校验，这么看起来总算有"pythonic"的感觉了s