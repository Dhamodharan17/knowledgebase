# Linked List

### Reverse Linked List

```python
class Solution:
    def reverseList(self, head):
        prev = None
        cur = head
        while cur:
            next = cur.next

            # core logic - point next to prev
            cur.next = prev
            prev = cur

            cur = next
        return prev
```

### Merge Two Sorted Lists

```python
class Solution:
    def mergeTwoLists(self, list1, list2):

        #dummy node to avoid edge cases
        head = ListNode()
        cur = head

        # merge sort 2 pointer algorthim
        while list1 and list2:
            if list1.val <= list2.val:
                cur.next = list1
                list1 = list1.next
            else:
                cur.next = list2
                list2 = list2.next
            cur = cur.next

        # add short given circuited sorted linked if any
        cur.next = list1 if list1 else list2

        return head.next #return after dummy
```

### Linked List Cycle

```python
class Solution:
    def hasCycle(self, head):

        s = f = head
        # if there is cycle (ground) s (30kmph) and f(60kmph) will meet
        while f and f.next:
            s = s.next
            f = f.next.next #handled f.next in while to avoid Null pointer
            if s == f:
                return True
        return False
```

### Reorder List

```python
class Solution:
    def reorderList(self, head: Optional[ListNode]) -> None:

        def mid(head):
            s = head
            f = head.next #1st mid
            while f and f.next:
                s = s.next
                f = f.next.next
            return s

        mid1 = mid(head) #get 1st mid
        head2 = mid1.next # create 2nd head
        mid1.next = None #break
        head2 = rev(head2) #reverse second list

        cur = head

        # since broke head2 by 1st mid, both ll will be equal
        while head and head2:

            nxt1 = head.next
            nxt2 = head2.next

            # core logic
            head.next = head2 #h1->h2
            head2.next = nxt1 #h2->h2.next


            #move both list
            head = nxt1
            head2 = nxt2

        return head
```

### Remove Nth Node From End of List

```python
class Solution:
    def removeNthFromEnd(self, head, k):

        # unknown scale -> draw k the draw full
        s = f = head

        #draw k
        for i in range(k):
            f = f.next
        if not f:
            return head.next

        # draw full
        while f and f.next:
            f = f.next
            s = s.next

        # s at k-1; 5-3 = 2 (at second so remove 3)
        s.next = s.next.next if s.next else None
        return head
```

### LRU

```python
class Node:
    def __init__(self, k, v):
        self.k = k
        self.v = v
        self.next = None
        self.prev = None


class LRUCache:

    def delete_node(self, node):

        nextnode = node.next
        prevnode = node.prev

        #ignore current node; P Current N
        prevnode.next = nextnode
        nextnode.prev = prevnode

    def add_to_head(self, node):

        head = self.head

        #always make change to new node 1st
        node.next = head.next
        node.prev = head

        #affected node from above
        head.next.prev = node
        head.next = node

    def __init__(self, capacity: int):
        self.head = Node(0,0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = capacity
        self.lookup = {}


    def get(self, key: int) -> int:

        if key not in self.lookup:
            return -1
        node = self.lookup[key]
        self.delete_node(node)
        self.add_to_head(node)

        return node.v


    def put(self, key: int, value: int) -> None:

        # update
        if key in self.lookup:
            node = self.lookup[key]
            node.v = value
            self.delete_node(node)
            self.add_to_head(node)
        else: # new node
            node = Node(key, value)
            self.lookup[key] = node
            self.add_to_head(node)
            # check after a new node
            if len(self.lookup) > self.size:
                lru = self.tail.prev
                self.delete_node(lru)
                del self.lookup[lru.k]
```

### Merge k Sorted Lists

```python
class Solution:

    # 2 pointer
    def merge(self, l1, l2):
        dummy = head = ListNode()
        while l1 and l2:
            if l1.val <= l2.val:
                dummy.next = l1
                l1 = l1.next
            else:
                dummy.next = l2
                l2 = l2.next
            dummy = dummy.next
        dummy.next = l1 if l1 else l2
        return head.next

    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        n = len(lists)
        if n == 0:
            return None
        head = lists[0]
        for i in range(1, n):
            head = self.merge(head, lists[i])
        return head
```

### Reverse

```python
class Solution:

    # 1. Find the k-th node from current (or return None if fewer than k nodes remain)
    def kthnode(self, head, k):
        curr = head
        # We need to advance k - 1 steps to reach the k-th node from head
        for _ in range(k - 1):
            if not curr:
                return None
            curr = curr.next
        return curr

    # 2. Standard linked list reversal
    def reverse(self, head):
        prev = None
        curr = head
        while curr:
            nxt = curr.next
            curr.next = prev
            prev = curr
            curr = nxt
        return prev

    def reverseKGroup(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if not head or k == 1:
            return head

        current = head
        prevGroupTail = None
        newHead = None

        while current:
            # Step 1: Find the k-th node
            kthNode = self.kthnode(current, k)

            # If fewer than k nodes remain, don't reverse—attach as-is and exit
            if not kthNode:
                if prevGroupTail:
                    prevGroupTail.next = current
                break

            # Step 2: Disconnect the group and track the next group's starting node
            nextLLStart = kthNode.next
            kthNode.next = None

            # Step 3: Reverse current group (returns the new head of this group)
            reversedHead = self.reverse(current)

            # Step 4: Stitch group into main list
            if not newHead:
                newHead = reversedHead  # Set global return head on first pass
            else:
                prevGroupTail.next = reversedHead # add new list existing groups

            # Step 5: Update pointers for next loop
            # 'current' is now the tail of the reversed group
            prevGroupTail = current
            current = nextLLStart

        return newHead if newHead else head
```

### LFU

```python
from collections import defaultdict, OrderedDict

class LFUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.min_freq = 0

        # Maps key -> (value, frequency)
        self.key_to_val_freq = {}

        # Maps frequency -> OrderedDict of {key: None}
        # OrderedDict maintains insertion order, so the first item is always the LRU item.
        self.freq_to_keys = defaultdict(OrderedDict)

    def _update_frequency(self, key: int) -> None:
        """Helper function to increment the frequency of a key."""
        val, freq = self.key_to_val_freq[key]

        # Remove key from current frequency group
        del self.freq_to_keys[freq][key]

        # If the current min_freq list becomes empty, increment min_freq
        if not self.freq_to_keys[self.min_freq]:
            self.min_freq += 1

        # Update key to its new frequency
        new_freq = freq + 1
        self.key_to_val_freq[key] = (val, new_freq)
        self.freq_to_keys[new_freq][key] = None

    def get(self, key: int) -> int:
        if key not in self.key_to_val_freq:
            return -1

        # Update frequency and return value
        self._update_frequency(key)
        return self.key_to_val_freq[key][0]

    def put(self, key: int, value: int) -> None:
        if self.capacity == 0:
            return

        # Scenario 1: Key already exists, update value and frequency
        if key in self.key_to_val_freq:
            _, freq = self.key_to_val_freq[key]
            self.key_to_val_freq[key] = (value, freq)
            self._update_frequency(key)
            return

        # Scenario 2: Cache is full, evict LFU (and LRU if tie) item
        if len(self.key_to_val_freq) >= self.capacity:
            # Pop the first item (LRU) from the minimum frequency list
            evict_key, _ = self.freq_to_keys[self.min_freq].popitem(last=False)
            del self.key_to_val_freq[evict_key]

        # Scenario 3: Insert new key
        self.key_to_val_freq[key] = (value, 1)
        self.freq_to_keys[1][key] = None
        self.min_freq = 1  # Reset min frequency to 1 since a new element is added
```
