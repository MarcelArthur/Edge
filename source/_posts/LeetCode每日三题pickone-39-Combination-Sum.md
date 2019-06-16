---
title: 【LeetCode】39. Combination Sum
date: 2018-08-11 11:42:24
tags:
- DFS
categories:
- 算法
---

### Problem:
Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

<!-- more -->

**Note**:

All numbers (including target) will be positive integers.
The solution set must not contain duplicate combinations.
**Example** 1:

```
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

**Example** 2:

```
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

来源:

[39-Combination Sum](https://leetcode.com/problems/combination-sum/description/)

---
Solution:

经典回溯法。排序后尝试加入每个candidates中的数添加到临时数组中，如果满足条件则添加到结果集，小于则继续添加，大于则返回移除后继续查找。
```Python
class Solution:
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """

        res = []
        templist = []
        candidates.sort()
        self.backtrace(res, templist, candidates, target, 0)
        return res

    def backtrace(self, res, templist, candidates, remain, start):
        if remain < 0:
            return
        elif remain == 0:
            res.append(templist[:])
        else:
            for i in range(start, len(candidates)):
                templist.append(candidates[i])
                self.backtrace(res, templist, candidates, remain - candidates[i], i)
                templist.remove(candidates[i])
```

Current optimal solution:

社区目前的最优解，高效简洁
```Python
class Solution:
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        result = []
        candidates.sort()
        def dfs(remain, stack):
            if remain == 0:
                result.append(stack[:])
                return

            for item in candidates:
                if item > remain:
                    break
                if stack and item < stack[-1]:
                    continue
                else:
                    dfs(remain - item, stack + [item])
            return

        dfs(target, [])
        return result
```
参考资料:

https://blog.csdn.net/u012033124/article/details/80641202

https://leetcode.com/problems/combination-sum/discuss/

类似题目:

[40-Combination Sum II](https://leetcode.com/problems/combination-sum-ii/description/)

[216-Combination Sum III](https://leetcode.com/problems/combination-sum-iii/description/)

[377-Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/description/)


