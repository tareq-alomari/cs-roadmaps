# 01 — هياكل البيانات والخوارزميات (DSA)

---

## لماذا DSA؟

أساس كل شيء في البرمجة — مطلوب في مقابلات FAANG وكل شركة تقنية كبرى.

---

## هياكل البيانات

### الأساسية

```
Array         - وصول عشوائي O(1)، إدراج O(n)
Linked List   - إدراج O(1)، بحث O(n)
Stack         - LIFO، push/pop O(1)
Queue         - FIFO، enqueue/dequeue O(1)
Hash Table    - بحث/إدراج O(1) متوسط
```

### المتوسطة

```
Tree          - Binary Tree, BST, AVL, Red-Black
Heap          - Min/Max Heap، أساس Priority Queue
Graph         - Directed/Undirected، Weighted
Trie          - للبحث في النصوص
```

### المتقدمة

```
Segment Tree  - Range queries O(log n)
Fenwick Tree  - Prefix sums O(log n)
Disjoint Set  - Union-Find
```

---

## الخوارزميات

### Sorting

```python
# الأهم معرفتها نظريًا وعمليًا
Bubble Sort    O(n²)   - للتعليم فقط
Merge Sort     O(n log n) - Stable, Divide & Conquer
Quick Sort     O(n log n) average - In-place
Heap Sort      O(n log n) - In-place, Not stable
Counting Sort  O(n+k)  - للأرقام الصغيرة
```

### Searching

```python
Linear Search  O(n)
Binary Search  O(log n)  ← احفظه وافهمه جيدًا

def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

### Graph Algorithms

```python
BFS  - أقصر مسار في الرسم البياني غير الموزون
DFS  - استكشاف، Topological Sort، Cycle Detection
Dijkstra    - أقصر مسار (أوزان موجبة)
Bellman-Ford - أقصر مسار (أوزان سالبة)
Floyd-Warshall - أقصر مسار بين جميع النقاط
Kruskal/Prim   - Minimum Spanning Tree
```

### Dynamic Programming

```
المفتاح: Overlapping Subproblems + Optimal Substructure

أنماط شائعة:
- Fibonacci / Climbing Stairs
- 0/1 Knapsack
- Longest Common Subsequence
- Longest Increasing Subsequence
- Coin Change
- Edit Distance
```

---

## Big O Notation

```
O(1)       < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!)

قواعد:
- Drop constants:    O(2n) → O(n)
- Drop lower terms:  O(n² + n) → O(n²)
- Space vs Time tradeoff
```

---

## خطة الدراسة (12 أسبوع)

```
الأسبوع 1-2:   Array, String, Hash Table
الأسبوع 3-4:   Stack, Queue, Linked List
الأسبوع 5-6:   Trees, BST, Recursion
الأسبوع 7-8:   Graphs (BFS, DFS)
الأسبوع 9-10:  Dynamic Programming
الأسبوع 11-12: Hard problems + Mock Interviews
```

---

## منصات التدريب

| المنصة | الاستخدام |
|--------|-----------|
| [LeetCode](https://leetcode.com) | مقابلات الشركات |
| [NeetCode](https://neetcode.io) | Roadmap منظم مع شرح |
| [Codeforces](https://codeforces.com) | Competitive Programming |
| [AlgoExpert](https://algoexpert.io) | مدفوع لكن ممتاز |

## ⏱️ الوقت المقدر: 3-6 أشهر

## مصادر
- كتاب: *Introduction to Algorithms* (CLRS)
- [NeetCode 150](https://neetcode.io/practice) ← أفضل قائمة للمقابلات
