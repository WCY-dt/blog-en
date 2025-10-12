---
layout: post
title:  "How is git diff implemented?"
date:   2025-04-04 03:00:00 +0800
categories: algorithms
tags: diff myers
summary: "Want to implement a git diff-like functionality? This article introduces two common algorithms: DP and Myers algorithm, and compares their performance"
comments: true
copyrights: 原创
mathjax: true
---

How is `git diff` implemented?

Let's say we have two files $$A$$ and $$B$$ with $$N=7$$ and $$M=6$$ lines respectively:

```plaintext
A
B
C
A
B
B
A
```

```plaintext
C
B
A
B
A
C
```

We can visualize these two files as an $$N \times M$$ grid:

<img src="/assets/post/images/diff1.webp" alt="grid" style="width:min(350px,100%)">

Starting from coordinate $$(x, y)$$, we can:

- Move right to $$(x+1, y)$$, representing deletion of line $$x+1$$ from the first file
- Move down to $$(x, y+1)$$, representing addition of line $$y+1$$ from the second file  
- Move diagonally to $$(x+1, y+1)$$, representing that line $$x+1$$ in the first file matches line $$y+1$$ in the second file

The third type of move is called a "snake," and it doesn't increase the path length.

Our goal is to move from $$(0, 0)$$ to $$(N, M)$$ while minimizing the total number of *right moves and down moves* in the path.

This problem can be solved using the minimum edit distance (Levenshtein distance) between two sequences.

## DP

You may have already noticed that the simplest solution to this problem is dynamic programming:

$$
dp[i][j] = \begin{cases}
\min\left(dp[i-1][j], dp[i][j-1]\right) + 1 & A[i] \neq B[j] \\
dp[i-1][j-1] & A[i] = B[j]
\end{cases}
$$

With boundary conditions:

$$
\begin{aligned}
dp[0][0] &= 0 \\
dp[i][0] &= i \\
dp[0][j] &= j
\end{aligned}
$$

<img src="/assets/post/images/diff2.webp" alt="dp" style="width:min(350px,100%)">

Here's the Python implementation:

```python
def diff(A: list[str], B: list[str]) -> tuple[int, list[tuple[str, str, int | None, int | None]]]:
    """
    Calculate the minimum edit distance (Levenshtein distance) between two sequences and return the detailed edit path.
    
    Args:
        A: Source sequence
        B: Target sequence
    
    Returns:
        tuple: A tuple containing two elements
            - int: Minimum edit distance (minimum number of operations required)
            - list: Edit path, each element is a 4-tuple (operation, element_value, A_index, B_index)
    
    Edit operations:
        '=': Elements are the same, no modification needed
        '-': Delete operation, exists in A but not in B
        '+': Insert operation, exists in B but not in A
    """
    N, M = len(A), len(B)
    dp = [[0] * (M + 1) for _ in range(N + 1)]
    for i in range(1, N + 1):
        dp[i][0] = i
    for j in range(1, M + 1):
        dp[0][j] = j
    for i in range(1, N + 1):
        for j in range(1, M + 1):
            if A[i - 1] == B[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                if dp[i - 1][j] < dp[i][j - 1]:
                    dp[i][j] = dp[i - 1][j] + 1
                else:
                    dp[i][j] = dp[i][j - 1] + 1
    i, j = N, M
    path = []
    while i > 0 or j > 0:
        if i > 0 and j > 0 and A[i - 1] == B[j - 1]:
            path.append(('=', A[i - 1], i - 1, j - 1))
            i -= 1
            j -= 1
        elif j > 0 and (i == 0 or dp[i][j - 1] <= dp[i - 1][j]):
            path.append(('+', B[j - 1], None, j - 1))
            j -= 1
        else:
            path.append(('-', A[i - 1], i - 1, None))
            i -= 1
    path.reverse()
    return dp[N][M], path
```

Since there may be multiple optimal paths, the backtracking process also requires careful consideration. For example, here we choose to prioritize right moves (deleting lines) over down moves (adding lines). This affects the final diff result but doesn't change the minimum edit distance.

> If you need the opposite strategy, you can change `dp[i][j - 1] <= dp[i - 1][j]` to `dp[i][j - 1] < dp[i - 1][j]`

Here's the execution result:

{% result title="DP diff" %}
```python
A = ["A", "B", "C", "A", "B", "B", "A"]
B = ["C", "B", "A", "B", "A", "C"]
distance, path = diff(A, B)
print("Minimum edit distance:", distance)
print("diff:")
for op, val, i, j in path:
    if op == '=':
        print(f"  {val}")
    elif op == '-':
        print(f"- {val}")
    elif op == '+':
        print(f"+ {val}")
```

```plaintext
Minimum edit distance: 5
diff:
- A
- B
  C
- A
  B
+ A
  B
  A
+ C
```
{% endresult %}

Great! However, the above algorithm has both time complexity and space complexity of $$O(NM)$$. Can we do better?

## Myers

The Myers algorithm provides a more efficient solution with time complexity $$O\left(N\log N + D^2\right)$$ and space complexity $$O(N)$$, where $$D$$ is the minimum edit distance (i.e., the total number of *right moves and down moves* in the path).

We define $$k$$ as the difference between *right moves and down moves* in the path, i.e., $$k = x - y$$. It can be proven that the $$D$$ of the shortest path satisfies the following inequality:

$$D \geq |N - M|$$

We can create the following $$k-D$$ coordinate system:

<img src="/assets/post/images/diff3.webp" alt="myers" style="width:min(500px,100%)">

We can find the shortest path by gradually increasing the value of $$D$$. For each fixed $$D$$, we can calculate all possible $$k$$ values, which range from $$-D \leq k \leq D$$.

We use an array $$V$$ to record the maximum $$x$$ value corresponding to each $$k$$.

Initially, $$V[1] = 0$$, indicating that when $$D = 0$$, the maximum $$x$$ value corresponding to $$k = 0$$ is 0.

We choose the approach that maximizes $$x$$, as this allows us to perform as many snake moves as possible. We have:

- When $$k = -D$$, it means we can no longer delete, so we can only move down one step from $$(x, y-1)$$, therefore

  $$
  x = V[k+1]
  $$

- When $$k = D$$, it means we can no longer add, so we can only move right one step from $$(x-1, y)$$, therefore

  $$
  x = V[k-1] + 1
  $$

- When $$-D < k < D$$, we can choose the approach that gives the larger $$x$$, therefore

  $$
  x = \text{max}(V[k-1] + 1, V[k+1])
  $$

Combining these, we get:

```python
if k == -D or (k != D and V.get(k - 1) < V.get(k + 1)):
    x = V.get(k + 1)
else:
    x = V.get(k - 1) + 1
```

With $$x$$, we can calculate $$y$$:

$$
y = x - k
$$

Then we can perform snake moves until $$A[x] \neq B[y]$$:

```python
while x < N and y < M and A[x] == B[y]:
    x += 1
    y += 1
```

We continuously increase the value of $$D$$ until $$V[N - M]$$ reaches $$N$$, indicating that we have found the shortest path from $$(0, 0)$$ to $$(N, M)$$.

Here's a diagram illustrating the Myers algorithm:

<img src="/assets/post/images/diff4.webp" alt="myers2" style="width:min(500px,100%)">

<img src="/assets/post/images/diff5.webp" alt="myers3" style="width:min(350px,100%)">

Here's the Python implementation:

```python
def myers_diff(A: list[str], B: list[str]) -> tuple[int, list[tuple[str, str, int | None, int | None]]]:
    """
    Use Myers algorithm to calculate the minimum edit distance (Levenshtein distance) between two sequences and return the detailed edit path.
    """
    N, M = len(A), len(B)
    if N == 0:
        return M, [('+', B[j], None, j) for j in range(M)]
    if M == 0:
        return N, [('-', A[i], i, None) for i in range(N)]
    
    max_d = N + M
    V = {1: 0}
    trace = [V.copy()]
    
    for D in range(max_d + 1):
        new_V = {}
        for k in range(-D, D + 1, 2):
            if k == -D or (k != D and V.get(k - 1, -1) < V.get(k + 1, -1)):
                x = V.get(k + 1, 0)
            else:
                x = V.get(k - 1, 0) + 1
            y = x - k
            
            while x < N and y < M and A[x] == B[y]:
                x += 1
                y += 1
            
            new_V[k] = x
            
            if x >= N and y >= M:
                path = []
                x, y = N, M
                for d in range(D, -1, -1):
                    k = x - y
                    V_prev = trace[d]
                    
                    if k == -d or (k != d and V_prev.get(k - 1, -1) < V_prev.get(k + 1, -1)):
                        prev_k = k + 1
                    else:
                        prev_k = k - 1
                    
                    prev_x = V_prev.get(prev_k, 0)
                    prev_y = prev_x - prev_k
                    
                    while x > prev_x and y > prev_y:
                        path.append(('=', A[x - 1], x - 1, y - 1))
                        x -= 1
                        y -= 1
                    
                    if d > 0:
                        if x == prev_x:
                            path.append(('+', B[y - 1], None, y - 1))
                            y -= 1
                        else:
                            path.append(('-', A[x - 1], x - 1, None))
                            x -= 1
                
                path.reverse()
                return D, path
        
        V = new_V
        trace.append(V.copy())
    
    return max_d, []
```

Here's the execution result:

{% result title="Myers diff" %}
```python
A = ["A", "B", "C", "A", "B", "B", "A"]
B = ["C", "B", "A", "B", "A", "C"]
distance, path = myers_diff(A, B)
print("Minimum edit distance:", distance)
print("diff:")
for op, val, i, j in path:
    if op == '=':
        print(f"  {val}")
    elif op == '-':
        print(f"- {val}")
    elif op == '+':
        print(f"+ {val}")
```

```plaintext
Minimum edit distance: 5
diff:
- A
- B
  C
- A
  B
+ A
  B
  A
+ C
```
{% endresult %}

## Discussion

### Performance Comparison

The Myers algorithm has time complexity $$O\left(N\log N + D^2\right)$$ and space complexity $$O(N)$$. In the worst case, $$D$$ can be close to $$N + M$$, causing the time complexity to degrade to $$O\left({(N + M)}^2\right)$$, which can even be worse than DP. We can write code to test the performance difference between the two algorithms under different conditions:

{% result title="Benchmark" hide=code %}
```python
import random
import time
import matplotlib.pyplot as plt
import numpy as np

def generate_test_case(N: int, M: int, similarity: float) -> tuple[list[str], list[str]]:
    base = [chr(i) for i in range(65, 91)]  # A-Z
    A = [random.choice(base) for _ in range(N)]
    B = []
    for i in range(M):
        if random.random() < similarity and i < N:
            B.append(A[i])
        else:
            B.append(random.choice(base))
    return A, B

def benchmark():
    test_cases = []
    for size in [100, 200, 300, 400, 500, 600]:
        for similarity in [0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1]:
            test_cases.append((size, size, similarity))
    
    runs_per_test = 100
    results = []
    
    for N, M, sim in test_cases:
        dp_times = []
        myers_times = []
        
        for run in range(runs_per_test):
            A, B = generate_test_case(N, M, sim)
            
            start = time.time()
            dp_distance, _ = diff(A, B)
            dp_time = time.time() - start
            dp_times.append(dp_time)
            
            start = time.time()
            myers_distance, _ = myers_diff(A, B)
            myers_time = time.time() - start
            myers_times.append(myers_time)
            
            assert dp_distance == myers_distance, "Results inconsistent"
        
        dp_time_avg = np.mean(dp_times)
        dp_time_std = np.std(dp_times)
        myers_time_avg = np.mean(myers_times)
        myers_time_std = np.std(myers_times)
        
        results.append((N, M, sim, dp_time_avg, myers_time_avg))
        print(f"N={N}, M={M}, similarity={sim:.1f} | "
              f"DP: {dp_time_avg:.4f}s (±{dp_time_std:.4f}) | "
              f"Myers: {myers_time_avg:.4f}s (±{myers_time_std:.4f})")
    
    plot_results(results)

def plot_results(results):
    sizes = sorted(set([r[0] for r in results]))
    similarities = sorted(set([r[2] for r in results]), reverse=True)
    
    fig = plt.figure(figsize=(12, 18))
    
    ax1 = plt.subplot(3, 2, 1)
    dp_by_size = {}
    myers_by_size = {}
    for size in sizes:
        size_results = [r for r in results if r[0] == size]
        dp_by_size[size] = np.mean([r[3] for r in size_results])
        myers_by_size[size] = np.mean([r[4] for r in size_results])
    
    x_pos = range(len(sizes))
    width = 0.35
    ax1.bar([x - width/2 for x in x_pos], [dp_by_size[s] for s in sizes], width, label='DP', alpha=0.8)
    ax1.bar([x + width/2 for x in x_pos], [myers_by_size[s] for s in sizes], width, label='Myers', alpha=0.8)
    ax1.set_xlabel('Sequence Length')
    ax1.set_ylabel('Average Time (s)')
    ax1.set_title('Performance by Sequence Length')
    ax1.set_xticks(x_pos)
    ax1.set_xticklabels(sizes)
    ax1.legend()
    ax1.grid(True, alpha=0.3)
    
    ax2 = plt.subplot(3, 2, 2)
    dp_by_sim = {}
    myers_by_sim = {}
    for sim in similarities:
        sim_results = [r for r in results if abs(r[2] - sim) < 0.01]
        dp_by_sim[sim] = np.mean([r[3] for r in sim_results])
        myers_by_sim[sim] = np.mean([r[4] for r in sim_results])
    
    x_pos = range(len(similarities))
    ax2.bar([x - width/2 for x in x_pos], [dp_by_sim[s] for s in similarities], width, label='DP', alpha=0.8)
    ax2.bar([x + width/2 for x in x_pos], [myers_by_sim[s] for s in similarities], width, label='Myers', alpha=0.8)
    ax2.set_xlabel('Similarity')
    ax2.set_ylabel('Average Time (s)')
    ax2.set_title('Performance by Similarity')
    ax2.set_xticks(x_pos)
    ax2.set_xticklabels([f'{s:.1f}' for s in similarities])
    ax2.legend()
    ax2.grid(True, alpha=0.3)
    
    # Add remaining plot code here (truncated for brevity)
    plt.tight_layout()
    plt.savefig('diff_algorithm_benchmark.png', dpi=300, bbox_inches='tight')
    plt.show()

benchmark()
```

```plaintext
N=100, M=100, similarity=0.9 | DP: 0.0008s (±0.0001) | Myers: 0.0001s (±0.0000)
N=100, M=100, similarity=0.8 | DP: 0.0008s (±0.0001) | Myers: 0.0002s (±0.0001)
N=100, M=100, similarity=0.7 | DP: 0.0008s (±0.0000) | Myers: 0.0003s (±0.0001)
N=100, M=100, similarity=0.6 | DP: 0.0008s (±0.0000) | Myers: 0.0006s (±0.0001)
N=100, M=100, similarity=0.5 | DP: 0.0008s (±0.0001) | Myers: 0.0008s (±0.0002)
N=100, M=100, similarity=0.4 | DP: 0.0008s (±0.0000) | Myers: 0.0011s (±0.0002)
N=100, M=100, similarity=0.3 | DP: 0.0008s (±0.0000) | Myers: 0.0013s (±0.0002)
N=100, M=100, similarity=0.2 | DP: 0.0008s (±0.0000) | Myers: 0.0015s (±0.0001)
N=100, M=100, similarity=0.1 | DP: 0.0008s (±0.0000) | Myers: 0.0017s (±0.0001)
N=200, M=200, similarity=0.9 | DP: 0.0031s (±0.0001) | Myers: 0.0002s (±0.0001)
N=200, M=200, similarity=0.8 | DP: 0.0031s (±0.0001) | Myers: 0.0006s (±0.0002)
N=200, M=200, similarity=0.7 | DP: 0.0031s (±0.0000) | Myers: 0.0013s (±0.0003)
N=200, M=200, similarity=0.6 | DP: 0.0032s (±0.0001) | Myers: 0.0021s (±0.0004)
N=200, M=200, similarity=0.5 | DP: 0.0032s (±0.0002) | Myers: 0.0032s (±0.0004)
N=200, M=200, similarity=0.4 | DP: 0.0032s (±0.0001) | Myers: 0.0042s (±0.0005)
N=200, M=200, similarity=0.3 | DP: 0.0032s (±0.0003) | Myers: 0.0052s (±0.0004)
N=200, M=200, similarity=0.2 | DP: 0.0032s (±0.0003) | Myers: 0.0060s (±0.0004)
N=200, M=200, similarity=0.1 | DP: 0.0032s (±0.0003) | Myers: 0.0066s (±0.0003)
N=300, M=300, similarity=0.9 | DP: 0.0074s (±0.0002) | Myers: 0.0004s (±0.0001)
N=300, M=300, similarity=0.8 | DP: 0.0075s (±0.0005) | Myers: 0.0013s (±0.0003)
N=300, M=300, similarity=0.7 | DP: 0.0076s (±0.0003) | Myers: 0.0028s (±0.0006)
N=300, M=300, similarity=0.6 | DP: 0.0077s (±0.0003) | Myers: 0.0045s (±0.0006)
N=300, M=300, similarity=0.5 | DP: 0.0078s (±0.0003) | Myers: 0.0072s (±0.0011)
N=300, M=300, similarity=0.4 | DP: 0.0078s (±0.0003) | Myers: 0.0097s (±0.0013)
N=300, M=300, similarity=0.3 | DP: 0.0079s (±0.0004) | Myers: 0.0119s (±0.0010)
N=300, M=300, similarity=0.2 | DP: 0.0080s (±0.0004) | Myers: 0.0141s (±0.0010)
N=300, M=300, similarity=0.1 | DP: 0.0079s (±0.0004) | Myers: 0.0156s (±0.0007)
N=400, M=400, similarity=0.9 | DP: 0.0140s (±0.0007) | Myers: 0.0007s (±0.0002)
N=400, M=400, similarity=0.8 | DP: 0.0141s (±0.0004) | Myers: 0.0024s (±0.0005)
N=400, M=400, similarity=0.7 | DP: 0.0145s (±0.0008) | Myers: 0.0050s (±0.0007)
N=400, M=400, similarity=0.6 | DP: 0.0147s (±0.0006) | Myers: 0.0085s (±0.0012)
N=400, M=400, similarity=0.5 | DP: 0.0152s (±0.0011) | Myers: 0.0127s (±0.0015)
N=400, M=400, similarity=0.4 | DP: 0.0151s (±0.0007) | Myers: 0.0176s (±0.0016)
N=400, M=400, similarity=0.3 | DP: 0.0150s (±0.0008) | Myers: 0.0221s (±0.0018)
N=400, M=400, similarity=0.2 | DP: 0.0149s (±0.0006) | Myers: 0.0258s (±0.0014)
N=400, M=400, similarity=0.1 | DP: 0.0150s (±0.0012) | Myers: 0.0283s (±0.0010)
N=500, M=500, similarity=0.9 | DP: 0.0227s (±0.0009) | Myers: 0.0010s (±0.0002)
N=500, M=500, similarity=0.8 | DP: 0.0231s (±0.0008) | Myers: 0.0036s (±0.0007)
N=500, M=500, similarity=0.7 | DP: 0.0234s (±0.0007) | Myers: 0.0077s (±0.0011)
N=500, M=500, similarity=0.6 | DP: 0.0239s (±0.0007) | Myers: 0.0131s (±0.0016)
N=500, M=500, similarity=0.5 | DP: 0.0243s (±0.0009) | Myers: 0.0206s (±0.0020)
N=500, M=500, similarity=0.4 | DP: 0.0244s (±0.0012) | Myers: 0.0283s (±0.0021)
N=500, M=500, similarity=0.3 | DP: 0.0244s (±0.0010) | Myers: 0.0354s (±0.0024)
N=500, M=500, similarity=0.2 | DP: 0.0243s (±0.0010) | Myers: 0.0417s (±0.0027)
N=500, M=500, similarity=0.1 | DP: 0.0243s (±0.0007) | Myers: 0.0452s (±0.0020)
N=600, M=600, similarity=0.9 | DP: 0.0337s (±0.0015) | Myers: 0.0015s (±0.0003)
N=600, M=600, similarity=0.8 | DP: 0.0342s (±0.0015) | Myers: 0.0053s (±0.0010)
N=600, M=600, similarity=0.7 | DP: 0.0348s (±0.0014) | Myers: 0.0109s (±0.0015)
N=600, M=600, similarity=0.6 | DP: 0.0360s (±0.0017) | Myers: 0.0198s (±0.0020)
N=600, M=600, similarity=0.5 | DP: 0.0360s (±0.0010) | Myers: 0.0302s (±0.0028)
N=600, M=600, similarity=0.4 | DP: 0.0364s (±0.0020) | Myers: 0.0407s (±0.0034)
N=600, M=600, similarity=0.3 | DP: 0.0359s (±0.0012) | Myers: 0.0513s (±0.0031)
N=600, M=600, similarity=0.2 | DP: 0.0363s (±0.0016) | Myers: 0.0614s (±0.0040)
N=600, M=600, similarity=0.1 | DP: 0.0362s (±0.0014) | Myers: 0.0666s (±0.0033)
```
{% endresult %}

![Benchmark Results](/assets/post/images/diff6.svg)

As we can see, when two sequences are very similar, the Myers algorithm significantly outperforms the DP algorithm. However, when sequences have large differences, the Myers algorithm's performance degrades and may become worse than DP. Overall, the Myers algorithm still outperforms the DP algorithm in most cases.

### Readability Optimization

Consider the following two diff results:

```diff
for (int i = 0; i < n; i++) {
    process1(i);
}
+for (int i = 0; i < n; i++) {
+    process2(i);
+}
```

```diff
for (int i = 0; i < n; i++) {
    process1(i);
+}
+for (int i = 0; i < n; i++) {
+    process2(i);
}
```

And this pair:

```diff
if (isSocketReady()) {
-    sendDataPart1();
-    sendDataPart2();
+    sendDataPartA();
+    sendDataPartB();
}
```

```diff
if (isSocketReady()) {
-    sendDataPart1();
+    sendDataPartA();
-    sendDataPart2();
+    sendDataPartB();
}
```

They achieve equivalent results, but in both comparisons, the first diff result is much more readable.

The Myers algorithm doesn't consider this aspect, so we can perform some post-processing on top of the Myers algorithm to optimize diff results.

A simple approach is to merge consecutive delete and add operations into blocks, then try to adjust at block boundaries to reduce unnecessary delete and add operations.

Here's the optimized code implementation:

```python
def optimize_diff(path: list[tuple[str, str, int | None, int | None]]) -> list[tuple[str, str, int | None, int | None]]:
    optimized_path = []
    i = 0
    while i < len(path):
        if path[i][0] == '-':
            j = i
            while j + 1 < len(path) and path[j + 1][0] == '-':
                j += 1
            del_block = path[i:j + 1]
            i = j + 1
            if i < len(path) and path[i][0] == '+':
                add_block = path[i]
                optimized_path.append((del_block, add_block))
                i += 1
        else:
            optimized_path.append(path[i])
            i += 1
    return optimized_path
```
