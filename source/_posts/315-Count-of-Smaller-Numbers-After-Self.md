---
title: 【LeetCode】315. Count of Smaller Numbers After Self
date: 2019-01-31 21:21:48
tags:
- DP
- BST
categories:
- 算法
---


吐槽:

LeetCode刷题第N天，说起来都工作一年了，来自工作的收益感觉都没刷题多。事实证明，选好职业方向是多么重要的事情。另外“任何语言都会过时，只有数据结构和算法才能永恒”，还有写题解真的要花好多时间，尽量写一点有代表性的题解吧。

<!-- more -->

题目链接: https://leetcode.com/problems/count-of-smaller-numbers-after-self/

关于这道题首先是理解题目要求。

```
数组 nums = [5, 2, 6, 1]

5 右边有 2 个元素比 5 小（2 和 1）
2 右边有 1 个元素比 2 小 （1）
6 右边有 1 个元素比 6 小（1）
1 右边有 0 个元素比 1 小
```

返回的数组为 [2, 1, 1, 0]

如果直接写双重循环O(N^2)的代码，直接超时，因为部分case数组长度 20000+，O(N^2)一算，基本就GG了。

仔细思考一下，其实可以维护一个BST(二叉搜索树)，从右向左进行遍历，同时维护每个树结点中的统计值属性，每次有符合的条件的值进入，更新统计值并且更新结点，将对应的结点生成插入到对应的位置。

代码如下:

```python
class TreeNode:
    def __init__(self,val):
        self.left = None
        self.right = None
        self.val = val
        self.count = 1

class Solution:
    def insertTree(self, root, val):
        if not root:
            return 0
        tempCount = 0
        while True:
            if val <= root.val:
                root.count += 1
                if root.left is None:
                    root.left = TreeNode(val)
                    break
                else:
                    root = root.left
            else:
                tempCount += root.count
                if root.right is None:
                    root.right = TreeNode(val)
                    break
                else:
                    root = root.right
        return tempCount

    def countSmaller(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        # Solution 1: 时间复杂度O(N!),每pop出左侧的一个数字必须遍历剩余的数字获取小于该数字的数字个数, TLE预定。
        # Solution 2: BST
        # Solution 3: 分段树求解

        n = len(nums)
        if n == 0:
            return []
        if n == 1:
            return [0]

        ans = []
        root = TreeNode(nums[-1])
        ans.append(0)

        for i in range(n-2, -1, -1):
            count = self.insertTree(root, nums[i])
            ans.append(count)
        return ans[::-1]
```



仔细看了一下对应的大神的思路:

1 线段树

```Java
public class Solution {
    private void sort(int[] nums, int[] pos, int from, int to) {
        if (from>=to) return;
        int m = (from+to)/2;
        sort(nums, pos, from, m);
        sort(nums, pos, m+1, to);
        int[] merged = new int[to-from+1];
        int i=from, j=m+1, p=0;
        while (i<=m || j<=to) {
            if (i>m) {
                merged[p++] = pos[j++];
            } else if (j>to) {
                merged[p++] = pos[i++];
            } else if (nums[pos[i]] <= nums[pos[j]]) {
                merged[p++] = pos[i++];
            } else {
                merged[p++] = pos[j++];
            }
        }
        for(int k=0; k<merged.length; k++) pos[from+k] = merged[k];
    }
    private int count(int[] sum, int s) {
        int count = 0;
        while (s>0) {
            count += sum[s];
            s -= (s & -s);
        }
        return count;
    }
    private void update(int[] sum, int s) {
        while (s<sum.length) {
            sum[s] ++;
            s += (s & -s);
        }
    }
    public List<Integer> countSmaller(int[] nums) {
        int[] pos = new int[nums.length];
        for(int i=0; i<nums.length; i++) pos[i] = i;
        sort(nums, pos, 0, nums.length-1);
        int[] seq = new int[nums.length];
        for(int i=0, s=0; i<pos.length; i++) {
            if (i==0 || nums[pos[i]] != nums[pos[i-1]]) seq[pos[i]] = ++ s; else seq[pos[i]] = s;
        }
        int[] sum = new int[nums.length+1];
        int[] smaller = new int[nums.length];
        for(int i=nums.length-1; i>=0; i--) {
            smaller[i] = count(sum, seq[i]-1);
            update(sum, seq[i]);
        }
        List<Integer> result = new ArrayList<>(nums.length);
        for(int i=0; i<smaller.length; i++) result.add(smaller[i]);
        return result;
    }
}

```

感觉是没有O(N)的线性解法，如果有大佬有特别高效的解法，欢迎交流探讨。
