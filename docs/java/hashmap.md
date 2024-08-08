# HashMap

---

1. Java HashMap allows null key, which always goes to index 0 as hash of null is 0.
2. Default size of HashMap is 16.
3. In Java 8, when we have too many unequals keys gives the same hashCode(index) value i.e, when the number of entries in the same hash bucket exceeds a threshold value ( `TREEIFY_THRESHOLD=8` ), contents of that bucket switches from linked list of entry objects to a balanced binary tree. This theoretically improves the performance from `O(n)` to `O(logn)` .