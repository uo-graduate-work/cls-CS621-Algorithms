## 1. Divide–and–Conquer Paradigm

General pattern:

1. **Divide:** Split problem of size $n$ into $a$ subproblems, each of size $\approx n/b$.
    
2. **Conquer:** Solve each subproblem **recursively**.
    
3. **Combine:** Merge subproblem solutions in $f(n)$ time.
    

Typical recurrence (worst case):

- $$  
    T(n) =  
    \begin{cases}  
    O(1) & n = O(1) \  
    a,T(n/b) + f(n) & \text{otherwise}  
    \end{cases}  
    $$
    

---

## 2. Merge Sort & Recurrence Solving

### Merge Sort (review)

- **Divide:** Split array $A$ into two halves.
    
- **Conquer:** Recursively sort each half.
    
- **Combine:** MERGE two sorted arrays in $O(n)$.
    

```text
MERGESORT(A)
    if length(A) == 1
        return A
    m = ⌊length(A)/2⌋
    AL = MERGESORT(A[1..m])
    AR = MERGESORT(A[m+1..length(A)])
    return MERGE(AL, AR)
```

MERGE runs in $\Theta(n)$ where $n$ is total length of both halves.

### Merge Sort Recurrence

- Input size $n$:  
    $$  
    T(n) =  
    \begin{cases}  
    C_1 & n = 1 \  
    2T(n/2) + C_2 n & n > 1  
    \end{cases}  
    $$
    

Solve by:

- **Master Theorem:** $a=2, b=2, f(n)=\Theta(n)$ ⇒ $d=1$, $c=\log_b a = 1$ ⇒ $c=d$  
    → $T(n) = \Theta(n \log n)$.
    
- **Iteration/recursion-tree idea:** after $i$ levels:
    
    - $T(n) = 2^i T(n/2^i) + i C_2 n$
        
    - stop when $n/2^i = 1$ ⇒ $i = \log n$
        
    - $T(n) = n C_1 + (\log n), C_2 n = \Theta(n \log n)$.
        

---

## 3. Master Theorem (for $T(n) = aT(n/b) + f(n)$)
$$T(n) = aT(\frac{n}{b}) + cn^k$$$$T(1) = c$$
Assume:

- $a \ge 1$, $b > 1$
    
- $f(n) = \Theta(n^d)$ for some $d \ge 0$
    
- $c = \log_b a$.
    

Then:

1. **Case 1 (small recursion term):** if $c < d$  
    ⇒ $T(n) = \Theta(f(n)) = \Theta(n^d)$
    
2. **Case 2 (recursion dominates):** if $c > d$  
    ⇒ $T(n) = \Theta(n^c)$
    
3. **Case 3 (balanced):** if $c = d$  
    ⇒ $T(n) = \Theta(n^c \log n)$
    

> Note: there’s a mild regularity condition for Case 1 (roughly says $f$ doesn’t drop too fast); for usual polynomials it holds automatically.

### Examples (from slides + HW)

1. $T(n) = T(n/2) + 5n$
    
    - $a=1$, $b=2$, $f(n)=5n \Rightarrow d=1$
        
    - $c = \log_2 1 = 0 < d$
        
    - **Case 1:** $T(n) = \Theta(n)$.
        
2. $T(n) = 4T(n/2) + 2n$
    
    - $a=4$, $b=2$, $f(n)=2n \Rightarrow d=1$
        
    - $c = \log_2 4 = 2 > d$
        
    - **Case 2:** $T(n) = \Theta(n^2)$.
        
3. $T(n) = 7T(n/7) + n$ (HW1)
    
    - $a=7$, $b=7$, $f(n)=n \Rightarrow d=1$
        
    - $c = \log_7 7 = 1 = d$
        
    - **Case 3:** $T(n) = \Theta(n \log n)$.
        
4. **Not applicable:** $T(n) = T(n-5) + n$ or $T(n) = T(n-1) + n$.
    
    - Subproblem size is $n-5$ or $n-1$ (not $n/b$), so **Master theorem doesn’t apply**; use **iteration** instead.
        

---

## 4. Iteration (Expansion) Method

Idea: repeatedly expand the recurrence until you hit the base case, then sum.

### Example: Quick Sort Worst Case

Worst case partition: 0 on one side, $n-1$ on the other:

- $$  
    T(n) =  
    \begin{cases}  
    1 & n=1\  
    T(n-1) + n & n>1  
    \end{cases}  
    $$
    

Expand:

- $T(n) = T(n-1) + n$
    
- $= T(n-2) + (n-1) + n$
    
- $= T(n-3) + (n-2) + (n-1) + n$
    
- $\dots$
    
- $= T(1) + 2 + 3 + \dots + n = \Theta(n^2)$
    

### Example: $T(n)=T(n-1)+2$ (HW1)

- $T(n) = T(n-1) + 2$
    
- $= T(n-2) + 4$
    
- $= T(n-3) + 6$
    
- $= T(n-i) + 2i$
    

Stop when $n-i=1 ⇒ i=n-1$:

- $T(n) = T(1) + 2(n-1) = \Theta(n)$
    

---

## 5. Substitution (Induction) Method

Used to _prove_ a guessed asymptotic bound.

Steps:

1. **Guess** a bound, e.g. $T(n) \le c n$.
    
2. **Induction hypothesis:** assume $T(i) \le c i$ for all $i < n$.
    
3. Plug into recurrence and show $T(n) \le c n$ holds for large enough $n$ with some choice of $c$.
    

### Example: Linear-Time Selection (Select)

Recurrence from median-of-medians algorithm:

$$  
    T(n) \le  
    \begin{cases}  
    O(1) & n \le 25 \\ 
    T(\frac{7n}{10}) + T(\frac{n}{5}) + O(n) & n > 25  
    \end{cases}  
    $$
    

Assume $T(i) \le c i$ for all $i < n$:

- $T(n) \le c\cdot \tfrac{7n}{10} + c\cdot \tfrac{n}{5} + c' n$
    
- $= \tfrac{9}{10} c n + c' n = c n \left( \tfrac{9}{10} + \tfrac{c'}{c} \right)$
    

Choose $c$ large enough (e.g., $c \ge 10 c'$) so that  
$\tfrac{9}{10} + \tfrac{c'}{c} \le 1$ ⇒ $T(n) \le c n$.

Thus **$T(n) = O(n)$**.

---

## 6. Quick Sort

### Algorithm (review)

```text
QUICKSORT(A, begin, end)
    if begin < end
        q = PARTITION(A, begin, end)
        QUICKSORT(A, begin, q-1)
        QUICKSORT(A, q+1, end)
```

`PARTITION` chooses pivot (here, last element), rearranges array so:

- $A[begin..q-1] \le A[q] \le A[q+1..end]$.
    

Partition is $\Theta(n)$.

### Running Times

- **Worst case:** extremely unbalanced partitions (pivot = min or max):
    
    - Recurrence: $T(n) = T(n-1) + \Theta(n)$ ⇒ $\Theta(n^2)$.
        
- **Best case:** split evenly every time:
    
    - Recurrence: $T(n) = 2T(n/2) + \Theta(n)$ ⇒ $\Theta(n \log n)$.
        
- **Average case:** number of comparisons is $E[X] = \Theta(n \log n)$:
    
    - Count indicator random variables $X_{ij}$ for whether elements $z_i, z_j$ compare;  
        use linearity of expectation and some combinatorial bounds (harmonic series) to show $E[X]=O(n\log n)$.
        
    - So average running time is $\Theta(n \log n)$.
        

---

## 7. Selection Algorithms

### Problem

Given array $A[1..n]$ and integer $k$, find the **$k$-th smallest** element of $A$.

---

### 7.1 Randomized Quickselect

```text
RandSelect(A, k)
    if |A| == 1: return A[1]
    L, R = Partition(A) // pivot chosen randomly
    if k ≤ |L|:
        return RandSelect(L, k)
    else:
        return RandSelect(R, k - |L|)
```

- Using same partitioning idea as quicksort.
    

**Complexity:**

- Best case: balanced split ⇒ $O(n)$.
    
- Average case: expected linear time $O(n)$ (similar analysis to quicksort average).
    
- Worst case: bad splits every time ⇒ $O(n^2)$.
    

---

### 7.2 Deterministic Linear-Time Selection (Median-of-Medians)

Goal: **$O(n)$ worst-case**.

High-level idea:

1. Split array into groups of 5 consecutive elements.
    
2. Find the **median** of each group by brute force.
    
3. Recursively find the **median $M$ of these medians**.
    
4. Use $M$ as pivot: partition into
    
    - $L = {,x \le M,}$, $R = {,x > M,}$.
        
5. Recurse on $L$ or $R$ depending on where the $k$-th element lies.
    

Pseudocode:

```text
Select(A, k)
    if n ≤ 25:
        return k-th smallest in A by brute force
    m = ⌈n/5⌉
    create array C[1..m]
    for i = 1 to m:
        C[i] = median of A[(5i-4)..(5i)]  // last group may be smaller
    M = Select(C, m/2)  // median of medians
    partition A around M into L (≤ M) and R (> M)
    if k ≤ |L|:
        return Select(L, k)
    else:
        return Select(R, k - |L|)
```

**Key balance argument:**

- At least half of the medians in $C$ are $\le M$, so at least $n/10$ medians $\le M$.
    
- Each such median is $\ge$ two elements in its group ⇒ at least $3n/10$ elements $\le M$.
    
- Similarly, at least $3n/10$ elements $\ge M$.
    

So every recursion drops **at least $3n/10$ elements** ⇒ recurse on at most $7n/10$ elements.

Recurrence:

- $T(n) \le T(7n/10) + T(n/5) + O(n)$ ⇒ by substitution, **$T(n) = O(n)$**.
    

---

## 8. Closest Pair of Points (Plane)

### Problem

Given $S={p_1,\dots,p_n}$ in the plane, $p_i=(x_i,y_i)$, find pair $(p_i,p_j)$ minimizing Euclidean distance $|p_ip_j|$.

Naive: check all $\binom{n}{2}$ pairs ⇒ $\Theta(n^2)$.

### D&C Algorithm (high-level)

Preprocessing (one-time):

- Build:
    
    - $X$: points of $S$ sorted by x-coordinate.
        
    - $Y$: same points sorted by y-coordinate.  
        (cost $O(n\log n)$ once)
        

Recursive step on triple $(S, X, Y)$:

1. **Divide:**
    
    - Split $S$ into $S_L, S_R$ by vertical line $D$ through median x.
        
    - Construct $X_L, X_R$ (already given by split of $X$).
        
    - Build $Y_L, Y_R$ by scanning $Y$ once and placing each point depending on its x-coordinate ⇒ $O(n)$.
        
2. **Conquer:**
    
    - Recursively find closest pair in $S_L$ (distance $\delta_L$).
        
    - Recursively find closest pair in $S_R$ (distance $\delta_R$).
        
    - Let $\delta = \min(\delta_L,\delta_R)$.
        
3. **Combine (cross pairs):**
    
    - Only need pairs with one point left and one right **within distance $\delta$ of line $D$**.
        
    - Build array $Y_{\text{mid}}$: points within vertical strip $[D-\delta, D+\delta]$, sorted by y (by scanning $Y$).
        
    - For each point $p$ in $Y_{\text{mid}}$, compare to **at most next 7 points** in $Y_{\text{mid}}$:
        
        - geometric packing argument: in any $2\delta \times 2\delta$ square there are at most 8 points; due to previous $\delta$-separation from same side, at most 8 are possible, so 7 ahead in y-order suffice.
            
4. Return the minimum of:
    
    - best pair in $S_L$, best in $S_R$, and best cross pair from strip.
        

**Runtime:**

- Divide: $O(n)$
    
- Combine: $O(n)$
    
- Two recursive calls: $2T(n/2)$
    

Recurrence:

- $T(n) = 2T(n/2) + O(n)$ ⇒ **$T(n) = O(n \log n)$**.
    

---

## 9. Integer Multiplication (Bit Complexity)

### Problem

Multiply two $n$-bit numbers $x$ and $y$ (represented as bit arrays).

CPU integer multiply is constant-time only for _fixed_ bit size; for arbitrarily long integers we care about algorithmic complexity.

### Grade-School / Naive Algorithm

- For each bit of $y$, multiply $x$ by that bit, shift appropriately, and add.
    
- Binary version of decimal long multiplication.
    
- Time: $O(n^2)$.
    

---

### 9.1 Simple D&C (No Improvement)

Write:

- $x = x_1 \cdot 2^{n/2} + x_0$
    
- $y = y_1 \cdot 2^{n/2} + y_0$
    

Then:

- $xy = x_1y_1 \cdot 2^n + (x_1y_0 + x_0y_1) \cdot 2^{n/2} + x_0y_0$
    

Algorithm:

1. Recursively compute four $(n/2)$-bit products:
    
    - $x_1y_1$, $x_1y_0$, $x_0y_1$, $x_0y_0$.
        
2. Combine in $O(n)$ time.
    

Recurrence:

- $T(n) = 4T(n/2) + O(n)$ ⇒ by Master theorem, $a=4,b=2,d=1 ⇒ c=2>d$  
    ⇒ $T(n) = \Theta(n^2)$ (no asymptotic improvement).
    

---

### 9.2 Karatsuba’s Trick (3 Recursions)

Key idea: reduce to **three** recursive multiplications.

Recall:

- $xy = x_1y_1 \cdot 2^n + (x_1y_0 + x_0y_1)\cdot 2^{n/2} + x_0y_0$
    

Let:

- $p = (x_1 + x_0)(y_1 + y_0) = x_1y_1 + x_1y_0 + x_0y_1 + x_0y_0$  
    ⇒ $x_1y_0 + x_0y_1 = p - x_1y_1 - x_0y_0$.
    

So:

- We only need three $(n/2)$-bit multiplications:
    
    - $x_1y_1$, $x_0y_0$, and $p$.
        

Pseudocode:

```text
KaratsubaMultiply(x, y)
    write x = x1·2^{n/2} + x0
    write y = y1·2^{n/2} + y0
    p      = KaratsubaMultiply(x1 + x0, y1 + y0)
    x1y1   = KaratsubaMultiply(x1, y1)
    x0y0   = KaratsubaMultiply(x0, y0)
    mid    = p - x1y1 - x0y0
    return x1y1·2^n + mid·2^{n/2} + x0y0
```

Recurrence:

- $T(n) = 3T(n/2) + O(n)$
    
- $a=3, b=2, d=1 ⇒ c = \log_2 3 ≈ 1.585 > d$
    
- ⇒ $T(n) = \Theta(n^{\log_2 3}) \approx \Theta(n^{1.59})$.
    

---

## 10. Matrix Multiplication & Strassen

### Problem

Multiply two $n\times n$ matrices $A$ and $B$:

- $c_{ij} = \sum_{k=1}^n a_{ik} b_{kj}$.
    

---

### 10.1 Standard Algorithm

- Triple nested loop, $n^3$ scalar multiplications/additions.
    
- **Time:** $\Theta(n^3)$.
    

---

### 10.2 Simple D&C (No Gain)

Partition:

- $A, B, C$ into four $(n/2)\times(n/2)$ blocks:
    
    $$  
    A =  
    \begin{pmatrix}  
    A_{11} & A_{12}\  
    A_{21} & A_{22}  
    \end{pmatrix},  
    \quad  
    B =  
    \begin{pmatrix}  
    B_{11} & B_{12}\  
    B_{21} & B_{22}  
    \end{pmatrix}  
    $$
    

Then:

- $C_{11} = A_{11}B_{11} + A_{12}B_{21}$, etc.
    

This yields:

- **8** recursive multiplications + **4** additions of $(n/2)\times(n/2)$ matrices.
    
- Recurrence:
    
    - $T(n) = 8T(n/2) + O(n^2)$ ⇒ $\Theta(n^3)$.
        

---

### 10.3 Strassen’s Algorithm

Idea:

- Express the four $C_{ij}$ blocks via **7 cleverly chosen products** $P_1, \dots, P_7$ of $(n/2)\times(n/2)$ matrices, plus $O(1)$ additions/subtractions of submatrices.
    

Steps:

1. Compute 10 intermediate sums/differences $S_1,\dots,S_{10}$ from $A_{ij}$ and $B_{ij}$.
    
2. Compute 7 products:
    
    - $P_1 = A_{11} S_1$, $P_2 = S_2 B_{22}$, …, $P_7 = S_9 S_{10}$.
        
3. Recombine to get $C_{11}, C_{12}, C_{21}, C_{22}$ from $P_1,\dots,P_7$.
    

Resulting recurrence:

- $T(n) = 7T(n/2) + O(n^2)$
    
- $a=7$, $b=2$, $d=2 ⇒ c = \log_2 7 \approx 2.81 > d$
    
- ⇒ $T(n) = \Theta(n^{\log_2 7}) \approx \Theta(n^{2.81})$.
    

Main takeaway: **fewer recursive multiplies** ⇒ asymptotic speedup.

---

## 11. Extra Algorithms from HW1

### 11.1 Fast Exponentiation (Exponentiation by Squaring)

Goal: compute $x^n$ in **$O(\log n)$** multiplications.

Iterative version:

```text
Power(x, n)
    res  = 1
    base = x
    while n > 0:
        if n mod 2 == 1:
            res = res * base   // use current bit
        base = base * base     // square each loop
        n = ⌊n / 2⌋            // shift bits of n right
    return res
```

Invariants:

- At each iteration, `base = x^{2^k}` for some $k$.
    
- `res` accumulates product of those powers for which the corresponding bit in $n$ is 1.
    
- # of iterations = # bits in $n$ = $\lfloor \log_2 n \rfloor + 1$.
    

So:

- **Time:** $\Theta(\log n)$.
    
- This is also a classic **divide-and-conquer on the exponent**:
    
    - If $n$ even: $x^n = (x^{n/2})^2$.
        
    - If $n$ odd: $x^n = x \cdot (x^{(n-1)/2})^2$.
        

---

### 11.2 Majority Element in Linear Time

Problem: given array $A[1..n]$, decide if there exists value $p$ that appears **more than $n/2$ times**, and find it.

Boyer–Moore majority vote algorithm:

```text
Majority(A)
    candidate = null
    count = 0
    for i = 1 to n:
        if count == 0:
            candidate = A[i]
            count = 1
        else if A[i] == candidate:
            count += 1
        else:
            count -= 1

    // verification pass
    occ = 0
    for i = 1 to n:
        if A[i] == candidate:
            occ += 1
    return (occ > n/2, candidate)
```

Intuition:

- First pass “cancels out” different values in pairs.
    
- If a majority element $p$ exists (> $n/2$ occurrences), it **cannot be fully cancelled** and must be the final `candidate`.
    
- Second pass confirms whether `candidate` truly appears $> n/2$ times.
    

Complexity:

- 2 linear scans, constant extra memory:
    
    - **Time:** $\Theta(n)$
        
    - **Space:** $O(1)$.
        

---

## 12. Which Recurrence Method to Use?

- **Master theorem:**
    
    - Recurrence of form $T(n) = aT(n/b) + f(n)$, $a\ge1$, $b>1$, subproblem size is **n/b**.
        
    - $f(n)$ is polynomial-like.
        
    - Examples: merge sort, Karatsuba, Strassen, many D&C algorithms.
        
- **Iteration / Recursion tree:**
    
    - Good for:
        
        - Non-standard forms (e.g. $T(n)=T(n-1)+n$).
            
        - Getting intuition / leading terms.
            
    - Often combined with algebraic summations (arithmetic series, geometric series).
        
- **Substitution (induction) method:**
    
    - When Master theorem doesn’t apply or you only need a **proof** of a guessed bound (e.g., linear-time selection).
        
    - Typical pattern: assume $T(n)\le cn$ or $T(n)\le c n\log n$ and choose $c$ large enough.
        
