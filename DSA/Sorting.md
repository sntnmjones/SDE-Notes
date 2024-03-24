## merge sort
In place

```python
class Solution:
    def sortArray(self, nums: List[int]) -> List[int]:
        def merge(leftNums, rightNums, result):
            l_p, r_p, k = 0, 0, 0

            while l_p < len(leftNums) and r_p < len(rightNums):
                if leftNums[l_p] < rightNums[r_p]:
                    result[k] =  leftNums[l_p]
                    l_p += 1
                else:
                    result[k] = rightNums[r_p]
                    r_p += 1
                k += 1

			# Add remainder to end of result
            result[k:] = leftNums[l_p:] if l_p < len(leftNums) else rightNums[r_p:]

        def mergesort(nums):
            if len(nums) == 1: return

            mid = len(nums) // 2
            L = nums[:mid]
            R = nums[mid:]

            mergesort(L)
            mergesort(R)

            merge(L, R, nums)
        
        mergesort(nums)
        return nums
```

new list
