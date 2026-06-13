# se-intern-assignment
Solutions for LRU Cache and Event Scheduler assessment with complexity analysis and system design discussion.
**Problem 1: LRU Cache Architecture and Mechanics**

An LRU Cache is a memory buffer that stores a fixed amount of frequently used data. When the cache is full it gets rid of the items that have not been used recently. This helps make the cache work faster when it needs to read or write data.

System Execution Trace (Capacity = 3)

Lets look at how the cache works when it can hold 3 items:

* put(A, 1). There is space so the cache adds element A.
State: [A] (A is the recently used item)

* put(B, 2). There is space so element B is added to the front.
State: [B, A]

* put(C 3). There is space so element C is added to the front.
State: [C B A] (A is now the recently used item)

* get(A). The cache finds A. It moves A to the front.
State: [A C B] (B is now the recently used item)

* put(D, 4). The cache is full so it gets rid of the recently used item, which is B and adds D to the front.
State: [D, A, C]

Engineering Design: Making it Fast

In systems the cache needs to work fast no matter how much data it has. This means it needs to look up and change data in time. If we use an array or linked list it is easy to add items one after the other but it takes a long time to find an item or move it around.

On the hand a hash map can find items quickly but it does not keep track of the order of the items.

To make it fast we use a combination of a hash map and a linked list.

1. The Hash Map Layer (self.cache)
The hash map helps us find items quickly by mapping keys to the location of the item in the linked list. This way we can find items in time.

2. The Linked List Layer (Node, head, tail)
The linked list helps us move items around quickly. Each item in the list has pointers to the items before and after it.

When we find an item that's not at the front of the list we can move it to the front quickly by changing a few pointers:

Python

node.prev.next = node.next
node.next.prev = node.prev

Then we can add the item to the front of the list.

The hash. Linked list work well together. The hash map helps us find items quickly. The linked list helps us move items around quickly. This way the cache can look up. Change data, in constant time, which is what we need for real systems.



**Problem 2: Event Scheduler Mechanics & Algorithmic Logic**

The main task of the Event Scheduler is to allocate resources. This means checking if one person can attend meetings without time conflicts. It also means finding the number of meeting rooms needed for all events.
This problem is like an Interval Scheduling Optimization Problem. I solved it by sorting events in order and using an allocation system.

System Execution Trace:

Lets look at 4 scheduled meeting intervals: [(9 10) (11, 12) (9, 11) (10 11)]

# Step 1: Timeline Normalization (Sorting)

The system needs to create a timeline. It sorts events by start time.
Sorted Input: [(9 10) (9 11) (10 11) (11, 12)]

# Step 2: Dynamic Room Allocation

The system uses a **Min-Heap** to track the end times of meetings. The size of the heap shows the number of rooms required.

* Process Event 1 (9, 10): No rooms are active. A new room is needed. The end time (10) is added to the heap.
Heap State: [10] (1 room active)

* Process Event 2 (9, 11): The start time is 9. The earliest room to become free is at 10. Since 9 < 10 no room is free. A new room is allocated. The end time (11) is added.
Heap State: [10 11] (2 rooms active)

* Process Event 3 (10, 11): The start time is 10. The earliest room to become free is at 10. Since 10 >= 10 the first room is free. The finished meeting is. The new end time (11) is added.
Heap State: [11 11] (2 rooms active)

* Process Event 4 (11, 12): The start time is 11. The earliest room to become free is at 11. Since 11 >= 11 a room is free. It is reused by updating the end time (12).
Heap State: [11 12] (2 rooms active)

Final Result

The algorithm finishes with a maximum pool size of 2. This means 2 meeting rooms are required to avoid scheduling conflicts.

Engineering Design: Algorithmic Efficiencies

The system has two stages.

1. Structural Sorting for can_attend_all

To check for scheduling conflicts for one person events are sorted by start time in a $O(N \log N)$ time. Then a single pass ($O(N)$) compares the meetings start time with the previous meetings end time.
This approach avoids a $O(N^2)$ pairwise comparison matrix. The total time complexity is $O(N \log N)$.

2.  Min-Heap Priority Queue for

A simple approach would scan every active room leading to $O(N^2)$ runtime.
Using a Min-Heap (Priority Queue) guarantees that the top element is the available room.

* Checking the element takes constant time ($O(1)$).
* If a room is free updating the heap takes time ($O(\log N)$).
For $N$ events the allocation engine runs in optimal $O(N \log N)$ time. This makes it scalable, for scheduling systems.
