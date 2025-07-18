# Leetcode_Study_Queue
# Leetcode 933: Number of Recent Calls

## Problem Summary

You are implementing a class `RecentCounter` to count the number of recent requests within a 3000ms window.

Implement a method:

```python
ping(t: int) -> int
```

Each call to `ping(t)` records a new ping at time `t` (in milliseconds). Return the number of pings that have occurred in the inclusive time range `[t - 3000, t]`.

- Each `t` is strictly increasing.
- Each `t` is guaranteed to be in the range `[1, 10^9]`.

---

## Intuition

This is a classic **sliding window** problem over a **time range**.

We can use a **queue** to store timestamps of past requests. For each new `ping(t)`:

- Add `t` to the queue.
- Remove all timestamps from the front of the queue that are older than `t - 3000`.
- Return the length of the queue.

The queue always contains only the timestamps that fall within the 3000ms window.

---

## Why a Queue?

- Because we want **first-in-first-out (FIFO)** behavior.
- Old pings are removed from the front (`popleft()`), and new ones are added to the back (`append()`).
- We only need to scan in one direction and maintain the order.

Use Python’s `collections.deque` for efficient O(1) `append` and `popleft`.

---
## Maintain window invariant
while window condition is violated:
    shrink window from the left

## Implementation

```python
from collections import deque

class RecentCounter:
    def __init__(self):
        self.q = deque()

    def ping(self, t: int) -> int:
        self.q.append(t)
        while self.q[0] < t - 3000:
            self.q.popleft()
        return len(self.q)
```

---

## Example

```python
rc = RecentCounter()
rc.ping(1)     # [1] -> returns 1
rc.ping(100)   # [1, 100] -> returns 2
rc.ping(3001)  # [1, 100, 3001] -> returns 3
rc.ping(3002)  # [100, 3001, 3002] -> returns 3
```

---

## Time Complexity

- `ping(t)`:
  - `append()` → O(1)
  - Each element is removed at most once via `popleft()` → **Amortized O(1)**
- Overall: **O(1) amortized per ping**

## Space Complexity

- The queue stores only recent pings within a 3000ms window
- In the worst case (1 ping per ms): **O(3000)** space

---

## Key Takeaways

- This is a **time-based sliding window** problem
- Use a **queue (deque)** to maintain the window
- Maintain only relevant pings (timestamps in `[t - 3000, t]`)
- Understand the difference between `list.pop(0)` (O(n)) and `deque.popleft()` (O(1))
- Analyze the amortized complexity, not worst-case per operation

---

## Follow-up Ideas

- Generalize this to a window of arbitrary size
- Apply this pattern to sliding window max/min/avg/median (e.g. Leetcode 239, 480)
