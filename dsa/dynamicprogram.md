# Dynamic Programming

## 312. Burst Balloons

```python
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        nums = [1] + nums + [1]
        n = len(nums)
        lookup = {}

        # maximum profit in the partition (i,j)
        def f(i, j):
            if i > j:
                # maximum profit in invalid partition = 0
                return 0
            if (i, j) in lookup:
                return lookup[(i,j)]
            best = -10**9
            for k in range(i, j+1):
                # burst out of partition; Xi--k---jX
                current = nums[i-1] * nums[k] * nums[j+1]
                left = f(i, k-1)
                right = f(k+1, j)
                best =max(best, current+left+right)
            lookup[(i,j)] = best
            return best

        return f(1, n-2) #burst all valid ballons
```

## Reverse Linked List

```python

```

## Reverse Linked List

```python

```

## Reverse Linked List

```python

```

## Reverse Linked List

```python

```
