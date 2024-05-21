# Data Structures 

1. **Arrays**
   - Basic operations (Add, Delete,Search,Update)
   - Array manipulation techniques
   - 2D Array

2. **Linked Lists**
   - Singly Linked Lists
   - Doubly Linked Lists
   - Circular Linked Lists

3. **Stacks and Queues**
   - Stack operations (push, pop, peek)
   - Queue operations (enqueue, dequeue, peek)

4. **Trees**
   - Binary Trees
   - Binary Search Trees (BST)
   - Tree traversals (Inorder, Preorder, Postorder)
   - Balanced Trees (AVL, Red-Black Trees)
   - Segment Trees
   - AVL Trees
   - B-Trees

5. **Graphs**
   - Graph representations (Adjacency Matrix, Adjacency List)
   - Graph traversal algorithms (DFS, BFS)
   - Shortest path algorithms (Dijkstra's, Bellman-Ford)

6. **Hash Tables**
   - Hash Map operations (insertion, deletion, lookup)
   - Collision handling techniques (chaining, open addressing)

7. **Heaps**
   - Binary Heaps
   - Priority Queue operations

8. **Tries**
   - Trie structure
   - Trie operations (insertion, deletion, search)

9. **Disjoint Set Union (Union-Find)**
   - Union and Find operations
   - Path compression and union by rank

10. **Graph Algorithms**
    - Minimum Spanning Tree (Prim's, Kruskal's)
    - Topological Sorting
    - Network Flow algorithms
   
```md
# Complexity Chart for Data Structures

| Data Structure       | Access   | Search   | Insertion | Deletion | Space Complexity |
|----------------------|----------|----------|-----------|----------|------------------|
| Array                | O(1)     | O(n)     | O(n)      | O(n)     | O(n)             |
| Linked List          | O(n)     | O(n)     | O(1)      | O(1)     | O(n)             |
| Doubly Linked List   | O(n)     | O(n)     | O(1)      | O(1)     | O(n)             |
| Stack                | O(n)     | O(n)     | O(1)      | O(1)     | O(n)             |
| Queue                | O(n)     | O(n)     | O(1)      | O(1)     | O(n)             |
| Binary Tree          | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
| Binary Search Tree   | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
| AVL Tree             | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
| Hash Table           | N/A      | O(1)     | O(1)      | O(1)     | O(n)             |
| Heap (Binary Heap)   | O(1)     | O(log n) | O(log n)  | O(log n) | O(n)             |
| Trie                 | O(m)     | O(m)     | O(m)      | O(m)     | O(n)             |
| Disjoint Set Union   | N/A      | O(log n) | O(log n)  | O(log n) | O(n)             |
| Segment Tree         | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
| Fenwick Tree         | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
| B-Tree               | O(log n) | O(log n) | O(log n)  | O(log n) | O(n)             |
```

Note: 
- "Access" refers to accessing an element by index or key.
- "Search" refers to searching for an element based on a key or value.
- "Insertion" refers to adding a new element to the data structure.
- "Deletion" refers to removing an element from the data structure.
- "Space Complexity" represents the space required by the data structure in memory.

# Algorithms for DSA

## Sorting Algorithms
1. **Bubble Sort**
2. **Selection Sort**
3. **Insertion Sort**
4. **Merge Sort** *
5. **Quick Sort** *
6. **Counting Sort** *
7. **Radix Sort**
8. **Bucket Sort**

## Searching Algorithms
1. **Linear Search**
2. **Binary Search** *
3. **Ternary Search**

## Dynamic Programming
1. **Knapsack Problem** *
2. **Longest Increasing Subsequence** *
3. **Longest Common Subsequence**
4. **Coin Change Problem** *
5. **Fibonacci Sequence (Memoization & Tabulation)**
6. **Matrix Chain Multiplication** *

# Algorithms for Maximum Subarray Problems
1. **Kadane's Algorithm** *
2. **Divide and Conquer Maximum Subarray Algorithm** *
3. **Prefix Sum Array** *
4. **Dynamic Programming Maximum Subarray Algorithm** *
5. **Sliding Window Maximum Subarray Algorithm** *


## Greedy Algorithms
1. **Activity Selection**
2. **Huffman Coding** *
3. **Dijkstra’s Algorithm** *
4. **Prim’s Algorithm** *
5. **Kruskal’s Algorithm**

## Backtracking
1. **N-Queens Problem** *
2. **Sudoku Solver** *
3. **Permutations and Combinations** *
4. **Subset Sum Problem**
5. **Word Search**

## Divide and Conquer
1. **Merge Sort** *
2. **Quick Sort** *
3. **Binary Search** *
4. **Closest Pair of Points**

## Bit Manipulation
1. **Basic Operations (AND, OR, XOR, NOT, shifts)**
2. **Checking Power of Two**
3. **Counting Set Bits** *
4. **Bitwise Sieve of Eratosthenes** *

## Graph Algorithms
1. **Depth-First Search (DFS)** *
2. **Breadth-First Search (BFS)** *
3. **Dijkstra’s Algorithm** *
4. **Bellman-Ford Algorithm**
5. **Floyd-Warshall Algorithm**
6. **Prim’s Minimum Spanning Tree** *
7. **Kruskal’s Minimum Spanning Tree** *
8. **Topological Sorting** *
9. **A* Search Algorithm**
    

## Mathematical Algorithms
1. **Greatest Common Divisor (Euclidean Algorithm)** *
2. **Sieve of Eratosthenes** *
3. **Modular Exponentiation**
4. **Prime Factorization**
5. **Permutation and Combination Calculations** *

## String Algorithms
1. **String Matching** (KMP, Rabin-Karp, Boyer-Moore) *
2. **Rabin-Karp Algorithm** *
3. **Z Algorithm**
4. **Aho-Corasick Algorithm**
5. **Manacher's Algorithm for Longest Palindromic Substring** *


## Two-pointer Techniques
1. **Two Sum Problem**
2. **Three Sum Problem**
3. **Container With Most Water** *

## Sliding Window
1. **Maximum Sum Subarray of Size K**
2. **Longest Substring Without Repeating Characters**
3. **Minimum Window Substring**

## Union-Find
1. **Disjoint Set Union (Union-Find)**
2. **Path Compression**
3. **Union by Rank**

## Miscellaneous
1. **Reservoir Sampling**
2. **Fisher-Yates Shuffle**
3. **Top-K Queries** (Priority Queues, Heaps) *
4. **Fenwick Trees (Binary Indexed Trees)**
5. **Tortoise and Hare (Cycle Detection)** *
