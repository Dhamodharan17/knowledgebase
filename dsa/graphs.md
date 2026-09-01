# Graphs

## Number of Islands

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        m, n = len(grid), len(grid[0])

        def f(r, c):

            grid[r][c] = '0' # mark visited
            for ri, ci in dirs:
                nri, nci = ri+r, ci+c
                if 0 <= nri < m and 0 <= nci < n and grid[nri][nci] == '1':
                    f(nri, nci)

        count = 0
        for r in range(m):
            for c in range(n):
                if grid[r][c] == '1':
                    count += 1
                    f(r, c)
        return count
```

## Max Area of Island

```python
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        dirs = [(1,0), (-1,0), (0,1), (0,-1)]


        def f(r, c):
            count = 1 # for each non zero
            grid[r][c] = 0 # mark visited
            for ri, ci in dirs:
                nri, nci = ri+r, ci+c
                if 0 <= nri < m and 0 <= nci < n and grid[nri][nci] == 1:
                    count += f(nri, nci) # get 1 for each non zero cell
            return count

        maxi = 0
        for r in range(m):
            for c in range(n):
                if grid[r][c] == 1:
                    count = f(r, c)
                    maxi = max(maxi, count)
        return maxi

```

## Clone Graph

```python
class Solution:
    def cloneGraph(self, node: Optional['Node']) -> Optional['Node']:

        lookup = {}
        def f(u):
            if not u:
                return None
            if u in lookup:
                return lookup[u]
            root = Node(u.val)
            lookup[u] = root #store so down the lane can use
            for v in u.neighbors:
                root.neighbors.append(f(v))
            return root
        return f(node)
```

## Walls and Gates

```python
class Solution:
    def wallsAndGates(self, rooms: List[List[int]]) -> None:
        # task - each room with nearest gate

        m, n = len(rooms), len(rooms[0])
        INF = 2147483647
        dirs = [(1,0), (-1,0), (0,1), (0,-1)]

        q = deque()
        for r in range(m):
            for c in range(n):
                if rooms[r][c] == 0:
                    q.append((r, c))

        level = 1
        while q:
            size = len(q)
            for _ in range(size):
                r, c = q.popleft()
                for ri, ci in dirs:
                    nri, nci = ri+r, ci+c
                    if 0 <= nri < m and 0 <= nci < n and rooms[nri][nci] == INF:
                        rooms[nri][nci] =  level #update gates when encountered
                        q.append((nri, nci))
            level += 1
```
