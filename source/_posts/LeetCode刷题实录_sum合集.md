---
title: 【LeetCode】求和问题合集(2sum,3sum,3sum closest,4sum)
date: 2017-11-31 19:21:48
tags:
- BST
categories:
- 算法
---

经典求和问题~

<!-- more -->
刷题语言:Python

2sum 

难度属于Easy 其实可能考虑出现的问题已经被题目取消了，没什么难度 略

3sum 

如果没什么思路可以先考虑暴力解法，先把问题解决再慢慢优化
。。。

很显然，暴力解法的效率太低了，用例跑了3/4就超时了。

那么就想办法降低时间复杂度将O（n3）降低为O（n2）

把3sum的问题分解为2sum的问题再解决，用双指针的思路从两侧一起去找到符合要求的列表,因为已经排序那么去重可以通过简单的判断解决。

```Python
	class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        KG = []
        nums.sort()
        for i in range(len(nums) - 2):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            left, right = i + 1, len(nums) - 1
            target =nums[i]
            while left < right:
                if nums[left] + nums[right] + target == 0:
                    QT = [nums[i], nums[left], nums[right]]
                    KG.append(QT)
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1
                    left += 1
                    right -= 1
                elif nums[left] + nums[right] + target < 0:
                    left += 1
                else:
                    right -= 1
        return KG
```

完美！

这个题难度为Medium，其实大多数就卡在算法不知道如何优化，新手刷题（比如我）就卡在这里半天，难度实际来看不是很大，算是教训，在此记录

3sum closest 

类似3sum的解法，另外考虑一下第一次进入判断时没有最小差值的情况


```Python

    class Solution(object):
    def threeSumClosest(self, nums, target):
    	"""
    	:type nums: List[int]
    	:type target: int
    	:rtype: int
    	"""
    	nums.sort()
    	mindiff = -1
    	result = 0
    	for i in range(len(nums)-2):
            left = i + 1
            right = len(nums) - 1
            while(left < right):
                sum = nums[left] + nums[i] + nums[right]
                diff = abs(sum - target)
                
                if diff == 0:
                    return sum
                if mindiff == -1:
                    mindiff = diff
                    result = sum
                if diff < mindiff:
                    mindiff = diff
                    result = sum

                if sum > target:
                    right -= 1
                else:
                    left += 1
    return result
	    
```

        
4 sum

1 先对列表排序

2 先确定数组的前两个(a,b), 再利用二分查找确定剩下的(c, d),思路近似于3sum
	
3 去重可以通过结构控制解决


```Python
	class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        nums.sort()
        L = list()
        for i in range(len(nums)-3):
            if i > 0 and nums[i] == nums[i-1]: continue
            for j in range(i+1, len(nums)-2):
                if j > i + 1 and nums[j] == nums[j-1]:continue
                left = j + 1
                right = len(nums) - 1
                sum = target - nums[i] - nums[j]
                while left < right:
                    tmp = nums[left] + nums[right]
                    if sum == tmp:
                        QT = [nums[i], nums[j], nums[left], nums[right]]
                        L.append(QT)
                        left += 1
                        right -= 1
                        while left < right and nums[left] == nums[left - 1]:
                            left += 1
                        while left < right and nums[right] == nums[right + 1]:
                            right -= 1
                    elif sum > tmp:
                        left += 1
                        while left < right and nums[left] == nums[left - 1]:
                            left += 1
                    else:
                        right -= 1
                        while left < right and nums[right] == nums[right + 1]:
                            right -= 1
        return L
```
