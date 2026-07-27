## 1. What Is Dynamic Programming?

- Despite the name, DP is **not** “just recursion” and not about “programming” in the coding sense (term dates to 1950s planning/scheduling).
    
- It’s a technique for problems with:
    
    - **Optimal substructure** – optimal solution can be built from optimal solutions of subproblems.
        
    - **Overlapping subproblems** – the same subproblems are solved many times by a naive recursive solution.
        
- Typical problems: **longest / shortest / minimum cost / maximum value** (LIS, shortest paths, knapsack, edit distance, matrix-chain, etc.).
    

### DP vs Divide & Conquer

- **Divide & Conquer**
    
    - Subproblems: substantially smaller (e.g., `n → n/2`, `n → 7n/10`).
        
    - Subproblems: usually **disjoint**.
        
    - Recursion tree: simple, no need to memoize.
        
- **Dynamic Programming**
    
    - Subproblems: often size `n-1`, `n-2`, … (subtract a constant).
        
    - Subproblems: heavily **overlap** → naive recursion recomputes work.
        
    - We **tabulate** results (bottom-up or memoized top-down) to avoid repetition (space–time tradeoff).
        

### Standard 4-Step DP Recipe

1. **Define OPT function**:
    
    - `OPT(subproblem parameters)` describes value of optimal solution to a subproblem.
        
2. **Write recurrence** for OPT (with base cases).
    
3. **Choose table + evaluation order**:
    
    - Identify dependencies (`OPT(i)` depends on smaller-index subproblems, etc.).
        
    - Fill table in an order where all dependencies are already known.
        
4. (**Optional**) **Reconstruct** an actual optimal solution using extra pointers (e.g., `PREV`, `choice` table).
    

Guidelines for choosing OPT:

- Subproblems should:
    
    - Include the original problem.
        
    - Have a clear size measure.
        
    - Let “bigger” subproblems depend on “smaller” ones.
        
- OPT’s parameters should encode enough info to make the recurrence simple (often we **force the last element** or **prefix length**, etc.).
    

---

## 2. Longest Increasing Subsequence (LIS)

### Problem

Given a sequence `A = ⟨a₁, …, aₙ⟩`, find an **increasing subsequence** of **maximum length**.

- Subsequence: `a_{s1}, a_{s2}, …, a_{sℓ}` with `1 ≤ s₁ < … < sℓ ≤ n`.
    
- Increasing: `a_{s1} < a_{s2} < … < a_{sℓ}`.
    

Example:  
`A = [5, 2, 8, 6, 3, 6, 9, 7]` → one LIS is `[2, 3, 6, 7]`.

### Subproblem & OPT Definition

Key idea: **focus on the last element** of the subsequence.

- Let `OPT(i)` = length of the longest increasing subsequence that **ends at position `i`** (and includes `a_i`).
    
- Overall LIS length = `max_i OPT(i)`.
    

### Recurrence

To build LIS ending at `a_i`, we need some earlier `a_j < a_i` placed just before `a_i`.

- If `i = 1`, only subsequence is `[a₁]`:  
    `OPT(1) = 1`.
    
- For `i > 1`:
    
    - Either `a_i` starts a subsequence alone → length `1`.
        
    - Or we append `a_i` to an LIS ending at some `j < i` with `a_j < a_i` → length `1 + OPT(j)`.
        

So:

[  
OPT(i) =  
\begin{cases}  
1, & i = 1 \  
\max\Big( {1} \cup {1 + OPT(j) \mid 1 \le j < i,\ a_j < a_i} \Big), & i > 1  
\end{cases}  
]

### Table Algorithm (O(n²))

We also track `PREV[i]` = index of previous element in LIS ending at `i`:

```text
LIS(A[1..n]):
    OPT[1..n], PREV[1..n]
    OPT[1] = 1
    PREV[1] = null
    for i = 2..n:
        OPT[i] = 1
        PREV[i] = null
        for j = 1..i-1:
            if A[j] < A[i] and OPT[j] + 1 > OPT[i]:
                OPT[i] = OPT[j] + 1
                PREV[i] = j
    L = max_i OPT[i]              // length
    // Reconstruction: follow PREV backwards from argmax_i OPT[i]
```

- Time: `O(n²)` (nested loops).
    
- Space: `O(n)`.
    

### Printing LIS

```text
PRINT-LIS(A, OPT, PREV):
    i = argmax_i OPT[i]
    seq = []
    while i != null:
        seq.append(A[i])
        i = PREV[i]
    reverse(seq)
    return seq
```

---

### HW3 Q1 – Example LIS Table

Array: `A = [7, 4, 6, 5, 8, 2, 10, 3]`.

From running the LIS DP:

- `OPT = [1, 1, 2, 2, 3, 1, 4, 2]`
    
- `PREV = [0, 0, 2, 2, 3, 0, 5, 6]` (0 = “no predecessor”).
    

Interpretation:

- LIS length is `4` at index `7` (`A[7]=10`).
    
- Backtracking: `7 → PREV[7]=5 → PREV[5]=3 → PREV[3]=2`.  
    Subsequence indices `[2,3,5,7]` → values `[4, 6, 8, 10]` (one LIS).
    

---

## 3. Text Segmentation (Word Break)

### Problem

Given string `s[1..n]` and a boolean subroutine `dict(w)` that checks whether string `w` is a valid word in constant time, determine whether `s` can be segmented into a sequence of valid words. And optionally recover such a segmentation.

Example:  
`s = "algorithmsisacomputersciencecourse"`  
→ `"algorithms is a computer science course"`.

### Why Not Greedy?

Greedy idea: scan left to right, always take the **longest prefix** that’s a word.

Counterexample:

- Dictionary: `{ "abc", "abcd", "ef" }`
    
- String: `"abcdef"`
    

Greedy takes `"abc"` first → leaves `"def"` (invalid), but `"abcd" + "ef"` is a valid segmentation.

### Subproblems & OPT Definition

Consider prefixes of `s`.

- `OPT(i)` = **True/False** whether prefix `s[1..i]` can be segmented into valid words.
    
- Base: `OPT(0) = True` (empty string is trivially “segmentable”).
    

### Recurrence

For `i > 0`:

- We try **all possible last word boundaries** at `j` (`0 ≤ j < i`):
    
    - If `OPT(j)` is True (prefix `s[1..j]` can be segmented),
        
    - **and** `dict(s[j+1 .. i])` is True,
        
    - then `OPT(i) = True`.
        

Formally:

[  
OPT(i) =  
\begin{cases}  
\text{True}, & i = 0 \  
\bigvee_{0 \le j < i}\big( OPT(j) \land dict(s[j+1..i]) \big), & i > 0  
\end{cases}  
]

### Algorithm (O(n²))

We also store `S[i]` = last split index before `i`:

```text
SEGMENT(s[1..n]):
    T[0..n], S[0..n]
    T[0] = True
    for i = 1..n:
        T[i] = False
        S[i] = -1
        for j = 0..i-1:
            if T[j] and dict(s[j+1..i]):
                T[i] = True
                S[i] = j
                break   // first valid split is enough
    return T[n], S
```

Reconstruction: starting from `i = n`, repeatedly cut at `S[i]` until 0.

- Time: `O(n²)` (substrings or indexes * j loops, assuming `dict` is O(1)).
    
- Space: `O(n)`.
    

---

## 4. 0–1 Knapsack

### Problem

We have:

- `n` items, item `i` with **weight** `w_i > 0` and **value** `v_i > 0`.
    
- Knapsack with capacity `W`.
    
- Must either **take** or **leave** each item (no fractions).
    
- Goal: maximize total value subject to total weight ≤ `W`.
    

Greedy by value / by density is **not optimal** in 0–1 knapsack (counterexample on slide).

### Subproblems & OPT Definition

Let `OPT(i, w)` = maximum value achievable using items `{1..i}` with capacity `w`.

- Original answer: `OPT(n, W)`.
    

### Recurrence

Consider item `i`:

1. **Exclude** item `i`:
    
    - Value: `OPT(i-1, w)`.
        
2. **Include** item `i` (only if `w_i ≤ w`):
    
    - We gain value `v_i` + best we can do on remaining capacity `w - w_i` with previous items:
        
    - Value: `v_i + OPT(i-1, w - w_i)`.
        

Base cases:

- If `i = 0` or `w = 0` → `OPT(i, w) = 0`.
    

So:

[  
OPT(i, w) =  
\begin{cases}  
0, & i = 0 \text{ or } w = 0 \  
OPT(i-1, w), & w_i > w \  
\max\big(OPT(i-1, w),\ v_i + OPT(i-1, w - w_i)\big), & w_i \le w  
\end{cases}  
]

### Table Algorithm (O(nW))

```text
KNAPSACK(w[1..n], v[1..n], W):
    OPT[0..n][0..W]
    for w = 0..W: OPT[0][w] = 0
    for i = 0..n: OPT[i][0] = 0

    for i = 1..n:
        for cap = 1..W:
            if w[i] > cap or OPT[i-1][cap] >= v[i] + OPT[i-1][cap - w[i]]:
                OPT[i][cap] = OPT[i-1][cap]
            else:
                OPT[i][cap] = v[i] + OPT[i-1][cap - w[i]]

    return OPT[n][W]
```

- `n+1` rows × `W+1` columns → `O(nW)` time and space.
    

Reconstruction: starting from `(i, w)=(n, W)`, decide for each `i` whether we took item `i` by comparing `OPT[i][w]` with `OPT[i-1][w]`.

---

## 5. Edit Distance (Levenshtein)

### Problem

Given two strings:

- `A[1..m]`
    
- `B[1..n]`
    

We want the minimum number of operations to transform `A` into `B` using:

1. **Insert** a letter.
    
2. **Delete** a letter.
    
3. **Substitute** one letter for another.
    

Example: `FOOD → MONEY`:

`FOOD → MOOD → MOND → MONED → MONEY` ⇒ distance = 4.

### Alignment View

- Think of inserting “blanks” into both strings to align them to same length.
    
- Each column yields:
    
    - letter vs blank → insertion / deletion.
        
    - different letters → substitution.
        
    - same letters → no cost.
        

### Subproblems & OPT Definition

Let:

- `Edit(i, j)` = edit distance between prefix `A[1..i]` and `B[1..j]`.
    

We then want `Edit(m, n)`.

### Recurrence – 3 Cases

1. **Delete last letter of A** (`A[i]` vs blank in B):
    
    - Cost: 1 (delete) + edit distance of remaining prefixes:  
        `Edit(i, j)` candidate = `Edit(i-1, j) + 1`.
        
2. **Insert last letter of B** (blank vs `B[j]`):
    
    - Cost: 1 (insert) + `Edit(i, j-1)`.
        
3. **Match/Substitute last letters** (`A[i]` vs `B[j]`):
    
    - If `A[i] == B[j]`: cost 0 + `Edit(i-1, j-1)`.
        
    - Else: cost 1 + `Edit(i-1, j-1)`.
        

Combine with a `min`.

Base cases:

- Transform empty string to `B[1..j]` by inserting `j` characters: `Edit(0, j) = j`.
    
- Transform `A[1..i]` to empty string by deleting `i` chars: `Edit(i, 0) = i`.
    

So:

$$
Edit(i, j) =  
\begin{cases}  
i, & j=0 \  
j, & i=0 \  
\min{, \  
\quad Edit(i-1, j) + 1,\quad \text{(delete)}\  
\quad Edit(i, j-1) + 1,\quad \text{(insert)}\  
\quad Edit(i-1, j-1) + diff(i,j)\ \text{(substitute/match)}\  
}, & i,j>0  
\end{cases}  
$$
where `diff(i,j) = 0` if `A[i]=B[j]`, else `1`.

### Table Algorithm (O(mn))

```text
EDIT-DISTANCE(A[1..m], B[1..n]):
    Edit[0..m][0..n]
    for j = 0..n: Edit[0][j] = j
    for i = 0..m: Edit[i][0] = i

    for i = 1..m:
        for j = 1..n:
            costSub = (A[i] == B[j]) ? 0 : 1
            Edit[i][j] = min(
                1 + Edit[i-1][j],      // delete
                1 + Edit[i][j-1],      // insert
                costSub + Edit[i-1][j-1]  // substitute/match
            )
    return Edit[m][n]
```

- Time: `O(mn)`.
    
- Space: `O(mn)` (can be optimized to `O(min(m,n))` with row-rolling).
    

---

## 6. Matrix-Chain Multiplication

### Problem

We have matrices `A₁, A₂, …, Aₙ`, where:

- `A_i` has dimension `p_{i-1} × p_i`.
    

We want the cheapest way (fewest scalar multiplications) to compute `A₁A₂…Aₙ` by choosing _parentheses_ (order of multiplications).

- Multiplying `p × q` by `q × r` costs `p·q·r`.
    

Brute force tries all parenthesizations – count grows like Catalan numbers, exponential in `n`.

### Subproblems & OPT Definition

Subproducts:

- For any `1 ≤ i ≤ j ≤ n`, consider chain `A_i A_{i+1} … A_j`.
    
- Let `OPT(i, j)` = minimum cost (scalar multiplications) to compute product `A_i … A_j`.
    
- We want `OPT(1, n)`.
    

### Recurrence

If `i = j`, just a single matrix → no multiplication needed:

- `OPT(i, i) = 0`.
    

For `i < j`, choose where to split the product:

- Pick `k` with `i ≤ k < j` and compute `(A_i … A_k) * (A_{k+1} … A_j)`.
    
- Cost:
    
    - `OPT(i, k)` (left chain)
        
    - `+ OPT(k+1, j)` (right chain)
        
    - `+ p_{i-1}·p_k·p_j` (multiply two resulting matrices).
        

We try all splits and take minimum:

[  
OPT(i, j) =  
\begin{cases}  
0, & i = j \  
\min_{i \le k < j}\ \big( OPT(i, k) + OPT(k+1, j) + p_{i-1} p_k p_j \big), & i < j  
\end{cases}  
]  

### Table Algorithm (O(n³))

We fill a 2D table `OPT[1..n][1..n]` in order of increasing chain length `L`.

```text
MATRIX-CHAIN-ORDER(p[0..n]):
    OPT[1..n][1..n]
    SPLIT[1..n][1..n]   // to reconstruct parens
    for i = 1..n:
        OPT[i][i] = 0
    for L = 2..n:                 // chain length
        for i = 1..(n-L+1):
            j = i + L - 1
            OPT[i][j] = +∞
            for k = i..j-1:
                cost = OPT[i][k] + OPT[k+1][j] + p[i-1]*p[k]*p[j]
                if cost < OPT[i][j]:
                    OPT[i][j] = cost
                    SPLIT[i][j] = k
    return OPT[1][n], SPLIT
```

- Time: `O(n³)` (triple loop).
    
- Space: `O(n²)`.
    

---

## 7. Coin Change (HW3 Q2 – Minimum Bills)

### Problem

Given:

- Target amount `n > 0`.
    
- Bill denominations `d₁ = 1, d₂, …, d_k` (all positive integers, unlimited supply).
    
- Want **minimum number of bills** to make exactly `n`.
    

### Subproblems & OPT Definition

Let `OPT(i)` = minimum number of bills needed to make amount `i` (for `0 ≤ i ≤ n`).

### Recurrence

- Base: `OPT(0) = 0`.
    
- For `i ≥ 1`:
    
    - Try using one bill of each denomination `d_j` such that `d_j ≤ i`:
        
        - Then the remaining amount is `i - d_j`.
            
        - Total bills = `1 + OPT(i - d_j)`.
            

So:

[  
OPT(i) = 1 + \min_{j: d_j \le i}\ OPT(i - d_j).  
]

If no `d_j ≤ i` (which cannot happen because `d₁=1`), we’d treat it as `+∞`.

### Algorithm (O(kn))

```text
MIN-BILLS(n, d[1..k]):
    OPT[0..n]
    OPT[0] = 0
    for i = 1..n:
        OPT[i] = +∞
        for j = 1..k:
            if d[j] ≤ i:
                OPT[i] = min(OPT[i], 1 + OPT[i - d[j]])
    return OPT[n]
```

- Time: `O(k n)`.
    
- Space: `O(n)`.
    

---

## 8. Multiplicative Subarray DP (HW3 Q3)

### Problem

Array of distinct positive numbers `A = [a₁,…,aₙ]`. Want a **subset** `C` (order from `A`) of maximum size such that for **every pair** `x,y ∈ C`, either `x = k·y` or `y = k·x` for some integer `k`. Equivalently:

- For all pairs, the larger is an integer multiple of the smaller.
    

We only need the **maximum size**, not the actual subset.

### Preprocessing

Sort the array in **increasing order**:

- Create `B = sort(A)` so `B[1] < B[2] < … < B[n]`.
    
- Now, if `B[i]` and `B[j]` are in the same subset with `i>j`, we must have `B[i]` being multiple of `B[j]`:
    
    - `B[i] % B[j] == 0`.
        

### Subproblems & OPT Definition

Let `OPT(i)` = size of the largest valid subset `C` that **ends at index `i` in B`** (i.e., maximum size of subset of` {B[1..i]}`that includes`B[i]` and satisfies the multiplicative property).

### Recurrence

- Base: `OPT(1) = 1` (subset `{B[1]}`).
    
- For `i > 1`:
    
    - `B[i]` can join a chain ending at `j` (`j < i`) only if `B[i]` is integer multiple of `B[j]`:
        
        - `B[i] % B[j] == 0`.
            
    - Then `OPT(i)` candidate = `1 + OPT(j)`.
        
    - If there is no such `j`, we can always take `{B[i]}` alone → `OPT(i) = 1`.
        

So:

[  
OPT(i) =  
\begin{cases}  
1, & i=1 \  
1 + \max{OPT(j) \mid 1 \le j < i,\ B[i] \bmod B[j] = 0}, & \text{if any such } j \  
1, & \text{otherwise}  
\end{cases}  
]

Final answer = `max_i OPT(i)` (the longest chain can end anywhere).

### Algorithm (O(n²))

```text
MAX-MULTIPLICATIVE-SUBSET(A[1..n]):
    B = sort(A)             // ascending
    OPT[1..n]
    OPT[1] = 1
    for i = 2..n:
        OPT[i] = 1
        for j = 1..i-1:
            if B[i] % B[j] == 0:
                OPT[i] = max(OPT[i], 1 + OPT[j])
    ans = 0
    for i = 1..n:
        ans = max(ans, OPT[i])
    return ans
```

- Time: `O(n²)` in worst case.
    

---

## 9. Meta-Takeaways for DP

When you see a problem like in these slides/HW:

1. **Ask: do subproblems overlap?**
    
    - E.g., naive Fibonacci, edit-distance recursion, etc., recompute same states many times.
        
2. **Find a good state (OPT):**
    
    - Often includes an index or prefix length.
        
    - Sometimes includes additional parameter(s): capacity (`w` in knapsack), both indices (`i,j` in edit distance / matrix-chain), etc.
        
3. **Derive recurrence by “last choice” or “first choice”:**
    
    - “What is the last item we took?” (knapsack, LIS).
        
    - “Where’s the last split?” (text segmentation, matrix-chain).
        
    - “How do the last characters align?” (edit distance).
        
4. **Make sure optimal substructure holds**:
    
    - Cut-and-paste / exchange arguments to ensure that using optimal subsolutions is valid.
        
5. **Decide evaluation order**:
    
    - Usually increasing size of prefix, index, chain-length, or capacity.
        
