## Heap
### Min Heap
```python
import heapq

# Creating a list
data = [5, 3, 8, 1, 7]

# Creating a min-heap from the list using heapify
heapq.heapify(data)

print("Min-Heap:", data)  # Output: Min-Heap: [1, 3, 8, 5, 7]
```

### Max Heap
```python
import heapq

# Creating a list
data = [5, 3, 8, 1, 7]

# Simulating a max-heap by negating the values and using min-heap functions
data_negated = [-x for x in data]
heapq.heapify(data_negated)

# Insert negative numbers when inserting
heapq.heappush(max_heap, -4)

# To get the max value, negate it again
max_value = -heapq.heappop(data_negated)

print("Max-Heap:", max_value)  # Output: Max-Heap: 8
```

### Get n smallest/largest of heap
```python
import heapq

# Create a min-heap
min_heap = [5, 3, 8, 1, 7]

# Use heapq.nsmallest() to get the smallest elements without popping
smallest_elements = heapq.nsmallest(3, min_heap)

print("Smallest elements:", smallest_elements)  # Output: Smallest elements: [1, 3, 5]

# Create a max-heap (simulated using negated values)
max_heap = [-5, -3, -8, -1, -7]

# Use heapq.nlargest() to get the largest elements without popping
largest_elements = [-x for x in heapq.nsmallest(3, max_heap)]

print("Largest elements:", largest_elements)  # Output: Largest elements: [8, 7, 5]
```

### Change max heap into min heap
```python
import heapq

# Example of a max heap
max_heap = [-5, -3, -8, -1, -7]  # Elements are negated

# Negate all elements to convert the max heap into a min heap
min_heap = [-x for x in max_heap]

# Convert the list into a min heap in-place
heapq.heapify(min_heap)

print("Min-Heap after conversion:", min_heap)  # Output: Min-Heap after conversion: [-8, -7, -5, -3, -1]
```