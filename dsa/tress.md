# Trees

### Vertical Order Traversal of a Binary Tree

```python
from collections import defaultdict
from typing import List, Optional

class Solution:
    def verticalTraversal(self, root: Optional[TreeNode]) -> List[List[int]]:
        # Map structure: { col: { row: [val1, val2, ...] } }
        colrowmap = defaultdict(lambda: defaultdict(list))

        # Helper DFS traversal to collect nodes into coordinates (c, r)
        def dfs(node, r, c):
            if not node:
                return

            colrowmap[c][r].append(node.val)
            dfs(node.left, r + 1, c - 1)
            dfs(node.right, r + 1, c + 1)

        dfs(root, 0, 0)

        result = []

        # Step 1: Process columns from left to right
        for c in sorted(colrowmap.keys()):
            col_list = []

            # Step 2: Process rows from top to bottom
            for r in sorted(colrowmap[c].keys()):
                # Step 3: Sort values at the exact same position (row, col)
                nodes_at_pos = sorted(colrowmap[c][r])
                col_list.extend(nodes_at_pos)

            result.append(col_list)

        return result
```

<details>
<summary> Why nodes_at_pos = sorted(colrowmap[c][r]) ?
</summary>
Sorting at that specific step is required because of **LeetCode Problem 987 ("Vertical Order Traversal of a Binary Tree")'s strict tie-breaker rule**.

When two or more nodes share the **exact same row and column coordinates**, the problem statement explicitly requires them to be reported in **ascending order of their values**.

---

### 1. The Problem Rule

If node $A$ and node $B$ have:

- The same column index $c$
- The same row index $r$

Then node values must be listed as $\min(A, B)$ followed by $\max(A, B)$.

---

### 2. Concrete Example

Consider this binary tree:

```text
       1
     /   \
    2     3
   / \   / \
  4   5 6   7

```

Looking at node `5` (left child of `3` or right child of `2` in overlapping structures) and node `6`:

- Node `5`: reached via `Root -> Left (r=1, c=-1) -> Right (r=2, c=0)`
- Node `6`: reached via `Root -> Right (r=1, c=1) -> Left (r=2, c=0)`

Both nodes `5` and `6` end up at coordinate **`(row=2, col=0)`**.

Depending on how DFS traverses the tree (e.g., visiting left subtree before right subtree), `colrowmap[0][2]` will store `[5, 6]`. However, if the tree structure or traversal order inserts `6` before `5` (like in BFS or alternate traversals), `colrowmap[0][2]` might hold `[6, 5]`.

By running `sorted(colrowmap[c][r])`, you guarantee that `[6, 5]` becomes `[5, 6]`, strictly satisfying the problem's sorting constraint regardless of how DFS visited them.

</details>

---
