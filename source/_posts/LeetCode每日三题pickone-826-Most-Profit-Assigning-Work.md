---
title: 【LeetCode】826. Most Profit Assigning Work
date: 2018-08-06 00:03:42
tags: 
- Greedy algorithm
categories:
- 算法
---
### Problems:
We have jobs: difficulty[i] is the difficulty of the ith job, and profit[i] is the profit of the ith job. 

Now we have some workers. worker[i] is the ability of the ith worker, which means that this worker can only complete a job with difficulty at most worker[i]. 

Every worker can be assigned at most one job, but one job can be completed multiple times.

For example, if 3 people attempt the same job that pays $1, then the total profit will be $3.  If a worker cannot complete any job, his profit is $0.

What is the most profit we can make?

<!-- more -->


#### Example 1:


```
Input: difficulty = [2,4,6,8,10], profit = [10,20,30,40,50], worker = [4,5,6,7]
Output: 100 
Explanation: Workers are assigned jobs of difficulty [4,4,6,6] and they get profit of [20,20,30,30] seperately.
```

#### Notes:

- 1 <= difficulty.length = profit.length <= 10000
- 1 <= worker.length <= 10000
- difficulty[i], profit[i], worker[i]  are in range [1, 10^5]



---

方案1：贪心算法 - 普通的实现思路: 遍历这个员工可用的最大的价值，累加得到最终结果。 时间复杂度O(N^2)，通常第一时间想到的就是时间最不可能的orz。 很显然，太暴力了，超时= = 

```Python
# Solution1: Runtime Time out
# Fail Function
class Solution:
    def maxProfitAssignment(self, difficulty, profit, worker):
        """
        :type difficulty: List[int]
        :type profit: List[int]
        :type worker: List[int]
        :rtype: int
        """
        profit_dict = dict()
        for z, profit_one in enumerate(profit):
        profit_dict[z] = profit_one
        max_salary = 0
        for i, workone in enumerate(worker):
        max_count = 0 
        for j, difficulty_one in enumerate(difficulty):
            if workone >= difficulty_one:
                max_count = max(max_count, profit_dict[j])
        max_salary += max_count
        return max_salary
```

---

方案2：那么另一个方法就是对于<difficulty, profit>这个pair进行排序，按照difficulty为index升序排列。同时对于worker也升序排序。这样两个都排序完后，就可以对于每个worker，找到所有difficulty < worker的job，看看哪个job能获得最大的利润就把这个job分配给当前的worker。这样下来的时间复杂度是O(NlogN+QlogQ), 其中N是job的数量，Q是worker的数量，另外对于已经排序过的列表，可以使用利用二分、two-pointer这种优化更快的得到结果,将时间复杂度降到O(N+Q)。结果==AC - 99.09%==

```Python
# Solution2: 99.09% AC 
class Solution:
    def maxProfitAssignment(self, difficulty, profit, worker):
        """
        :type difficulty: List[int]
        :type profit: List[int]
        :type worker: List[int]
        :rtype: int
        """
        profit_zip = zip(difficulty, profit)
        pro_list = [x for x in profit_zip]
        pro_list.sort()
        worker.sort()
        
        i = max_p = 0
        result = 0
        for w in worker:
            while i < len(worker) and pro_list[i][0] <= w:
                max_p = max(max_p, pro_list[i][1])
                i += 1
            result += max_p
        return result
```

---


方案3: 看到一个大神的思路，记录下。借鉴桶排序的的思想,首先分配一个数组d[100001]代表每个difficulty的最大profit，先初始化值，然后从前到后扫一遍，确保每个d[i]放的的确是difficulty为i时的最大利润。 ——@WhzisZihan 结果待验证


```C++
//C++版
class Solution {
    public:
        int maxProfitAssignment(vector<int>& difficulty, vector<int>& profit, vector<int>& worker) {
            memset(cnt, 0, sizeof(cnt));
            int dsize = difficulty.size(), wsize = worker.size(), res = 0;
            for (int i = 0; i < dsize; ++i) {
                cnt[difficulty[i]] = (profit[i] > cnt[difficulty[i]] ? profit[i] : cnt[difficulty[i]]);
            }
            for (int i = 1; i <= 100000; ++i) {
                cnt[i] = (cnt[i] < cnt[i - 1] ? cnt[i - 1] : cnt[i]);
            }
            // then just sum all the expected profit of each worker in corresponding diffculty
            for (int i = 0; i < wsize; ++i) {
                res += cnt[worker[i]];
                }
            return res;
            }
    private:
        int cnt[100001]; // the range is from [1, 100000]
    };
```
##### python版本随后补充  >未完待续 2018/08/05
