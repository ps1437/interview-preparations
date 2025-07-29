# Shortest Path from 'S' to 'X' in a Grid 
You are given a 2D character grid representing a maze-like playground. Each cell in the grid contains one of the following characters:

- `'S'`: Starting point (always at position `(0, 0)`)
- `'X'`: Target point (can be anywhere in the grid)
- `'1'`: A cell that can be traversed
- `'0'`: A wall (cannot be traversed)

You can move **up, down, left, or right** (no diagonal moves) from a cell, but only to a cell that is either `'1'` or `'X'`.

Your task is to:

> **Implement a function using recursion (DFS) to find the minimum number of steps required to reach `'X'` from `'S'`.**  
> Return `-1` if `'X'` is not reachable.

---

## Function Signature

```java
public static int shortestPath(char[][] grid)
