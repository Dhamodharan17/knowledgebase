# Backtracking

## Subsets

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        N = len(nums)

        def f(i, temp):
            res.append(list(temp))
            for j in range(i, N):
                temp.append(nums[j])
                f(j+1, temp)
                temp.pop()

        res = []
        f(0, [])
        return res
```

## Combination Sum

```python
class Solution:
    def combinationSum(self, c: List[int], target: int) -> List[List[int]]:

        N = len(c)
        def f(i, t, temp):

            if t == 0:
                res.append(list(temp))
                return
            if i == N:
                return
            for j in range(i, N):
                if c[j] <= t:
                    temp.append(c[j])
                    f(j, t-c[j], temp)
                    temp.pop()

        res = []
        f(0, target, [])
        return res
```

## Combination Sum II

```python
class Solution:
    def combinationSum2(self, c: List[int], target: int) -> List[List[int]]:
        N = len(c)
        c.sort()

        def f(i, t, temp):
            if t == 0:
                res.append(list(temp))
                return
            if i == N:
                return
            for j in range(i, N):
                # skip duplicates in the current window
                if j > i and c[j] == c[j-1]:
                    continue
                if c[j] <= t:
                    temp.append(c[j])
                    f(j+1, t-c[j], temp)
                    temp.pop()

        res = []
        f(0, target, [])
        return res
```

## Permutations

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        N = len(nums)
        seen = set()

        def f(i, temp):

            if len(temp) == N:
                res.append(list(temp))
                return
            # seen.add(i) - marking parent without selecting
            for j in range(N):
                if j not in seen:

                    temp.append(nums[j])
                    seen.add(j) #pick element mark as seen
                    f(j+1, temp)

                    temp.pop()
                    seen.remove(j)


        res = []
        f(0, [])
        return res
```

## Subsets II

```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        N = len(nums)
        res = []

        def f(i, temp):
            res.append(list(temp))
            for j in range(i, N):
                #skip duplicates in current i to j
                if j > i and nums[j] == nums[j-1]:
                    continue
                temp.append(nums[j])
                f(j+1, temp)
                temp.pop()
        f(0, [])
        return res
```

## Generate Parentheses

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:

        def f(ocnt, ccnt, cstr):
            if ocnt + ccnt == 2*n:
                res.append(str(cstr))
                return
            if ccnt > ocnt:
                return

            # allow only n brackets and let other side grow
            if ocnt < n:
                f(ocnt+1, ccnt, cstr+'(')
            if ccnt < n:
                f(ocnt, ccnt+1, cstr+')')
        res = []
        f(0, 0, '')
        return res
```

## Word Search

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        dirs = [(1,0), (-1, 0), (0, 1), (0, -1)]
        m, n = len(board), len(board[0])
        N = len(word)

        def f(r, c, sidx):

            if sidx == N:
                return True
            if not 0 <= r < m or not 0 <= c < n:
                return False
            if board[r][c] != word[sidx] or board[r][c] == '#':
                return False
            temp  = board[r][c]
            board[r][c] = '#'

            for ri, ci in dirs:
                nri, nci = r+ri, c+ci
                if f(nri, nci, sidx+1):
                    return True
            board[r][c] = temp
            return False

        for r in range(m):
            for c in range(n):
                if f(r, c, 0):
                    return True
        return False
```

## Palindrome Partitioning

```python
class Solution:
    def partition(self, s: str) -> List[List[str]]:
        N = len(s)
        def f(start, temp):
            if start == N:
                res.append(list(temp))
                return
            for end in range(start, N):
                curs = s[start:end+1]
                if curs != curs[::-1]:
                    continue
                temp.append(curs)
                f(end+1, temp)
                temp.pop()
        res = []
        f(0, [])
        return res
```

## Letter Combinations of a Phone Number

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        phmap = {2:'abc', 3:'def', 4:'ghi',
        5:'jkl', 6:'mno', 7:'pqrs',
        8:'tuv', 9:'wxyz'}
        N = len(digits)

        def f(i, temp):

            if i == N:
                res.append(''.join(temp))
                return
            for c in phmap[int(digits[i])]:
                # move to next list(key)
                f(i+1, temp+c)

        res = []
        f(0, '')
        return res
```

## N-Queens

<details>
<summary>Click to view image</summary>

![Alt Text](./dsa/assets/nqueens.png)

</details>

```python
class Solution:

    def solveNQueens(self, n: int) -> List[List[str]]:

        board = [['.'] * n for _ in range(n)]
        res = []

        def f(current_row):
            if current_row == n:
                res.append(["".join(row) for row in board])
                return

            # try all columns
            for current_col in range(n):
                if self.valid(n, current_row, current_col, board):
                    board[current_row][current_col] = 'Q'

                    # move next row
                    f(current_row + 1)

                    board[current_row][current_col] = '.'

        f(0)
        return res
    def valid(self, n, r, c, board):
        # 1. Check upper column
        for i in range(r):
            if board[i][c] == 'Q':
                return False

        # 2. Check upper-left diagonal
        ri, ci = r - 1, c - 1
        while ri >= 0 and ci >= 0:
            if board[ri][ci] == 'Q':
                return False
            ri -= 1
            ci -= 1

        # 3. Check upper-right diagonal
        ri, ci = r - 1, c + 1
        while ri >= 0 and ci < n:
            if board[ri][ci] == 'Q':
                return False
            ri -= 1
            ci += 1

        return True

```
