## 1. Why Do We Analyze Run Time?

* **Goal:** Compare algorithms by how their running time grows with *input size*.
* **Complications:**

  * Different inputs of the same size can take different time.
  * Different implementations / machines / compilers change raw running time.
  * Hardware effects: caching, parallelism, hyper-threading, etc.
* **Solution:**
  Ignore machine-dependent constants; study **asymptotic growth** of running time as a function of input size $n$.

---

## 2. Time Complexity & Input Size

### Time Complexity Function

* Let $T : \mathbb{N} \to \mathbb{R}$ be the **time complexity function**.
* $T(n)$ = **maximum** running time over all inputs of size $n$.

  * This is **worst-case time complexity**.
* Variants:

  * Replace “maximum” by “average” → **average-case complexity**.
  * Replace “running time” by “memory” → **space/memory complexity**.

### What Is “Input Size”?

Depends on the problem:

* Most algorithms: $n$ = number of items (e.g., length of array).
* Graph algorithms: often two parameters

  * $n$ = number of vertices, $m$ = number of edges.
* Bit-complexity problems (e.g., integer multiplication):

  * Size = number of **bits** needed to represent inputs.

---

## 3. Example: Insertion Sort

### Pseudocode

```text
for i = 2 to n do
    key ← A[i]
    j ← i - 1
    while (j > 0) and (A[j] > key) do
        A[j + 1] ← A[j]
        j ← j - 1
    end
    A[j + 1] ← key
end
```

* **Loop invariant:** before each iteration $i$, the prefix $A[1..i-1]$ is sorted.
* At step $i$, insert $A[i]$ into the sorted prefix in the correct position.

### Number of Iterations

* **Best case (already sorted):**
  Inner `while` runs 0 times; only outer loop runs → **$n-1$ iterations**.
* **Worst case (reverse sorted):**
  Inner loop shifts $i-1$ elements for each $i$:
  $$
  1 + 2 + \dots + (n-1) = \frac{n(n-1)}{2} = \frac{1}{2}n^2 - \frac{1}{2}n
  $$
* So insertion sort does **$\Theta(n^2)$** work in the worst case.

### “Full-detail” Asymptotic Analysis Idea

* Let each line take constant time $c_i$.
* Sum up:

  * Lines 1,2,3,8: run $n-1$ times.
  * In worst case, line 4 runs $i$ times; lines 5,6 run $i-1$ times, for each $i$.
* You get something like:
  $$
  T(n) = \alpha n^2 + \beta n + \gamma \in \Theta(n^2)
  $$
* In practice, we usually just say:

  * Inner loop runs at most $\sim n^2$ times → **$T(n) \in O(n^2)$**.
  * There exists an input (reverse sorted) where $\Omega(n^2)$ work is needed.
  * Hence **$T(n) \in \Theta(n^2)$**.

---

## 4. Asymptotic Notation

We compare functions $f,g : \mathbb{N} \to \mathbb{R}$ that are positive for large $n$.

### Big-O: Upper Bound

* **Definition:** $f(n) \in O(g(n))$ if
  $\exists c > 0, n_0 \in \mathbb{N}$ s.t.
  $$
  f(n) \le c \cdot g(n), \quad \forall n \ge n_0
  $$
* Interpretation: **eventually**, $f$ is at most a constant multiple of $g$.
* Example: $10n \in O(n)$ with $c = 20$, any $n_0 \ge 1$.

### Big-Omega: Lower Bound

* **Definition:** $f(n) \in \Omega(g(n))$ if
  $\exists c > 0, n_0$ s.t.
  $$
  f(n) \ge c \cdot g(n), \quad \forall n \ge n_0
  $$
* Interpretation: eventually, $f$ is at least a constant multiple of $g$.
* Relation: $f \in \Omega(g)$ **iff** $g \in O(f)$.

### Big-Theta: Tight Bound

* **Definition:** $f(n) \in \Theta(g(n))$ if both:

  * $f(n) \in O(g(n))$ **and** $f(n) \in \Omega(g(n))$.
* Equivalently: $f$ and $g$ grow at the same rate up to constant factors.
* Symmetry: $f \in \Theta(g)$ iff $g \in \Theta(f)$.

### Examples

* $n \in O(n^2)$.
* $n \log n \notin O(n)$.
* $2n + 5 \in O(n)$.
* $\tfrac{1}{2}n^2 + 2n + 10 \in \Theta(n^2)$.
* $\log_{100} n \in \Theta(\log n)$.
* $n^{100} + 2n^{90} + n^{70} + n^2 + 1 \in \Theta(n^{100})$.
* $\log(n!) \in \Theta(n \log n)$ (via Stirling’s approximation).

---

## 5. Thinking of Runtimes as Sets

* We sometimes write:

  * $f(n) \in O(g(n))$ or $f(n) = O(g(n))$.
* View $O(g)$ as the **set** of functions asymptotically bounded above by $g$.

---

## 6. Properties of Asymptotic Notation

### Transitivity

* If $f = O(g)$ and $g = O(h)$, then $f = O(h)$.
* If $f = \Omega(g)$ and $g = \Omega(h)$, then $f = \Omega(h)$.
* If $f = \Theta(g)$ and $g = \Theta(h)$, then $f = \Theta(h)$.

### Additivity

If $f, g$ have the same asymptotic class as $h$:

* $f = O(h)$ and $g = O(h) \Rightarrow f + g = O(h)$.
* Similarly for $\Omega$ and $\Theta$.

---

## 7. Using Limits to Compare Growth

Let
$$
L = \lim_{n\to\infty} \frac{f(n)}{g(n)}.
$$

* If $L = 0$:

  * $f \in O(g)$ and $g \notin O(f)$.
* If $L = \infty$:

  * $f \in \Omega(g)$ and $g \notin \Omega(f)$.
* If $0 < L < \infty$:

  * $f \in \Theta(g)$.

### L’Hôpital’s Rule (Calculus Tool)

If $\lim_{n\to a} f(n)$ and $\lim_{n\to a} g(n)$ are both $0$ or $\infty$, then
$$
\lim_{n\to a} \frac{f(n)}{g(n)} = \lim_{n\to a} \frac{f'(n)}{g'(n)}
$$
(if the derivative-based limit exists).

Example:
$$
\lim_{n\to\infty} \frac{n}{e^n}
= \lim_{n\to\infty} \frac{1}{e^n} = 0
\Rightarrow n \in O(e^n).
$$

---

## 8. Common Asymptotic Families & Their Relations

### Polynomials

For $a_d > 0$,
$$
a_d n^d + a_{d-1} n^{d-1} + \dots + a_1 n + a_0 \in \Theta(n^d).
$$

### Logarithms

* Change of base:
  $$
  \log_a n = \Theta(\log_b n), \quad a,b>1.
  $$
* For any $a > 0$:
  $$
  \log n \in O(n^a).
  $$

### Exponentials

* For any $r > 1$ and $d > 0$:
  $$
  n^d \in O(r^n).
  $$

### Overall Hierarchy (slow → fast)

Roughly:

$$
1 \prec \log n \prec n^\epsilon \prec n \prec n \log n \prec n^2 \prec n^3 \prec \dots \prec 2^n \prec n! \prec n^n
$$

### Example Ordering

Order these by asymptotic growth (slowest to fastest):

1. $10100$ (constant)
2. $4 \lg n - 1$
3. $5n - 3$ and $10n - 2$ (same $\Theta(n)$)
4. $2n(\lg n)^2 + 3n$ ($\Theta(n(\log n)^2)$)
5. $3n^2 - 3n + 1$ ($\Theta(n^2)$)
6. $2n^3 - 5n$ ($\Theta(n^3)$)
7. $2^n + n + 1$ ($\Theta(2^n)$)
8. $n!$
9. $n^n - 2$

---

## 9. Naming Common Running Times

* **Linear:** $O(n)$
* **“$n \log n$” time:** $O(n \log n)$
* **Quadratic:** $O(n^2)$
* **Cubic:** $O(n^3)$
* **Polynomial:** $O(n^k)$ for some fixed $k > 0$
* **Exponential:** $O(a^n)$ for $a > 1$

Graphs comparing $x$, $x \log x$, $x^2$, $2^x$ show that **exponential** functions quickly dominate polynomials, which in turn dominate logarithms.

A typical table (assuming ~$10^6$ operations/sec) shows polynomial algorithms stay feasible for larger $n$, while exponential ones blow up extremely fast.

---

## 10. “Efficient” (Polynomial-Time) Algorithms

* **Definition:** An algorithm is *efficient* if
  $$
  T(n) \in O(n^k)
  $$
  for some constant integer $k$.
  These are **polynomial-time algorithms**.
* Is $O(n \log n)$ polynomial time?
  Yes — $n \log n \in O(n^2)$, for example, so it’s bounded by a polynomial.

**Intuition:**

* Most designed polynomial-time algorithms have reasonably small exponents and constants.
* Escaping from brute-force exponential time usually requires exploiting deeper structure.

**Caveats:**

* Some polynomial-time algorithms have huge constants / exponents and are useless in practice.
* Some exponential-time algorithms (e.g., simplex, some search tools like `grep`) perform well on typical instances.

---

## 11. Fibonacci: Exponential vs Linear-Time Algorithms

### The Fibonacci Sequence

Defined by:
$$
F_0 = 0,\quad F_1 = 1,\quad F_n = F_{n-1} + F_{n-2}\ \text{for } n>1.
$$

Sequence:
$0, 1, 1, 2, 3, 5, 8, 13, 21, 34, \dots$

### Naive Recursive Algorithm

```text
FIBONACCI(n)
    if n == 0
        return 0
    elseif n == 1
        return 1
    else
        return FIBONACCI(n - 1) + FIBONACCI(n - 2)
```

Let $T(n)$ = # of basic steps to compute `FIBONACCI(n)`.

* For some constants:

  * $T(0) = 2$, $T(1) = 3$.
* For $n > 1$:
  $$
  T(n) = T(n-1) + T(n-2) + 3.
  $$
* Since $F_n$ satisfies the same homogeneous part,
  $$
  T(n) \ge F_n.
  $$
* And using the inequality
  $$
  F_n \ge 2^{n/2} = (\sqrt{2})^n \approx (1.4)^n,
  $$
  we see $T(n)$ grows **exponentially**:
  $$
  T(n) \in \Omega\big((\sqrt{2})^n\big)
  $$
  → Exponential-time algorithm.

### SMARTFIBONACCI: Linear-Time Algorithm

Idea: compute $F_n$ iteratively using only the previous two values.

```text
SMARTFIBONACCI(n)
    if n == 0
        return 0
    elseif n == 1
        return 1
    else
        pprev = 0
        prev  = 1
        for i = 2 to n
            f     = prev + pprev
            pprev = prev
            prev  = f
        return f
```

* The loop runs $n-1$ times and each iteration does $O(1)$ work:
  $$
  T(n) = 6 + 6(n-1) = 6n \in \Theta(n).
  $$
* Thus **SMARTFIBONACCI is linear-time**, much better than exponential.

---

## 12. How to Do a Quick Complexity Argument

1. **Choose a “basic operation”**
   (e.g., comparison, assignment, recursive call).
2. **Count how many times it can execute** as a function of input size $n$.
3. **Write a rough bound** (often an inequality):

   * e.g., “Inner loop runs at most $n-1$ times for each of the $n-1$ iterations.”
4. **Drop constants and lower-order terms**:

   * If $T(n) = 5n^2 + 3n + 7$, then $T(n) \in \Theta(n^2)$.
5. For tight bounds:

   * Give **upper bound (O)** and **lower bound ($\Omega$)** to justify **$\Theta$**.
