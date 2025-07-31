# 1. [LeetCode Problem: Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/description/)

---

# 2. 🧩 Problem: Reflecting Bishop Movement

## 📝 Problem Statement

You are given a rectangular chessboard of size `m x n`. A bishop is placed at a given starting position `initPosition = [x, y]` and moves in a diagonal direction defined by `initDirection = [dx, dy]`.

- The bishop moves **one unit per step** in the given direction.
- If it hits a **boundary**, it **reflects** (i.e., the corresponding direction component `dx` or `dy` is flipped).
- The bishop continues moving for exactly `k` steps.

### Function Signature

```java
public int[] bishopFinalPosition(int[] boardSize, int[] initPosition, int[] initDirection, long k)
````

Your task is to **return the bishop's final position** after `k` moves.

---

## 📥 Input

* `boardSize`: An array `[m, n]` where `1 <= m, n <= 1000`
* `initPosition`: An array `[x, y]`, where `0 <= x < m` and `0 <= y < n`
* `initDirection`: An array `[dx, dy]`, where both `dx` and `dy` ∈ {-1, 1}
* `k`: A long integer (`1 <= k <= 10⁹`), representing the number of steps

---

## 📤 Output

* An array `[final_x, final_y]` representing the final position of the bishop after `k` moves.

---

## 💡 Reflection Rules

* If `x == 0` and `dx == -1`, flip `dx = 1`
* If `x == m - 1` and `dx == 1`, flip `dx = -1`
* If `y == 0` and `dy == -1`, flip `dy = 1`
* If `y == n - 1` and `dy == 1`, flip `dy = -1`

---

## ✅ Example

### Input

```java
boardSize = [3, 7]
initPosition = [1, 2]
initDirection = [-1, 1]
k = 13
```

### Output

```java
[1, 1]
```

---

## 🔁 Movement Trace (Partial)

1. Start at `[1, 2]`, move to `[0, 3]` → hits top → flip `dx`
2. Now direction `[1, 1]`, move to `[1, 4]`, `[2, 5]` → hits bottom → flip `dx`
3. Continue this pattern for 13 steps...

---

## 🧪 Sample Test Cases

### Test Case 1

```java
Input:
boardSize = [4, 4]
initPosition = [0, 0]
initDirection = [1, 1]
k = 5

Output:
[1, 1]
```

---

### Test Case 2

```java
Input:
boardSize = [3, 3]
initPosition = [1, 1]
initDirection = [1, 1]
k = 1000000000

Output:
[1, 1]
```

> 💡 **Insight:** The bishop moves in a predictable cycle due to the boundaries. If a cycle is detected, you can optimize by computing `k % cycleLength` and only simulating those steps.
